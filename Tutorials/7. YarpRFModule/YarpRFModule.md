## YARP RFModule
For this tutorial the source can be found on this [link](https://github.com/vvv-school/tutorial_RFModule-simple).
### First Implementation
- Clone the tutorial files.
- Build the tutorial
```
cd tutorial_rfmodule-simple
mkdir build; cd build
cmake ../
make
```
- ``yarpserver`` shoul be running. Open a terminal and run it:
```
yarpserver
```
- Run the tutorial. To do so, open another terminal and switch to the build directory of the tutorial and run it:
```
./tutorial_frmodule-simple
``` 
The module will start executing the function ``updateModule()`` with periodicity of 1.0 seconds. This function is described in the C++ file of the tutorial as follows:
```
/*
    * This is our main function. Will be called periodically every getPeriod() seconds.
    */
    bool updateModule()
    {
        count++;
        //printf("[%d] updateModule\n", count);
        yInfo()<<"["<<count<<"]"<< " updateModule... ";

        return true;
    }
```


- To terminate the module hit ``ctrl+c`` at the terminal.

## C++ File
Here we have an executable which performs periodic activities. In addition, our module can be terminated smoothly by sending a ``ctrl+C`` signal. This is important if we want to perform shutdown operations like parking the robot, turning off the motors etc.

```
#include <iostream>
#include <yarp/os/RFModule.h>
#include <yarp/os/Network.h>
#include <yarp/os/LogStream.h>

using namespace std;
using namespace yarp::os;

class MyModule:public RFModule
{
    RpcServer handlerPort; //a port to handle messages
    int count;

    double period;
public:

    double getPeriod()
    {
        return period; //module periodicity (seconds)
    }

    /*
    * This is our main function. Will be called periodically every getPeriod() seconds.
    */
    bool updateModule()
    {
        count++;
        //printf("[%d] updateModule\n", count);
        yInfo()<<"["<<count<<"]"<< " updateModule... ";

        return true;
    }

    /*
    * Message handler. Manage period and quit commands.
    */
    bool respond(const Bottle& command, Bottle& reply)
    {
        yInfo()<<"Responding to command";

        //parse input
        if (command.check("period"))
        {
            period=command.find("period").asFloat64();
            reply.addString("ack");
            return true;

        }

        return RFModule::respond(command, reply);
    }


    /*
    * Configure function. Receive a previously initialized
    * resource finder object. Use it to configure your module.
    * Open port and attach it to message handler.
    */
    bool configure(yarp::os::ResourceFinder &rf)
    {
        count=0;
        period=1.0; //default value
        handlerPort.open("/myModule");
        attach(handlerPort);

        //user resource finder to parse parameter --period

        if (rf.check("period"))
            period=rf.find("period").asFloat64();

        return true;
    }

    /*
    * Interrupt function.
    */
    bool interruptModule()
    {
        yInfo()<<"Interrupting your module, for port cleanup";
        return true;
    }

    /*
    * Close function, to perform cleanup.
    */
    bool close()
    {
        yInfo()<<"Calling close function";
        handlerPort.close();
        return true;
    }
};

int main(int argc, char * argv[])
{
    Network yarp;

    MyModule module;
    ResourceFinder rf;
    rf.configure(argc, argv);
    // rf.setVerbose(true);

    yInfo()<<"Configure module...";
    module.configure(rf);
    yInfo()<<"Start module...";
    module.runModule();

    yInfo()<<"Main returning...";
    return 0;
}

```
## Modified Implementation
We will now enhance this module.
1. ### Change periodicity with command line parameter ``--period``
If we take a look at the ``configure`` function defined inside the C++ source file, we can see that an instance of ResourceFinder, ``rf``, is initialized with command line parameters ``argc``, ``argv``. In this case we can change the periodicity by recompiling and executing the file in the following structure:
```
./tutorial_rfmodule-simple --period 0.1
./tutorial_rfmodule-simple --period 2.0
```
2. ### Adding an interface to respond to commands from a port
