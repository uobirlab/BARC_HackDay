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

2. RVIZ is a visualisation tool, which displays a robot, its sensoric measurements and even values on different topics. When your roscore is running, you can simply open rviz by running ``rosrun rviz rviz``. This will open a Graphical User Interface. On the left, there is a panel with list of chosen parameters to show. You can simply add one by clicking on add button and choose your desire one. 
It is important, that you need to run a node publishing to a topic, which you would like to visualise. For example, if you would like to see data from laser scanner, you need to click on button "Add", then choose "LaserScan" in Rviz. When clicking on "Topic", you can choose specific topic with data. However, if there is no topic to choose, you dont have any node publishing these data. It means, you are not connected to a robot or simulation.

3. Player/Stage are former tools how to provide standard way for using robotics platforms. The idea behind Player is similar to idea behind ROS - to have standard way of working with a robot and easy switching between working with a real platform or a simulator - Stage. As these were popular tools, ROS also provides a bridge between ROS and Stage simulator. Therefore, Stage simulator can be used without issues when working with ROS, it listen to messages on topics and according them control a robot. It also publish simulation of sensoric measurements. We will use Stage simulator to test our code. 

4. Laser scanner functionality - Laser scan works as follows. First, it sends a laser beam in a direction specified by a starting angle. Second, it measures a time between transmitting of the beam and returning of the reflected beam. Then, it counts the distance to an object, where the beam reflected. Afterwards, it increases the angle and sends a new beam to a new position. Therefore, the laser scanner provides set of distances according predefined set of angles.


About the Provided code
------------------------

- ***barc_teleop*** is a node that provides teleoperation by taking as input messages of type sensor_msgs::Joy and giving in the output msgs of type geometry_msgs::Twist . It publishes to the topic "/teleop/cmd_vel" . Twist messages in a cmd_vel topic is the common command that drivers of a differential drive robot compatible with ROS listen to for sending motor commands. This node is optimised (button allocation wise) for an xbox controller. For xbox controller Y button is teleoperation, A is autonomus navigation, B is stop.

- ***barc_safe_cmd*** node subscribes to teleoperation node messages and navigation package messages and based on the above user preferences gives to the drivers either the 'cmd_vel' coming from teleoperation or by autonomous navigation. Please make sure if the robot does anything strange and unsafe to stop it immediatly by pressing the stop button!


About the provided launch files:
--------------------------------

- ***pioneer_simple_teleop.launch*** runs simple teleoperation in the real robot by executing the teleoperation node, the pioneer drivers node and the drivers joystick node.

- ***pioneer_nav.launch*** provides more advance capabilities such as Simultaneous Localization and Mapping (SLAM) and autonomous navigation. Allows the user to teleop the robot in order to construct a map and localise with a SLAM algorithm. Once the map is at least partially constructed the user can enable the robot (joystick autonomy button) to navigate autonomously in a defined explored area by clicking on the map with the rviz ''goal'' button.

- ***stage_sim.launch*** enables all the capabilities from simple teleoperation to SLAM and navigation but on a simulated robot on the stage simulator.


0.Task - Setting up
------

0. If you dont have catkin workspace, create one. Ensure, that in your ".bashrc" file (use command ```gedit ~/.bashrc``` to open the file) are following lines: ```source /opt/ros/hydro/setup.bash``` and ```source /home/lenka/barc_ws/devel/setup.bash``` (of course, this path differs and must include your actual path to your catkin workspace)
1. Go to your src folder inside catkin workspace and clone the repository by command ``git clone https://github.com/uobirlab/BARC_HackDay.git
``
2. Go to the catkin workspace by command ``cd ..`` and compile the code by running ``catkin_make``, this should run without issues and compile provided code.


1.Task -Simple navigation:
-----------

This task is for complete beginners. The aim is to write a simple node controlling and moving a robot around using laser scan readings. First, your node will be tested using ***Stage simulator*** and as soon as it will work, you will run it on a real Pioneer robot.

***cmd_vel publisher:***

a. Create a new ROS package "wanderer"(see ROS tutorials how to do this).

b. Create a new node "wanderer_publisher" using your favourite programming language.

c. You can control a Pioneer robot by publishing messages of type ``geometry_msgs/Twist`` (http://docs.ros.org/api/geometry_msgs/html/msg/Twist.html) to topic ***cmd_vel*** (http://wiki.ros.org/ROSARIA). Therefore, create a simple publisher to this topic. You can send command of your preferences. Please note, that a maximal translation speed is 1.2 m/s and maximal rotation speed is 300°/s. 

d. If you are using C++ to code, you need to compile your work by command ``catkin_make`` in your catkin workspace (not inside the package).

e. To test your node, first start roscore by command ``roscore``.

f. In a new terminal, run Stage simulator by comand ``roslaunch barc_launch stage_sim.launch``. A window with simulation should open. You can see a map of lower ground of CS building with pioneer robot placed in this word.

g. In a new terminal, run your code by ``rosrun wanderer wanderer_publisher``. You should see that the robot in simulation starts to move and its behaviour corresponds to the command what you gave to it. 

h. If you can control a robot, it is time to procede to next step.

 ***Laser scanner listener:***

a. Create a new node "wanderer_listener" inside package "wanderer". 

b. Laser scanner provides messages of type ``sensor_msgs/LaserScan`` (http://docs.ros.org/api/sensor_msgs/html/msg/LaserScan.html) on the topic ``scan`` (http://wiki.ros.org/hokuyo_node#Published_Topics), so write a listener node for this topic. For testing purposes, only print some information by ``ROSINFO()`` (behaving same as printf) when you receive a message.

c. If you are using C++ to code, you need to compile your work by command ``catkin_make`` in your catkin workspace (not inside the package). 

d. To test your node, first start roscore by command ``roscore``.

e. In another terminal, run Stage simulator by command ``roslaunch barc_launch stage_sim.launch``. A window with simulation should open. 

f. In another terminal, run your code by ``rosrun wanderer wanderer_listener``. If everything is working, you should see messages created by your node in terminal. Notice, that the robot is not moving as we didnt run any control node.

g. You can also run both nodes (wanderer_pubsliher and wanderer_listener). As the robot will move in Stage simulator, laser scanner data will be simulated and you can observe this within your listener node. 

h. If this part is working, you can procede to merging both steps.

***Robot wanderer:***

The idea of a node is to navigate robot in a corridor using laser scan measurements. Thus, you need to read laser scans and add some logic to detect, if there is more space on the left or on the right. Based on this information, robot should turn to ensure that it will be still in the middle of corridor. Later, you can add detection if a space in front of a robot is free.

a. In this step, you should combine both packages to one. Thus create a new node "wanderer_node", when you will connect both previous nodes.

b. The best way to do this is copy/paste code from "wanderer_listener" and start to work on logic part - detection of free space on the left, right, in front of a robot. Please feel free to consult your ideas with demonstrators. Notice, that message ``sensor_msgs/LaserScan)`` contains an array ``float32[] ranges``, which includes the measurements from scanner ordered by an angle. If the robot's heading is zero angle, first measurements correspondes to -90° (ie. right), last measurement to +90° (ie. left)

c. Again, test your node by using ``ROSINFO()`` to print left, right (depending if a robot should turn left or right).

d. Run Stage simulator by ``roslaunch barc_launch stage_sim.launch`` and your node to test. You can also run your first node wanderer_pubsliher to have robot moving around. 

e. Now, when previous step is working, you should import code from "wanderer_publisher". Please notice, that you cant copy/paste everything, but smarter combination is necessary to respect existing code. 

e. Send different velocity commands, if a robot is on the left/right. You can make experiments with different commands if the robot is really close to an obstacle, or if it is further. 

f. Test this behaviour by running Stage simulator and your node. 

g. If it is working, add stopping the robot, when an obstacle is directly in fron of it in certain distance. Test again.

h. If your node is working, show it to demonstrator. After this, you can put your laptop on the robot and test the same node on real robot!

***Real robot testing:***

a. Run ``roscore`` 

b. Open another terminal and run ``roslaunch barc_launch pioneer_simple_teleop.launch ``, this will enable you robot's hardware and sensors. 

c. You can open rviz in other terminal by ``rosrun rviz rviz`` 

d. Add a new data to visualise - choose "LaserScan" and as topic choose ``\scan`` 

e. You can run your code by ``rosrun wanderer wanderer_node``. If anything wrong happens, press stop button on joystick.

TODO: they might need to setup extra rights for USB port to be able to connect to robot. Add here the command to do that. TODO: test dependencies for nodes

2.Task - advance navigation
==================================

For navigation and obstacle avoidance, ROS’s navigation stack can be used and more specifically the ***move_base*** node. This node reads the odometry, the current pose estimate (through a TF map->odom transformation, and the laser range finder scans from the relevant topics and safely drives the robot in the environment to a predefined goal. Makes use of two different types of maps: a) a global map of the environment that mostly contains stable features (e.g. walls); b) a local map that contains things like walls and obstacles in the local surroundings of the robot. These maps are cost maps. This means that the map is represented by a grid of cells and each cell has a cost value allocated to it. For example a group of greed cells that represents an obstacle will have high cost as these cells are occupied (not free space) and the robot cannot move there. You can find more on these on the [navigation tutorials]. The *pioneer_nav.launch* file contains everything that you need in order to run autonomous navigation on the real robot.

***Simultaneous Localisation and Mapping (SLAM)***

SLAM means that the robot builds on real time a map of the environment tha it is in, while at the same time localises (finds its possition) on this map/environment. The are two main SLAM algoriths in ROS. We will be using [gmapping]. Gmapping needs the input from a laser range finder (normally on the ROS topic "/scan") and odometry (ros [odometry] standard message, normally in "/odom") from the pioneer drivers. It provides a grid map of the environment ("/map" topic) and the pose of the robot through a TF package transformation. To visualise in RVIZ you can choose the map display (on map frame) and the transformation on the for the position of the robot (odometry frame).


[navigation tutorials]:http://wiki.ros.org/navigation

[gmapping]:http://wiki.ros.org/gmapping

[odometry]:http://docs.ros.org/hydro/api/nav_msgs/html/msg/Odometry.html
