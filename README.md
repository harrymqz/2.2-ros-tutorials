
# 2.2-ros-tutorials: ROS Tutorials
## 1. Installing and Configuring Your ROS Environment
```
## Show/Check ROS variables are set
printenv | grep ROS

## Source this file or modify ~/.bashrc and paste it in the bash file
source /opt/ros/melodic/setup.bash

## Create a ROS Workspace
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws/
## Running it the first time in your workspace,
## it will create a CMakeLists.txt link in your 'src' folder
catkin_make
source devel/setup.bash
## Check if the above setup.bash is sourced
echo $ROS_PACKAGE_PATH
```
## 2. Navigating the ROS Filesystem

### 2.1. Filesystem concepts
- Packages: Packages are the software organization unit of ROS code. Each package can contain libraries, executables, scripts, or other artifacts.
- Manifests (package.xml): A manifest is a description of a package. It serves to define dependencies between packages and to capture meta information about the package like version, maintainer, license, etc...

### 2.2. Filesystem Tools
```
## **Using rospack**
## Allows you to get information about packages
rospack find [package_name]

## **Using roscd**
## It allows you to change directory
roscd [locationname[/subdir]]

## **roscd log**
## roscd log will take you to the folder where ROS stores log files
roscd log

## **Using rosls**
## It allows you to [ls] directly in a package by name
rosls [locationname[/subdir]]
```

## 3. Creating a ROS Package

### 3.1. What makes up a catkin Package?
For a package to be considered a catkin package it must meet a few requirements
- The package must contain a [catkin compliant package.xml](http://wiki.ros.org/catkin/package.xml) file.
    - That package.xml file provides meta information about the package.
- The package must contain a [CMakeLists.txt which uses catkin](http://wiki.ros.org/catkin/CMakeLists.txt).
    - If it is a [catkin metapackage](http://wiki.ros.org/catkin/package.xml#Metapackages) it must have the relevant boilerplate CMakeLists.txt file.
- Each package must have its own folder
    -   This means no nested packages nor multiple packages sharing the same directory.
```
## my_package is inside of src folder, I mean ~/ws_catkin/src/my_package/
my_package/
  CMakeLists.txt
  package.xml
```

### 3.2. Packages in a catkin Workspace

```
workspace_folder/        -- WORKSPACE
  src/                   -- SOURCE SPACE
    CMakeLists.txt       -- 'Toplevel' CMake file, provided by catkin
    package_1/
      CMakeLists.txt     -- CMakeLists.txt file for package_1
      package.xml        -- Package manifest for package_1
    ...
    package_n/
      CMakeLists.txt     -- CMakeLists.txt file for package_n
      package.xml        -- Package manifest for package_n
```
### 3.3. Creating a catkin Package
```
## How to create a new catkin package
cd ~/catkin_ws/src
catkin_create_pkg <package_name> [depend1] [depend2] [depend3]

## This will create a beginner_tutorials folder which contains a package.xml and a CMakeLists.txt, which have been partially filled out
catkin_create_pkg beginner_tutorials std_msgs rospy roscpp
```
### 3.4. Building a catkin workspace and sourcing the setup file
```
## Now you need to build the packages in the catkin workspace
cd ~/catkin_ws
catkin_make
. devel/setup.bash ## It's the same as (source devel/setup.bash)
```
### 3.5. package dependencies
#### 3.5.1. First-order dependencies
```
## This order will show the first order's dependencies
rospack depends1 <package_name>
```
```
## Example
roscd beginner_tutorials
cat package.xml

## We will see the following piece of code
<package format="2">
...
  <buildtool_depend>catkin</buildtool_depend>
  <build_depend>roscpp</build_depend>
  <build_depend>rospy</build_depend>
  <build_depend>std_msgs</build_depend>
...
</package>
```
#### 3.5.2. Indirect dependencies
```
## In many cases, a dependency will also have its own dependencies
rospack depends1 rospy
## A package can have quite a few indirect dependencies
rospack depends <package_name>
```
### 3.6. Customizing the package.xml
#### 3.6.1. Customizing the package.xml
```
## **description tag**
## Change the description to anything you like
<description>The beginner_tutorials package</description>

## **maintainer tags**
## This is a required and important tag for the package.xml because it lets others know who to contact about the package. At least one maintainer is required, but you can have many if you like
<maintainer email="user@todo.todo">user</maintainer>

## **license tags**
## You should choose a license and fill it in here. Some common open source licenses are BSD, MIT, Boost Software License, GPLv2, GPLv3, LGPLv2.1, and LGPLv3 (http://opensource.org/licenses/alphabetical). At least one maintainer is required, but you can have many if you like
<license>BSD</license>

## **dependencies tags**
## The next set of tags describe the dependencies of your package. The dependencies are split into build_depend, buildtool_depend, exec_depend, test_depend (http://wiki.ros.org/catkin/package.xml#Build.2C_Run.2C_and_Test_Dependencies)
<buildtool_depend>catkin</buildtool_depend>
<build_depend>roscpp</build_depend>
<build_depend>rospy</build_depend>
<build_depend>std_msgs</build_depend>
<exec_depend>roscpp</exec_depend>
<exec_depend>rospy</exec_depend
<exec_depend>std_msgs</exec_depend>
 
## **Final package.xml**
<?xml version="1.0"?>
<package format="2">
  <name>beginner_tutorials</name>
  <version>0.1.0</version>
  <description>The beginner_tutorials package</description>

  <maintainer email="you@yourdomain.tld">Your Name</maintainer>
  <license>BSD</license>
  <url type="website">http://wiki.ros.org/beginner_tutorials</url>
  <author email="you@yourdomain.tld">Jane Doe</author>

  <buildtool_depend>catkin</buildtool_depend>

  <build_depend>roscpp</build_depend>
  <build_depend>rospy</build_depend>
  <build_depend>std_msgs</build_depend>

  <exec_depend>roscpp</exec_depend>
  <exec_depend>rospy</exec_depend>
  <exec_depend>std_msgs</exec_depend>

</package>
```
## 4. Building a ROS Package

### 4.1. Building Packages
Before continue with this section, be sure you have added the following line (source /opt/ros/kinetic/setup.bash) at the end of ~/.bashrc file

#### 4.1.1. Using catkin_make
You can imagine that catkin_make combines the calls to cmake and make in the standard CMake workflow.
```
# In a catkin workspace
catkin_make [make_targets] [-DCMAKE_VARIABLES=...]
catkin_make install  # (optionally)
```
The above commands will build any catkin projects found in the src folder. If your source code is in a different place, say my_src then you would call catkin_make like this:
```
# In a catkin workspace
catkin_make --source my_src
catkin_make install --source my_src  # (optionally)
```
#### 4.1.2. Building Your Package
You should see that there is a folder called beginner_tutorials which you created with catkin_create_pkg in the previous tutorial. We can now build that package using catkin_make:
```
catkin_make
```
## 5. Understanding ROS Nodes

### 5.1. Quick Overview of Graph Concepts
- Nodes: A node is an executable that uses ROS to communicate with other nodes.
- Messages: ROS data type used when subscribing or publishing to a topic.
- Topics: Nodes can publish messages to a topic as well as subscribe to a topic to receive messages.
- Master: Name service for ROS (i.e. helps nodes find each other)
- rosout: ROS equivalent of stdout/stderr
- roscore: Master + rosout + parameter server (parameter server will be introduced later) 

### 5.2. Nodes
A node really isn't much more than an executable file within a ROS package. ROS nodes use a ROS client library to communicate with other nodes. Nodes can publish or subscribe to a Topic. Nodes can also provide or use a Service.

### 5.3. Client Libraries
- rospy = python client library
- roscpp = c++ client library

### 5.4. roscore
roscore is the first thing you should run when using ROS
```
roscore
```
### 5.5. Using rosnode
rosnode displays information about the ROS nodes that are currently running. The rosnode list command lists these active nodes:
```
rosnode list
```
The **rosnode info** command returns information about a specific node.
```
rosnode info <ros_node>
rosnode cleanup # Unregister the nodes
rosnode ping <ros_node>
```

### 5.6. Using rosrun
rosrun allows you to use the package name to directly run a node within a package (without having to know the package path).
```
rosrun [package_name] [node_name]
```
## 6. Understanding ROS Topics
### 6.1. Setup
#### 6.1.1 roscore
Open in a tab (be sure you're running just once)
```
roscore
```
#### 6.1.2. turtlesim
Open in another tab
```
rosrun turtlesim turtlesim_node
```
#### 6.1.3. turtle keyboard teleoperation
Open in a third tab, be sure you are able to see the turtle window and press the arrow keys in the this tab in order to move the turtle
```
rosrun turtlesim turtle_teleop_key
```
### 6.2. ROS Topics
The turtlesim_node and the turtle_teleop_key node are communicating with each other over a ROS **Topic**. turtle_teleop_key is **publishing** the key strokes on a topic, while turtlesim **subscribes** to the same topic to receive the key strokes.

#### 6.2.1. Using rqt_graph
Install some applications to make the ros qt graph work
```
sudo apt install ros-melodic-rqt ros-melodic-rqt-common-plugins
```
In a new tab to see how is represented the topics and the nodes
```
rosrun rqt_graph rqt_graph
```
#### 6.2.2. Introducing rostopic
The rostopic tool allows you to get information about ROS **topics**.
```
## Will show help about rostopic options
rostopic -h
```
#### 6.2.3. Using rostopic echo
rostopic echo shows the data published on a topic.
```
rostopic echo [topic]
rostopic echo /turtle1/cmd_vel
```
#### 6.2.4. Using rostopic list
**rostopic list** returns a list of all topics currently subscribed to and published.
```
rostopic list -h
## In order to see a detailed list of the topics
rostopic list -v
```
### 6.3. ROS Messages
Communication on topics happens by sending ROS **messages** between nodes. For the publisher (turtle_teleop_key) and subscriber (turtlesim_node) to communicate, the publisher and subscriber must send and receive the same **type** of message. This means that a topic **type** is defined by the message **type** published on it. The **type** of the message sent on a topic can be determined using rostopic type.

#### 6.3.1. Using rostopic type
**rostopic type** returns the message type of any topic being published.
```
rostopic type [topic]
## To test it
rostopic type /turtle1/cmd_vel
rosmsg show geometry_msgs/Twist
```
### 6.4. rostopic continued
#### 6.4.1 Using rostopic pub
```
rostopic pub [topic] [msg_type] [args]
rostopic pub -1 /turtle1/cmd_vel geometry_msgs/Twist -- '[2.0, 0.0, 0.0]' '[0.0, 0.0, 1.8]'
```
Let's explain step by step:
- **rostopic pub**: This command will publish messages to a given topic
- **-1**: This option (dash-one) causes rostopic to only publish one message then exit
- **/turtle1/cmd_vel**: This is the name of the topic to publish to
- **geometry_msgs/Twist**: This is the message type to use when publishing to the topic
- **-\-**: This option (double-dash) tells the option parser that none of the following arguments is an option. This is required in cases where your arguments have a leading dash -, like negative numbers
- **'[2.0, 0.0, 0.0]' '[0.0, 0.0, 1.8]'**: As noted before, a geometry_msgs/Twist msg has two vectors of three floating point elements each: linear and angular. In this case, '[2.0, 0.0, 0.0]' becomes the linear value with x=2.0, y=0.0, and z=0.0, and '[0.0, 0.0, 1.8]' is the angular value with x=0.0, y=0.0, and z=1.8

#### 6.4.2 Using rostopic hz
**rostopic hz** reports the rate at which data is published
```
rostopic hz [topic]
rostopic hz /turtle1/pose
## This is a good way to pass the output of the first command to the second one
rostopic type /turtle1/cmd_vel | rosmsg show
```
### 6.5. Using rqt_plot
```
rostopic type /turtle1/cmd_vel | rosmsg show
```
### QUESTIONS

What does the "sources" command (source /opt/ros/melodic/setup.bash)?
Can I use different workspaces?

