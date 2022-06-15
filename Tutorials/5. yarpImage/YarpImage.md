Here the ``yarpImage`` tutorial will be discussed. It is a simple tutorial which recieves the image from the camera and transfers the same image to the output port. 

Previous tutorials used C++ for port definitions. In this tutorial Python have been used and we will dive deep into it :wink: .

## The Python Code
```
import numpy as np
import yarp

# Initialise YARP Network
yarp.Network.init()

if __name__ == '__main__':

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

    # Preparing input image buffer (do it only once)
    in_buf_array = np.ones((image_h, image_w, 3), dtype = np.uint8)
    in_buf_image = yarp.ImageRgb()
    in_buf_image.resize(image_w, image_h)
    in_buf_image.setExternal(in_buf_array.data, in_buf_array.shape[1], in_buf_array.shape[0])

    # Preparing output image buffer (do it only once)
    out_buf_image = yarp.ImageRgb()
    out_buf_image.resize(image_w, image_h)
    out_buf_array = np.zeros((image_h, image_w, 3), dtype = np.uint8)
    out_buf_image.setExternal(out_buf_array.data, out_buf_array.shape[1], out_buf_array.shape[0])

    while True:
        # Receiving input image
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

