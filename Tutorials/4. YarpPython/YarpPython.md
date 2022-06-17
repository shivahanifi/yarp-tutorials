This is the tutorial in which I explain how I started to use YARP from Python.

## How to run a ``.py`` file with YARP?

- ### YARP installed seperately
    In this case, you have to use ``SWIG``. Find the steps in this [link](https://www.yarp.it/git-master/yarp_swig.html#yarp_swig_python).
- ### YARP installed using [robotology-superbuild](https://github.com/robotology/robotology-superbuild)
  
    ### Step 1 
    In this case you have to turn on the cmake option:  ``ROBOTOLOGY_USES_PYTHON``.

    It is better to check if the option is already on:

    ```
      cd robotology-superbuild/build
      ccmake .
    ```
    In the list of options search for the ``ROBOTOLOGY_USES_PYTHON`` if it is on there is no problem. Otherwise, turn it on and then configure with ``c`` and generate with ``g``.

    Another way to turn it on is to reach it through the command line:
    ```
      ccmake ../
      ROBOTOLOGY_USES_PYTHON=ON
      make
      make install
    ```
    ### Step 2: The Path
    Make YARP to know where to find Python library in YARP and also the libraries of Python. You need to add the following lines to the ``.bashrc`` file. (Do not forget to replace the ``PATH_TO_THE_SUPERBUILD``.)
    ```
      export PYTHONPATH=PATH_TO_THE_SUPERBUILD/robotology-superbuild/build/src/YARP/lib/python3

      export PYTHONPATH=/usr/lib/python3/dist-packages
    ```

    - What is ``.bashrc`` ?
  
        It is a script file that is executed when a user logs in. The file itself contains a series of configurations for the terminal session. It is a hidden file and to view it run ``ls -a`` at the root directory. 
        
        To view the ``.bashrc`` file:

        ```
            cat .bashrc
        ```
        To make changes in the ``.bashrc`` file:

        ```
              vi .bashrc
        ```

        To start editing press any letter on the keyboard. (Personal experience: use ``x`` if you want to delete anything).

            
        At the end of the ``.bashrc`` file add the lines you want.Then press escape and to save and exit from vi, press colon (:) followed by ‘wq’ and enter.The changes are saved. 
        
        To reflect the changes in the bash, either exit and launch the terminal again.Or use the command:
        ```
            source .bashrc
        ```
        You can check the changes made, by viewing the ``.bashrc`` file :
        ```
            cat .bashrc
        ```


    ### :bulb: Easier Way 
    Use a text editor and esily save it.
        ```
            gedit .bashrc
        ```
    ### Just for the record
    The previously mentioned way is a permanent way to edit the ``.bashrc``. However, you can also do it from the command line which will work only once (Why would you :grin:).
    ```
        export PYTHONPATH=${PYTHONPATH}:/usr/lib/python3/dist-packages

        export PYTHONPATH=${PYTHONPATH}:PATH_TO_THE_SUPERBUILD/robotology-superbuild/build/src/YARP/lib/python3
    ```    
    ### Step 3: Run the file
     Navigate the terminal to the directory where the ``.py`` file is located using the cd command. 

     Then, type ``python3 SCRIPT.py`` in the terminal to execute the script.