## [Handling YARP images in Python](https://robotology.github.io/robotology-documentation/doc/html/icub_python_imaging.html)
Here the ``yarpImage`` tutorial will be discussed. It is a simple tutorial which recieves the image from the camera and transfers the same image to the output port. 

Previous tutorials used C++ for port definitions. In this tutorial Python have been used and we will dive deep into it :wink: .

## The Python Code
1. ### Import the libraries
    ``numpy`` to work with arrays and ``yarp``.
```
import numpy as np
import yarp
```
2. ### Initialize YARP Network. 
   On some operating systems, there are certain start-up tasks that need to be performed. It is good to call this method near the start of your program.
```
yarp.Network.init()
```
3. ### Run Only The Main
   To allow or prevent parts of code from being run when the modules are imported. 
   It ensures that variables that are created, functions that are called, operations that are done, etc only when you directly run file, not when you import it into another.
```
if __name__ == '__main__':
```
4. ### Ports
   An input port is typically block buffered by default, which means that on any read, the buffer is filled with immediately-available bytes to speed up future reads. Buffered port is used when you want to send and receive messages in the background without having to stop your processing.

   Follow 3 steps for each port:
    - Define the kind of port you are using
    - Name the port
    - Open the port
```
    # Input and Output image size
    image_w = 640
    image_h = 480

    # Input port
    in_port = yarp.BufferedPortImageRgb()
    in_port_name = '/image:i'
    in_port.open(in_port_name)

    # Output port
    out_port = yarp.Port()
    out_port_name = '/image:o'
    out_port.open(out_port_name)
```
5. ###  Preparing Input Image Buffer
   The key element of image processing in Python is creating a YARP image object that wraps around a data structure native to Python, which then can be conveniently manipulated using Python-specific means. Buffers are set up in every program to hold data coming in and going out. For input image buffering follow the steps:
    - Create a ``numpy`` array of ones in the size of the input image.
    - Create the YARP image
    - Using ``set.External`` method, wrap the YARP image around the created array.
  
  (do it only once)
```
    in_buf_array = np.ones((image_h, image_w, 3), dtype = np.uint8)
    in_buf_image = yarp.ImageRgb()
    in_buf_image.resize(image_w, image_h)
    in_buf_image.setExternal(in_buf_array.data, in_buf_array.shape[1], in_buf_array.shape[0])
```
6. ### Preparing Output Image Buffer
   - Here with the `` out_buf_image.setExternal`` it is demonstrating the portion of the memory that contains the image, which actually is the `` yarp.ImageRgb()``.
   
     (do it only once)
```
    out_buf_image = yarp.ImageRgb()
    out_buf_image.resize(image_w, image_h)
    out_buf_array = np.zeros((image_h, image_w, 3), dtype = np.uint8)
    out_buf_image.setExternal(out_buf_array.data, out_buf_array.shape[1], out_buf_array.shape[0])
```
7. ### Receiving/Sending Image
   Here the code is in a while loop. 
   - It reads the data from the port
   - If it receives images prints the "Image received!" message. Otherwise it prints "No image received!".
   - Copies the received image.
   - With ``assert`` it is checking if the received image is in the right memory loaction.
   - Prints the shape of the image.
   - Sends the same image to the output.
   - Finally closes the ports.
```
    while True: 
        print ('Receiving image from input port...')
        received_image = in_port.read()
        if received_image:
            print("Image received!")
            in_buf_image.copy(received_image)
            
            # Checking the received image
            assert in_buf_array.__array_interface__['data'][0] == in_buf_image.getRawImage().__int__()
            
            ##################################
            # You can process image here
            
            print ('Shape of image is:')
            frame = in_buf_array
            print (frame.shape)
            
            ##################################
            # Sending output image
            print ('Sending output image...')
            out_buf_array[:,:] = frame
            out_port.write(out_buf_image)
        else:
            print("No image received!")

    print ('Closing ports...')
    in_port.close()
    out_port.close()
```

