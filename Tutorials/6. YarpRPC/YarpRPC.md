# Client/server communication
This [tutorial](https://github.com/vvv-school/tutorial_yarp-rpc) covers basic client/server communication using YARP specialized RPC. After doing this tutorial you will be expected to know the concept of services/client, remote procedure call and two-way communications.
## What is RPC?
A Remote Procedure Call (RPC) server is a network communication interface that provides remote connection and communication services to RPC clients. It enables remote users or RPC clients to execute commands and transfer data using RPC calls or over the RPC protocol.  In YARP it is used to refer to communication that consists of a message and a reply to that message. Regular YARP ports can do this as well, but use of RpcServer RpcClient allows for better run-time checking of port usage to catch mistakes.

This tutorial uses the classes:
- [yarp::os::RpcServer](https://www.yarp.it/latest//classyarp_1_1os_1_1RpcServer.html#details)
- [yarp::os::RpcClient](https://www.yarp.it/latest//classyarp_1_1os_1_1RpcClient.html)
- [yarp::os::Bottle](https://www.yarp.it/latest//classyarp_1_1os_1_1Bottle.html)
  
## Process:
1. Clone the repository and build it.
```
cd tutorial_yarp-rpc
mkdir build
cd build
cmake ../
make
```
2. Run the ``yarpserver``
3. (Server side) Open another terminal and switch to the build directory and run the ``tutorial_yarp-rpc-server``.
```
./tutorial_yarp-rpc-server
```
4. Open and other terminal and send command to the server using ``yarp rpc``:
```
yarp rpc /server
>> hello
```
5. close the ``yarp rpc`` by pressing ``CTRL+C``
6. (Client side) In the build folder run the client and see the the messages exchanged between server and client.
```
./tutorial_yarp-rpc-client
``` 
## C++ files
- Server
```
#include <yarp/os/all.h>
using namespace yarp::os;

int main(int argc, char *argv[]) {
    Network yarp;
    RpcServer port;
    port.open("/server");

    while (true) {
        yInfo()<<"Waiting for a message...";
        Bottle cmd;
        Bottle response;

        // read from port

        // prepare reply

        // write reply
     }
}

```