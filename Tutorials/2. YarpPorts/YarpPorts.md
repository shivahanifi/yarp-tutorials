# YARP Ports
This is the tutorial to get familiar with the ports and connections in YARP. I have followed the steps in the official YARP webpage, [Getting Started with YARP Ports](https://www.yarp.it/latest/note_ports.html).

- ## Simple read-write example
1. Start three terminals and do the following:
```
yarpserver
```
```
yarp read /read
```
```
yarp write /write /read
````
2. Type a list of numbers on the ``yarp write`` terminal.
3. The same will appear on the ``yarp read``terminal.

- ## Transforming the output of /write
The aim is to add up all the numbers in ``/write`` and show them in ``/read``.  
1. ``summer`` is a program to do this. You can find it in the *"example/os/summer/summer.cpp"* in the YARP source. In order to use the ``summer`` first we need to make it executable. To do so, head to the *YARP/example/os/summer* and build the program.
```
mkdir build
cd build
cmake ..
make
```
2. Make sure the ``yarp read`` and ``yarp write`` programs are still running. If that is not the case in two different terminals do the following: 
```
yarp write /write
```
```
yarp read /read
```

3. Open 2 terminals:
- At the first one run the ``summer`` program.
```
./summer
```

 - At the second terminal connect the ports.

```
yarp connect /write /summer
yarp connect /summer /read
```
If you also want to see the raw inputs on the read port connect the read and write ports:
```
yarp connect /write /read
```
4. Open the terminal of ``/write``, write a list of numbers and hit hte enter.
5. Open the ``/read`` terminal, here you can see the sum of the numbers you entered previously. 