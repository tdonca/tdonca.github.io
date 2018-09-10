---
layout: post
title: ROS Communication with Microsoft HoloLens
date: 2018-09-08 15:46
comments: true
external-url:
categories: Robotics
---


>Augmented Reality interfaces provide intuitive and efficient human-robot interaction without the costly time delay of testing, stopping, and programming separately. [Microsoft HoloLens][1] is one such useful AR device, and with it, a whole range of interactive communication is possible. 

<br/>
In order to integrate the HoloLens into robotics projects, a robust communication system is necessary. Since many robotics projects use the ROS framework, incorporating the ROS communication format directly on the HoloLens would be ideal. The [ROS-sharp][2] library developed by Dr. Martin Bischoff uses [JSON][3] format communication to emulate ROS communication. The [rosbridge][4] package provides the conversion between JSON and ROS messages, and ROS-sharp connects to a ROS node running a rosbridge server using [WebSockets][5].

Setup involves starting the WebSocket server on the ROS side and creating a WebSocket client on the HoloLens side.

<br/>
### Setting up the ROS server
	
Overview of the necessary steps:<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1. Install the rosbridge_suite package<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2. Launch the WebSocket server<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3. Determine the server IP address<br/>

The rosbridge_suite package provides a convenient interface for creating ROS messages from JSON. Non-ROS programs such as the HoloLens application can connect to the rosbridge server and use JSON commands to create ROS functionality. This way, HoloLens projects can seamlessly communicate with any ROS node.


##### Install rosbridge_suite
Install the [rosbridge_suite][6] package by following the site instructions. This just uses apt-get to install the package into your currently installed ROS distribution.
	

##### Launch the WebSocket server
After sourcing your ~/.bashrc file, start the rosbridge node that creates a WebSocket server using the command `roslaunch rosbridge_server rosbridge_websocket.launch`  
<br/>
![server-running](/assets/hololens/RosBridgeServer.png){:class="img-responsive" height="200" width="425" style=" display: block; margin: auto; "}
<br/>

##### Determine the server IP address
Now that the server is running, check the server IP address using ifconfig. This will be used later to configure the HoloLens application so that it can find the WebSocket server.


<br/>
### Setting up the HoloLens project

Overview of the necessary steps:<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1. Install Unity2017<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2. Download and open the ROS-sharp Unity package<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3. Modify the project configuration settings<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4. Import and configure the HoloLens toolkit<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5. Create a ROS communication link<br/>

Hololens projects are most conveniently created through Unity. The [Unity Manual][7] provides a good overview of Unity project architecture and how objects and scripts are used to create functionality. The ROS-sharp package provides a deafult Unity project that has the libraries necessary for ROS communication over WebSocket. Once configured properly, this project will allow seamless ROS communication for publishing and subscribing to topics.


##### Install [Unity2017][8]  
The 2017 version is currently required (September 2018) for the HoloLens development toolkit, which provides many convenient scripts. Look for it in the Older Versions section.
	
##### Download and open ROS-sharp Unity package  
The HoloLens runs on the Universal Windows Platform, so the ROS-sharp library version specific to UWP is necessary. This has been generously provided by [David Whitney][13]. Find it [here][9], and download the repository to the Unity workspace. Open the project using the Unity interface:  

<br/>
![open-project](/assets/hololens/OpenProject.png){:class="img-responsive" height="300" width="514" style=" display: block; margin: auto; "}
		
This project provides the ROS-sharp library preloaded and ready to use. 

##### Modify the project configuration settings  
You might notice some build errors in Unity, and this is because the ROS-sharp library uses the .NET Framework 4.6, which is not activated by default in Unity 2017.
In `Edit > Project Settings > Player > UWP Settings` modify the scripting and API settings to use .NET 4.6:  

<br/>
![config-settings](/assets/hololens/ConfigSettings.png){:class="img-responsive" height="400" width="278" style=" display: block; margin: auto; "}
<br/>	

##### Import and Configure the HoloLens toolkit  
The [Microsoft Mixed Reality Toolkit][10] provides a collection of scripts and components to make Unity development for the HoloLens as easy as possible. Follow the installation instructions in the link, making sure to download the [2017 release][11] to match up with Unity2017. Import the toolkit as a custom package, and enable the project and scene configuration settings as described in the link's instructions.
	
	
##### Create a ROS communication link  
The HoloLens has some settings that controls what "capabilities" the application is allowed to have. In this case, enable the networking capabilities in `Edit > Project Settings > Player > Publishing Settings` 


![net-capabilities](/assets/hololens/NetworkingCapabilities.png){:class="img-responsive" height="400" width="224" style=" display: block; margin: auto; "}
	
Now that the project is all configured, the last thing to do is to create a WebSocket client to connect to the rosbridge server. Create a RosConnector component, and make sure is uses the WebSocketUWP Protocol. Input the WebSocket server IP address determined earlier during the ROS setup, with the default port of 9090.  


![ros-connector](/assets/hololens/RosConnector.png){:class="img-responsive" height="200" width="557" style=" display: block; margin: auto; "}

Everything is ready! After building and deploying the application using Visual Studio, you will see the HoloLens successfully connect to the ROS WebSocket server.  


![unity-build](/assets/hololens/Build.png){:class="img-responsive" height="300" width="502" style=" display: block; margin: auto; "}   
![holo-connect-vs](/assets/hololens/HololensConnect.png){:class="img-responsive" height="403" width="502" style=" display: block; margin: auto; "}   
![holo-connected](/assets/hololens/HololensConnected.png){:class="img-responsive" height="232" width="502" style=" display: block; margin: auto; "}   


	
<br/>
### Extending Capabilities
	
With the WebSocket connection, the Hololens is connected to the ROS network and capable of publishing and subscribing to topics just like a regular node. The ROS-sharp project already provides some very useful publishers and subscribers as scripts:  

![ros-scripts](/assets/hololens/PubSub.png){:class="img-responsive" height="300" width="551" style=" display: block; margin: auto; "}

as well as some standard ROS message types. It is very easy to create new messages and their accompanying publishers and subscribers. Just follow the directions on the ROS-sharp wiki to [create custom messages][12].


[1]: https://www.microsoft.com/en-us/hololens
[2]: https://github.com/siemens/ros-sharp
[3]: https://en.wikipedia.org/wiki/JSON
[4]: http://wiki.ros.org/rosbridge_suite
[5]: https://en.wikipedia.org/wiki/WebSocket
[6]: http://wiki.ros.org/rosbridge_suite/Tutorials/RunningRosbridge
[7]: https://docs.unity3d.com/2017.3/Documentation/Manual/UnityManual.html
[8]: https://store.unity.com/download
[9]: https://github.com/dwhit/ros-sharp
[10]: https://github.com/Microsoft/MixedRealityToolkit-Unity/blob/master/GettingStarted.md
[11]: https://github.com/Microsoft/MixedRealityToolkit-Unity/releases
[12]: https://github.com/siemens/ros-sharp/wiki/Dev_NewMessageTypes
[13]: https://www.linkedin.com/in/david-whitney-aa288011b/