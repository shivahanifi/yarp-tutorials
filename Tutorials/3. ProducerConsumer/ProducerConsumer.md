# YARP producer consumer with Port and BufferedPort
For this tutorial the following [source](https://github.com/vvv-school/tutorial_yarp-producer-consumer) has been followed.

The first step is to clone the code in the source file to your prefered directory. 

## Ports from Scratch 
- ### Producer Port
Head to the cloned file and open the C++ file related to producer port.

``/tutorial_yarp-producer-consumer/port/producer.cpp
``

In this file the producer port is being defined. To do so you can use C++ or Python. Here the code is in C++. 

### Steps to define a port in C++

1. Inclued the header files you will need.
    - iostream: In order to read or write to the standard input/output streams.
    - yarp/os/Network.h: Utilities for manipulating the YARP network, including initialization and shutdown.
    - yarp/os/Port.h: A mini-server for network communication.
    - yarp/os/all.h



```
#include <iostream>
#include <yarp/os/Network.h>
#include <yarp/os/Port.h>
#include <yarp/os/all.h>
````

2. Namespaces

The namespace is used to decrease or limit the scope of any variable or function.

- std: (STANDARD) Is a namespace whose members are used in the program. This namespace is present in the iostream.h header file.
- yarp::os: An interface to the operating system, including Port based communication.

```
using namespace std;
using namespace yarp::os;
```
3. Main Function

```
int main(int argc, char *argv[]) {

    //open the network
    Network yarp;

    //open the output port
    Port outPort;

    if (!outPort.open("/producer"))
    {
        //standard error stream
        fprintf(stderr, "error opening port");
        return -1;
    }

    int counter=0;
    while (true) {
        counter++;

        Bottle message;
        message.addInt32(counter);
        message.addString("Hello from producer");
        outPort.write(message);
        printf("[%d] written message\n", counter);
        Time::delay(0.1);
    }
    return 0;
}
```

The codes for the other ports follow the same structure with slight differences.

## Build and Install

Open a terminal, first build the code and then install it.

```
cd tutorial_yarp-producer-consumer
mkdir build; cd build
cmake ../
make
make install
```
The code will generate producer consumer pairs using conventional yarp::os::Port and yarp::os::BufferedPort. The producer opens a port /producer, in which it sends numbered messages. The consumer opens a port /consumer, in which it receives data, and it prints the content at the console.

## Using `` yarp::os::Port``
1. Start the yarp server
```
yarpserevr
```
2. Open a second terminal and start the producer.
```
tutorial_yarp-producer-port
```
3. Open the 3rd terminal and run the consumer.
```
tutorial_yarp-consumer-port 
```
4.On the 4th terminal, connect the two ports.
```
yarp connect /producer /consumer
```
On the consumer terminal you can see:
```
yarp: Receiving input from /producer to /consumer using tcp
Received: 20 Hello from producer
Received: 21 Hello from producer
Received: 22 Hello from producer
Received: 23 Hello from producer
Received: 24 Hello from producer
```
5. Open the 5th terminal and run another instance of consumer, specify a different port name and add a delay.
```
tutorial_yarp-consumer-port --name /consumer2 --delay 5000
```
6. On the 6th terminal connect the consumer2 to the producer.
```
yarp connect /producer /consumer2
```
As you can see on the consumer2 terminal, to simulate a slow computer we added a delay (5s), which is the time the consumer waits before reading from the port.

## Connecting using a different protocol
You can use ``udp`` protocol for the connection between producer and each of the consumers.
```
yarp connect /producer /consumer2 udp
yarp connect /producer /consumer udp
```

## Using ``yarp::os::BufferedPort``
Here we are going to replicate the same experiment with the BufferedPort class.


