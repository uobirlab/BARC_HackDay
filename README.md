BARC_HackDay 16.6.2014, Pre-installation:
======================================================

Instal Ubuntu OS.
-------------------
* Dual system (recommended) - the installation is quick and it will guide you how to install dual system to your existing one. Although Ubuntu 14.04 exists, we recommend to install Ubuntu 12.04 as the ROS libriaries are supported mainly for this version. You can find alternative downloads here: ``http://www.ubuntu.com/download/alternative-downloads``

* Virtual machine - if you dont want to install full operating system, you can install software (Virtual machine), which will emulate Ubuntu in your existing OS. For a Virtual machine software see for example ``https://www.virtualbox.org/``, download ubuntu 12.04 with ROS installed here ``http://nootrix.com/downloads/#RosVM``

* If you have Ubuntu as dual boot system, follow ROS Hydro installation: ``http://wiki.ros.org/hydro/Installation/Ubuntu``

Pre-knowledge:
-------------
1. Read and make ROS beginners tutorials: ``http://wiki.ros.org/ROS/Tutorials``

2. RVIZ is a visualisation tool of the robot, its sensoric measurements and values on different topics. When your roscore is running, you can simply open rviz by running ``rosrun rviz rviz``. This will open a Graphical User Interface. On the left, there is a panel with list of chosen parameters to show. You can simply add one by clicking on add button and choose your desire one. Important is, that you need to run some node providing messages to the topic, which you would like to visualise. For example, if you would like to see data fro laser scanner by choosing "scan" in Rviz, you need some node reading data from Hokuyo (laser scanner on the robot) already running.

3. Player/Stage -TODO: some short description

4. Laser scanner functionality - TODO


About the Provided code
------------------------

- ***barc_teleop*** is a node that provides teleoperation by taking as input messages of type sensor_msgs::Joy and giving in the output msgs of type geometry_msgs::Twist . It publishes to the topic "/teleop/cmd_vel" . Twist messages in a cmd_vel topic is the common command that drivers of a differential drive robot compatible with ROS listen to for sending motor commands. This node is optimised (button allocation wise) for an xbox controller. For xbox controller Y button is teleoperation, A is autonomus navigation, B is stop.

- ***barc_safe_cmd*** node subscribes to teleoperation node messages and navigation package messages and based on the above user preferences gives to the drivers either the 'cmd_vel' coming from teleoperation or by autonomous navigation. Please make sure if the robot does anything strange and unsafe to stop it immediatly by pressing the stop button!


About the provided launch files:
--------------------------------

- ***pioneer_simple_teleop.launch*** runs simple teleoperation in the real robot by executing the teleoperation node, the pioneer drivers node and the drivers joystick node.

- ***pioneer_nav.launch*** provides more advance capabilities such as Simultaneous Localization and Mapping (SLAM) and autonomous navigation. Allows the user to teleop the robot in order to construct a map and localise with a SLAM algorithm. Once the map is at least partially constructed the user can enable the robot (joystick autonomy button) to navigate autonomously in a defined explored area by clicking on the map with the rviz ''goal'' button.

- ***stage_sim.launch*** enables all the capabilities from simple teleoperation to SLAM and navigation but on a simulated robot on the stage simulator.


Tasks:
------

***Simple navigation:*** 

This task is for complete beginners. The aim is to write a simple node controlling and moving a robot around using laser scan readings. First, your node will be tested using Player/Stage simulator and as soon as it will work, you will run it on a real robot.

***cmd_vel publisher:***

a. Create a new ROS package "wanderer"(see tutorials how to do this).

b. Create a new node "wanderer_publisher" using your favourite programming language. c. You can control a Pioneer robot by publishing messages of type ``TODO`` (link TODO). Therefore, create a simple publisher to this topic. You can send command of your preferences. Please note, that a maximal values are: TODO

d. If you are using C++ to code, you need to compile your work by command ``catkin_make`` in your catkin workspace (not in package). e. To test your node, first start roscore by command ``roscore``.

f. In another terminal, run Player/Stage simulator by comand ``TODO``. A window with simulation should open. g. In another terminal, run your code by ``rosrun wanderer wanderer_publisher``. You should see that the robot in simulation starts to move and its behaviour corresponds to the command what you gave to it. h. If you can control a robot, it is time to procede to next step.

 ***Laser scanner listener:***

a. Create a new node "wanderer_listener" inside package "wanderer". 

b. Laser scanner provides messages of type ``TODO`` on the topic ``TODO``, so write a listener node for this topic. For testing purposes, only print some information by ``ROSINFO()`` when you receive a message.

c. If you are using C++ to code, you need to compile your work by command ``catkin_make`` in your catkin workspace (not in package). 

d. To test your node, first start roscore by command ``roscore``.

e. In another terminal, run Player/Stage simulator by comand ``TODO``. A window with simulation should open. 

f. In another terminal, run your code by ``rosrun wanderer wanderer_listener``. If everything is working, you should see messages created by your node in terminal. Notice, that the robot is not moving as we didnt run any control node.

g. If this part is working, you can procede to merging both steps.

***Robot wanderer:***

The idea of a node is to navigate robot in a corridor using laser scan measurements. Thus, you need to read laser scans and add some logic to detect, if there is more space on the left or on the right. Based on this information, robot should turn in that way that it will be still in the middle of corridor. Notice, you should always detect if a space in front of a robot is free.

a. In this step, you should combine both packages to one. Thus create a new node "wanderer_node", when you will connect both previous nodes.

b. The best way to do this is copy/paste code from "wanderer_listener" and start to work on logic part - detection of free space on the left, right, in front of a robot. Please feel free to consult yout ideas with demonstrators.

c. Again, test your node by printing ``ROSINFO()`` - TODO: it will good to prepare for them some random navigation in the environment that they can test their logic of laser scanner reading. 

d. Now, when former step is working, you should import code from "wanderer_publisher". Please notice, that you cant copy/paste everything, but smarter combination is necessary to respect existing code. 

e. Send different velocity commands, if a robot is on the left/right. TODO: should they stop robot if there is an obstacle in front of? f. Test your node by running Player/Stage and your node. 

g. If your node is working, show it to demonstrator. After this, you can put your laptop on the robot and test the same node on real robot!

***Real robot testing:***

a. Run ``roscore`` 

b. Open another terminal and run ``roslaunch TODO pioneer_simple_teleop.launch``, this will enable you a robot hardware and sensors. 

c. You can open rviz in other terminal by ``rosrun rviz rviz`` 

d. Add a new data to visualise - choose "scan" and as topic choose TODO 

e. You can run your code by ``rosrun wanderer wanderer_node``. If anything wrong happens, press stop button on joystick.

TODO: they might need to setup extra rights for USB port to be able to connect to robot. Add here the command to do that. TODO: test dependencies for nodes TODO: information about setting .bashrc file

***Advance navigation:***

via ROS move_base package - TODO: description 