# [yarpmanager](https://www.yarp.it/latest/yarpmanager.html)

This is a detailed explanation of how I proceeded with the [tutorial_yarpmanager](https://github.com/vvv-school/tutorial_yarpmanager).
## XML file
The application is build in the XML format to be used in the yarpmanager. The XML file contains the information a bout the application such as ``name, description, version`` and ``author`` which are not mandatory to mention. However, ``module`` and ``conection`` are important. The core of the application is being defined in these parts.
```
<application>
    <name>Tutorial_Yarpmanager</name>

    <description>A simple Yarp application</description>
    <version>1.0</version>
    
    <authors>
        <author email="ali.paikan@iit.it">Ali Paikan</author>
    </authors>

    <module>
        <name>yarpview</name>
        <parameters>--x 100</parameters>
        <node>localhost</node>
    </module>

    <module>
        <name>yarpdev</name>
        <parameters>--device fakeFrameGrabber --mode line </parameters>
        <node>localhost</node>
     </module>

    <connection>
        <from>/grabber</from>
        <to>/yarpview/img:i</to>
        <protocol>udp</protocol>
    </connection>

</application>
```
## Progress
1. After installing YARP successfully, run ``yarpserver`` in the terminal.
2. Clone the [tutorial_yarpmanager](https://github.com/vvv-school/tutorial_yarpmanager/blob/master/README.md).
3. Open the terminal at the tutorial folder and run ``yarpmanager``. The GUI for the yarpmanager will show at the screen.
4. In the yarpmanager, from the entities choose the application and double click on it.
5. The modules of the application will appear on the right side.
6. Run the modules 
    - One by one by choosing them and then pressing the green start button :arrow_forward:.
    - All together by pressing *Run* from the *Manage* menu. 
7. Connect all the modules. Press *Connect all* from the *Manage* menu. 
8. To change the parameters as the tutorial you can do it in two ways:

    - Temporarily: double click on the parameters field of the module and edit it.
    - Permanently: open the corresponding XML file and change the parameters field of the desired module.
9. To terminate the application choose *Disconnect all* from the *Manage* menu and the *Stop all*.