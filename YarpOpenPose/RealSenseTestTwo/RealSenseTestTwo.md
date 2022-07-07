## RealSense Camera - Test 2

With this tutorial I will try to change different parts of the `yarpopenpose.ini` file. The goal is to dive deep and understand better :)

Get the detailed information related to the parameters from the OpenPose's [GitHub](https://github.com/CMU-Perceptual-Computing-Lab/openpose/blob/master/include/openpose/flags.hpp).

### First Error
As usual the skeleton view did not work at first :| 
No worries, I solved it. The reason was that last time, since we were using the eGPU we had set the `num_gpu` equal to 2 and also the `num_gpu_start` equal to 0 in the `yarpopenpose.ini` file. However this time I was not using the eGPU and it was causing the error. Therefore, I changed them back to 1 and 0 respectively. And it worked :))

This is the original results you get.
![No Change](Images/NoChange.jpeg)

## Parameters
Apply the changes to the parameters in the  `yarpopenpose.ini` file.

- name 

    This is the name of the emodule you are using. `yarpOpenPose`

- model_name 

    It specifies the model to be used. The preffered model is `BODY_25` but there are different models with different features.

    - `BODY_25`

        fastest for CUDA version, most accurate, and includes foot keypoint
    - `COCO`

        18 Keypoints

    - `MPI`

        15 keypoints, least accurate model but fastest on CPU

    - `MPI_4_layers`

        15 keypoints, even faster but less accurate

- model_folder

    Specify the path in which the models for OpenPose are.
`PATH_T0_OpenPose/openpose/models`
- net_resolution

    Multiples of 16. If it is increased, the accuracy potentially increases. If it is decreased, the speed increases.

    For maximum speed-accuracy balance, it should keep the closest aspect ratio possible to the images or videos to be processed.

    Using `-1` in any of the dimensions, OP will choose the optimal aspect ratio depending on the user's input value. 

    E.g., the default `-1x368` is equivalent to `656x368` in 16:9 resolutions, e.g., full HD (1980x1080) and HD (1280x720) resolutions.

    Defult valu was "-1*128"
- img_resolution

    Defult value is “640*480".
- num_gpu
    
    The number of GPU devices to use. If negative, it will use all the available GPUs in your machine.
- num_gpu_start

    GPU device start number.

- num_scales

    Number of scales to average. To create new scales uses the `scale_gap` and multiplies the resolution with it. 
    
    It helps to get more accurate results. However, if you have a memory error it is an option to use only one scale.

- scale_gap

    Scale gap between scales. No effect unless scale_number > 1. Initial scale is always 1. If you want to change the initial scale, you actually want to multiply the `net_resolution` by your desired initial scale.

### ! The parameters up to this point are the ones that are important for our project and we may apply changes to them. Also the ones related to face are important.( `face_enable` and `face_resolution`)

### Rest of the parameters can be studied from the [source](https://github.com/CMU-Perceptual-Computing-Lab/openpose/blob/master/include/openpose/flags.hpp).

You can see below some of the interestiong results from changing the parameters.

- part_to_show

    Prediction channel to visualize: 
    
    - 0 (default): for all the body parts
    - 1:  for the background heat map
    - 2:  for the superposition of heatmaps
    - 3: for the superposition of PAFs
    - 4-(4+#keypoints): for each body part heat map, the following ones for each body part pair PAF.

     I changed it to 1, 3 and 6. The result was interesting.

    ![part_to_show= 1](Images/PartToShow1.png)

    ![part_to_show= 3](Images/PartToShow3.png)

    ![part_to_show= 6](Images/PartToShow6.png)

- render_pose

    If rendering is enabled, it will render both `outputData` and `cvOutputData` with the original image and desired body part to beshown (i.e., keypoints, heat maps or PAFs).
    - 0: no rendering
    - 1: CPU rendering (slightly faster)
    - 2: GPU rendering (slower but greater functionality, e.g., `alpha_X` flags). 
    - -1: it will pick CPU if CPU_ONLY is enabled, or GPU if CUDA is enabled. 

    The initial value is 2. Changing it to 0 only hand keypoints were displaying.
    ![render_pose= 0](Images/RenderPose0.png)

- alpha_pose

    Blending factor (range 0-1) for the body part rendering. 
    - 1: will show it completely
    - 0: will hide it 
    Only valid for GPU rendering.

    Initial value is 0.6. Setting it to 0 disabled the skeleton view and setting it to 1 enhanced the skeleton view.
    ![alpha_pose= 0](Images/AlphaPose0.png)
    ![alpha_pose= 1](Images/AlphaPose1.png)


- hand_enable
   
    Changed the `hand_enable` to add the hands to the skeleton.
    ![Hands Added](Images/HandADDED.jpeg)


- hand_scale_number

    nalogous to `scale_number` but applied to the hand keypoint detector. Our best results were found with
     - `hand_scale_number` = 6
     
     - `hand_scale_range` = 0.4.

    Applying the changes:
    
     chnaged the `face_enable` to `false`, and then applied changes to `hand_scale_number` which was originally equal to 1. When I put it equal to 0, there was no hand points, and the other images shows the result for `hand_scale_number` equal to 2.
    ![hand_scale_number= 0](Images/HandScaleNumber0.png)
    ![hand_scale_number= 2](Images/HandScaleNumber2.png)

