# Handling YARP images in Python
This is to explain how YARP and Python interact when images are involved. The source of this explanation is from [iCub-main](https://robotology.github.io/robotology-documentation/doc/html/icub_python_imaging.html).

## Conversion between YARP image types and Python types (and back)
The key to obtain high performance in any image processing software is to avoid copying the data back and forth whenever possible. Ideally, having received an image through a YARP port, we would modify it in place and then pass the very same image to the output port. To facilitate this, `yarp::sig::Image` class (Base class for storing images) implements a method called `setExternal()` which allows an Image object to "wrap around" an already existing data buffer. 

- NOTE
  
   In computer science, a data buffer is a region of a memory used to temporarily store data while it is being moved from one place to another. Typically, the data is stored in a buffer as it is retrieved from an input device or just before it is sent to an output device.


Therefore the key element of image processing in Python is creating a YARP image object that wraps around a data structure native to Python, which then can be then conveniently manipulated using Python-specific means.

Due to its popularity and mature stage of development, in this tutorial we consider NumPy arrays as the substrate for image processing within Python. However, the concepts presented here may be applied to other Python frameworks as well (e.g. PIL, PyQt), provided certain requirements discussed below are fulfilled.

## Sending data from Python to YARP
If you're using Python 3.x and the data structure you want to receive the image data into supports the Python buffer protocol (which is the case for NumPy arrays), all you have to do is to call the setExternal method of an YARP image object, specifying your python object as its first argument.

- What is the Python Buffer Protocol?
  
  The Python buffer protocol, also known in the community as `PEP 3118`, is a framework in which Python objects can expose raw byte arrays to other Python objects. This can be extremely useful for scientific computing, where we often use packages such as NumPy to efficiently store and manipulate large arrays of data. Using the buffer protocol, we can let multiple objects efficiently manipulate views of the same data buffers, without having to make copies of the often large datasets.

  The ability for Python to natively share data with the compiled libraries is incredibly important for scientific computing. This is one big reason that NumPy and its predecessors were initially developed, and it's why the buffer protocol was later proposed and added to Python's standard library. The buffer protocol is extremely useful for what scientists do with Python: build intuitive interfaces to compiled legacy code.

  Fortunately, most scientists can simply use the buffer protocol via tools like NumPy without knowing too much of the details of what's behind it. Because the buffer protocol is fundamentally a C-API entity, implementing new functionality with it generally requires digging into the guts of Python's C API. 

### Example 
A NumPy array with random contents is created, a YARP image is wrapped around it and sent to a YARP port. It is assumed that there is an instance of yarpview running with associated port name
```
import numpy
import yarp
 
# Initialise YARP
yarp.Network.init()
 
# Create the array with random data
img_array = numpy.random.uniform(0., 255., (240, 320)).astype(numpy.float32)
 
# Create the yarp image and wrap it around the array  
yarp_image = yarp.ImageFloat()
#setExternal(data, imgWidth, imgHeight)
yarp_image.setExternal(img_array, img_array.shape[1], img_array.shape[0])
 
# Create the yarp port, connect it to the running instance of yarpview and send the image
output_port = yarp.Port()
output_port.open("/python-image-port")
yarp.Network.connect("/python-image-port", "/view01")
output_port.write(yarp_image)
 
# Cleanup
output_port.close()
```

- Crucial Notes
  
  1. NumPy array shape, data type and value range must be compatible with the type of YARP image to be wrapped around it. Below is the list of YARP image types exposed by YARP bindings and corresponding requirements for the underlying NumPy array:
       -  `yarp.ImageMono` - array data type: uint8, shape: (image-height, image-width), values range: 0 to 255
       -  `yarp.ImageFloat` - array data type: float32, shape: (image-height, image-width), values range: 0. to 255. (Note: this is different from 0. to 1. range, assumed by default e.g. by scipy.ndimage routines!)
       -  `yarp.ImageRgb` - array data type: uint8, shape: (image-height, image-width, 3), values range: 0 to 255
       -  `yarp.ImageRgbFloat` - array data type: float32, shape: (image-height, image-width, 3), values range: 0. to 255.
       -  `yarp.ImageRgba` - array data type: uint8, shape: (image-height, image-width, 4), values range: 0 to 255
    
    In the example above the result of calling numpy.random.uniform() had to be explicitly cast to float32, since by default it returned float64. This may very well be architecture-dependent, so it's best to specify the array data type explicitly (tip: most NumPy array creation routines accept the dtype parameter).

2. NumPy array being wrapped around must be contiguous in the memory. This is usually the case for newly created arrays, but if the array you want to send is a result of some computation process (especially one involving operations like transposing, slicing, etc.), make sure to call numpy.ascontiguousarray() before passing the array to setExternal().
3. Image dimensions specified in setExternal call must match actual dimensions of the array. Using the array's shape attribute as in the example above is the most fool-proof way to go, but note the order in which the array dimensions are specified in this attribute. Rows (corresponding to image height) come first, then columns (image width).
4. Underlying array must outlive the Image object wrapping around it. Make sure the array exists as long as the image object is being used. It may be a good practice to delete the image object explicitly (tip: Python del statement) as soon as it is no longer needed.
## Receiving data from YARP to Python
The idea is pretty much the same as when sending an image to the port. In order to minimize the number of copy operations, we wrap the YARP image around a NumPy array before reading an image from the port. The example below assumes that iCub simulator is running and that the world camera is available at port "/icubSim/cam"
```
import numpy
import yarp
import matplotlib.pylab
 
# Initialise YARP
yarp.Network.init()
 
# Create a port and connect it to the iCub simulator virtual camera
input_port = yarp.Port()
input_port.open("/python-image-port")
yarp.Network.connect("/icubSim/cam", "/python-image-port")
 
# Create numpy array to receive the image and the YARP image wrapped around it
img_array = numpy.zeros((240, 320, 3), dtype=numpy.uint8)
yarp_image = yarp.ImageRgb()
yarp_image.resize(320, 240)
yarp_image.setExternal(img_array, img_array.shape[1], img_array.shape[0])
 
# Read the data from the port into the image
input_port.read(yarp_image)
 
# display the image that has been read
matplotlib.pylab.imshow(img_array)
 
# Cleanup
input_port.close()
```
The method above minimizes the number of performed copy operations however it comes with a serious limitation: the exact size and type of the incoming image must be known beforehand. If the image coming through the port does not match the dimensions, pixel type or quantum of the prepared yarp_image, the latter will be automatically re-allocated when read() is called and association with img_array will be covertly lost. Note that before calling setExternal() we call resize() on the yarp_image. This is required because just calling setExternal will not set internal variables of the image class properly (namely the pixel size and the quantum), and the yarp_image's buffer will also be re-allocated by read().

If the size and type of the incoming image is not known or when greater flexibility is required, we won't get away without copying the image, at least when a frame with new size or format is retrieved. One must detect such situation, adjust the receiving array accordingly and copy the image contents.

As an alternative to using the bare yarp.Port class, one can also use one of the specialized instantiations of the of the BufferedPort template exposed by the YARP bindings, e.g. BufferedPortImageRgb. The read() method of this class returns a yarp image of an appropriate type, which then must be copied to another yarp image, associated with a NumPy array. Although this introduces one additional copy operation, this facilitates adjusting the receiving NumPy array to potentially changing image size, as discussed in the previous paragraph.