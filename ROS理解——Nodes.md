# ROS要点记录

## Nodes & Topic

* **Node** 表示一个运行的进程，在运行``roscore``之后会产生一个``\rosout``的**node**，此**node**运行后需要新开一个==terminal==来进行后续指令；**node**一般被用来执行类似于摄像头运行、LiDAR运行或者电机之类
* **Topic** 表示**node**之间通信的程序

### rosrun

```shell
$ rosrun [package_name] [node_name] __name:=self name
```

使用``rosrun``命令可以创建一个**node**并开启一个进程

==但是需要注意，产生节点的名字一般是package_name而不是节点名==

```shell
$ rosnode list
```

使用``rosnode list``可以查询目前使用的**node**

假设执行以下命令：

```shell
$ rosrun package node_name1
$ rosrun package node_name2
```

那么``node_name1``和``node_name2``就使用``package``这一**topic**进行通讯

### rostopic

rostopic可使用命令

```shell
$ rostopic bw     display bandwidth used by topic
$ rostopic echo   print messages to screen
$ rostopic hz     display publishing rate of topic    
$ rostopic list   print information about active topics
$ rostopic pub    publish data to topic
$ rostopic type   print topic type
```

情景：

```shell
$ rosrun turtlesim turtlesim_node
$ rosrun turtlesim turtle_teleop_key
```

使用查看工具

```shell
$ sudo apt-get install ros-<distro>-rqt
$ sudo apt-get install ros-<distro>-rqt-common-plugins
$ rosrun rqt_graph rqt_graph
```

得到下图，可以看到两个node之间的通信过程

![Topic连接](https://i.loli.net/2021/11/04/NEcAHvGLSdPIq4s.png)

1. ```shell
   $ rostopic echo [topic]
   ```

打印某个topic信息，例如上面这个==/turtle1/command_velocity==

```shell
linear: 
  x: 2.0
  y: 0.0
  z: 0.0
angular: 
  x: 0.0
  y: 0.0
  z: 0.0
---
linear: 
  x: 2.0
  y: 0.0
  z: 0.0
angular: 
  x: 0.0
  y: 0.0
  z: 0.0
---
```

2. ```shell
   $ rostopic type [topic]
   $ rostopic show [msg_type]
   ```

例如:

```shell
$ rostopic type /turtle1/cmd_vel
geometry_msgs/Twist
```

然后使用``show``命令

```shell
$ rosmsg show [geometry_msgs/Twist]
geometry_msgs/Vector3 linear
  float64 x
  float64 y
  float64 z
geometry_msgs/Vector3 angular
  float64 x
  float64 y
  float64 z
```

3. ```shell
   $ rostopic pub [topic] [msg_type] [args]
   ```

例如：

```shell
$ rostopic pub -1 /turtle1/cmd_vel geometry_msgs/Twist -- '[2.0, 0.0, 0.0]' '[0.0, 0.0, 1.8]'
```

其中

* `` -1`` 代表只执行一条命令就会退出
* ``--`` 代表后面的``args`` 中不存在可选参数了，尤其是当参数中有负号``-`` ，防止混淆
* ``'[2.0, 0.0, 0.0]' '[0.0, 0.0, 1.8]'`` 表示参数，应该和``[msg_type]`` 对应
* ``-r`` 表示指令频率，单位$hz$



## Services & Parameters

* **Services**

> Services are another way that nodes can communicate with each other. Services allow nodes to send a **request** and receive a **response**.

**Services**是另外一种节点间通信的方式，但是稍微高级一点，可以有请求和应答

* **Parameters**

> `rosparam` allows you to store and manipulate data on the ROS Parameter Server. The Parameter Server can store integers, floats, boolean, dictionaries, and lists. rosparam uses the YAML markup language for syntax

**Parameters**简单来说其就是来存储值的

### rosservice

```shell
$ rosservice list         print information about active services
$ rosservice call         call the service with the provided args
$ rosservice type         print service type
$ rosservice find         find services by service type
$ rosservice uri          print service ROSRPC uri
```

1. ```shell
   $ rosservice list
   /clear
   /kill
   /reset
   /rosout/get_loggers
   /rosout/set_logger_level
   /spawn
   /teleop_turtle/get_loggers
   /teleop_turtle/set_logger_level
   /turtle1/set_pen
   /turtle1/teleport_absolute
   /turtle1/teleport_relative
   /turtlesim/get_loggers
   /turtlesim/set_logger_level
   ```

2. ```shell
   $ rosservice type [service]
   ```

例如：

```shell
$ rosservice type /clear
std_srvs/Empty
```

表示无参**service** 

3. ```shell
   $ rosservice call [service] [args]
   ```

例如：

```shell
$ rosservice call /clear
```

可以通过一些方式来查询参数，例如：

```shell
$ rosservice type /spawn | rossrv show
float32 x
float32 y
float32 theta
string name
---
string name
```

可以查看``/spawn`` 的参数，横线上是参数，下面是返回值，前面是类型后面是意义

### rosparam

```shell
$ rosparam set            set parameter
$ rosparam get            get parameter
$ rosparam load           load parameters from file
$ rosparam dump           dump parameters to file
$ rosparam delete         delete parameter
$ rosparam list           list parameter names
```

1. ```shell
   $ rosparam list
   /rosdistro
   /roslaunch/uris/host_nxt__43407
   /rosversion
   /run_id
   /turtlesim/background_b
   /turtlesim/background_g
   /turtlesim/background_r
   ```

2. ```shell
   $ rosparam set [param_name]
   $ rosparam get [param_name]
   ```

设置值和取值

3. ```shell
   $ rosparam dump [file_name] [namespace]
   $ rosparam load [file_name] [namespace]
   ```

举例：

```shell
$ rosparam dump params.yaml
$ rosparam load params.yaml copy_turtle
$ rosparam get /copy_turtle/turtlesim/background_b
255
```

显然==[namespace]==也是**rosparam**

## Rqt_console & Roslaunch

* **rqt_console and rqt_logger_level**

> `rqt_console` attaches to ROS's logging framework to display output from nodes. `rqt_logger_level` allows us to change the verbosity level (DEBUG, WARN, INFO, and ERROR) of nodes as they run.

帮助Debug的，有优先级

```shell
Fatal
Error
Warn
Info
Debug
```

* **roslaunch**

> `roslaunch` starts nodes as defined in a launch file.

可以一次性产生多个节点

### roslaunch

```shell
$ roslaunch [package] [filename.launch]
```

例子：

```xml
<launch> # 表示是一个.launch文件

  <group ns="turtlesim1">
    <node pkg="turtlesim" name="sim" type="turtlesim_node"/>
  </group>

  <group ns="turtlesim2">
    <node pkg="turtlesim" name="sim" type="turtlesim_node"/>
  </group>
  # 以上代码开辟了两个group，其分别有自己的命名空间，所以在不同的group即使叫一样的name也不会冲突，以上命令会同时产生两个乌龟

  <node pkg="turtlesim" name="mimic" type="mimic">
    <remap from="input" to="turtlesim1/turtle1"/>
    <remap from="output" to="turtlesim2/turtle1"/>
  </node>
  # 定义了一个node，其将turtlesim1/turtle1发来的数据转发给turtlesim2/turtle1，做到让两个乌龟同步

</launch>
```

## Edit

Usage：

```shell
$ rosed [package_name] [filename]
```

改变默认编辑器（默认为vim）

```shell
$ export EDITOR='nano -w'
$ export EDITOR='emacs -nw'
```

## Msg & Srv

* **msg**

> msg files are simple text files that describe the fields of a ROS message. They are used to generate source code for messages in different languages.

就是产生给**node**的信息

* **srv**

> an srv file describes a service. It is composed of two parts: a request and a response.

是产生**service**的文件

==msg files are stored in the `msg` directory of a package, and srv files are stored in the `srv` directory.==

类似于写函数参数

### msg

**msg**文件只是描述一下==域类型==和==域名==，举个例子

```yaml
Header header
string child_frame_id
geometry_msgs/PoseWithCovariance pose
geometry_msgs/TwistWithCovariance twist
```

这里有一个新的类型``Header``，他表示了ROS的时间戳、坐标系

```shell
$ rosmsg show [message type]
```

可以指定package名，例如==beginner_tutorials/Nums==

也可以不指定，例如==Num==，但这会导致显示所有package中的==Num==（只要包含了这个msg）

### srv

**srv**文件类似于**msg**但是需要写输入和输出，举个例子**AddTwoInts.srv**

```yaml
int64 A
int64 B
---
int64 Sum
```

```shell
$ roscp [package_name] [file_to_copy_path] [copy_path]
$ rossrv show <service type>
$ rossrv show beginner_tutorials/AddTwoInts.srv.srv
int64 A
int64 B
---
int64 Sum
```

特性同**msg**

### prepare

要使用**msg**和**srv**文件需要先在==package.xml==文件中写明

```xml
<build_depend>message_generation</build_depend>
<exec_depend>message_runtime</exec_depend>
```

在CMakeLists.txt中写明

```cmake
find_package(catkin REQUIRED COMPONENTS
  roscpp
  rospy
  std_msgs
  message_generation
)
## 表示将一个msg文件加入编译
add_message_files(
  FILES
  Num.msg
)
## 表示将一个srv文件加入编译
add_service_files(
  FILES
  AddTwoInts.srv
)
## 使用msg文件和srv文件所必需的依赖
generate_messages(
  DEPENDENCIES
  std_msgs
)
```



### msg & srv code

```shell
$ roscd beginner_tutorials
$ cd ../..
$ catkin_make
$ cd -
```

Any .msg file in the msg directory will generate code for use in all supported languages. 

* The C++ message header file will be generated in `~/catkin_ws/devel/include/beginner_tutorials/`. 
* The Python script will be created in `~/catkin_ws/devel/lib/python2.7/dist-packages/beginner_tutorials/msg`.
*  The lisp file appears in `~/catkin_ws/devel/share/common-lisp/ros/beginner_tutorials/msg/`.

## Publisher & Subscriber

### Publisher

```c++
#include "ros/ros.h"
#include "std_msgs/String.h"

#include <sstream>

/**
 * This tutorial demonstrates simple sending of messages over the ROS system.
 */
int main(int argc, char **argv)
{
  /**
   * The ros::init() function needs to see argc and argv so that it can perform
   * any ROS arguments and name remapping that were provided at the command line.
   * For programmatic remappings you can use a different version of init() which takes
   * remappings directly, but for most command-line programs, passing argc and argv is
   * the easiest way to do it.  The third argument to init() is the name of the node.
   *
   * You must call one of the versions of ros::init() before using any other
   * part of the ROS system.
   */
  
  /**
   * 第三个参数是node名字
   * 
   * 必须使用init()函数之后才能使用其他的ROS功能
   */
  ros::init(argc, argv, "talker");

  /**
   * NodeHandle is the main access point to communications with the ROS system.
   * The first NodeHandle constructed will fully initialize this node, and the last
   * NodeHandle destructed will close down the node.
   */
  
  /**
   * 就是开启一个node，类似于句柄，第一个被构造的会完全初始化node，最后一个被析构的会关闭node
   */
  ros::NodeHandle n;

  /**
   * The advertise() function is how you tell ROS that you want to
   * publish on a given topic name. This invokes a call to the ROS
   * master node, which keeps a registry of who is publishing and who
   * is subscribing. After this advertise() call is made, the master
   * node will notify anyone who is trying to subscribe to this topic name,
   * and they will in turn negotiate a peer-to-peer connection with this
   * node.  advertise() returns a Publisher object which allows you to
   * publish messages on that topic through a call to publish().  Once
   * all copies of the returned Publisher object are destroyed, the topic
   * will be automatically unadvertised.
   *
   * The second parameter to advertise() is the size of the message queue
   * used for publishing messages.  If messages are published more quickly
   * than we can send them, the number here specifies how many messages to
   * buffer up before throwing some away.
   */
  
  /**
   * advertise()函数主要是按照给定的名字（第一个参数）发起一个Topic并且指定谁作为Publisher
   * 谁调用advertise()就是Publisher,此函数返回一个ros::Publisher类型.
   * 使用发布者的.publish()方法进行消息发布，一旦ros::Publisher的所有备份都被析构，Topic
   * 自动被搁置
   *
   * 第一个参数是Topic名,在此处为chatter
   *
   * 第二个参数是缓冲队列大小
   */
  ros::Publisher chatter_pub = n.advertise<std_msgs::String>("chatter", 1000);

  ros::Rate loop_rate(10);

  /**
   * A count of how many messages we have sent. This is used to create
   * a unique string for each message.
   */
  int count = 0;
  while (ros::ok())
  {
    /**
     * This is a message object. You stuff it with data, and then publish it.
     */
    std_msgs::String msg;

    std::stringstream ss;
    ss << "hello world " << count;
    msg.data = ss.str();

    ROS_INFO("%s", msg.data.c_str());

    /**
     * The publish() function is how you send messages. The parameter
     * is the message object. The type of this object must agree with the type
     * given as a template parameter to the advertise<>() call, as was done
     * in the constructor above.
     */
    
    /**
     * publish()发布的命令类型必须对应 advertise<>尖括号中内容
     */
    chatter_pub.publish(msg);

    ros::spinOnce();

    loop_rate.sleep();
    ++count;
  }


  return 0;
}
```

1. ``ros::Rate loop_rate(10);``代码可以使循环以一个固定的频率进行，例如此处为==10hz==，他会记录从上一次调用``loop_rate.sleep()``后过了多久，以保证循环以固定的频率进行

2. ``ros::ok()``返回==false==只有以下三种情况

* **Ctrl+C**
* 有另外一个（同名）节点把我们顶掉
* ``ros::shutdown()``被调用
* 所有==ros::NodeHandle==被析构

3. ``ros::spinOnce();``大概是说这是接受订阅者的返回值？

> Calling `ros::spinOnce()` here is not necessary for this simple program, because we are not receiving any callbacks. However, if you were to add a subscription into this application, and did not have `ros::spinOnce()` here, your callbacks would never get called. So, add it for good measure.

### Subscriber

```c++
#include "ros/ros.h"
#include "std_msgs/String.h"

/**
 * This tutorial demonstrates simple receipt of messages over the ROS system.
 */
void chatterCallback(const std_msgs::String::ConstPtr& msg)
{
  ROS_INFO("I heard: [%s]", msg->data.c_str());
}

int main(int argc, char **argv)
{
  /**
   * The ros::init() function needs to see argc and argv so that it can perform
   * any ROS arguments and name remapping that were provided at the command line.
   * For programmatic remappings you can use a different version of init() which takes
   * remappings directly, but for most command-line programs, passing argc and argv is
   * the easiest way to do it.  The third argument to init() is the name of the node.
   *
   * You must call one of the versions of ros::init() before using any other
   * part of the ROS system.
   */
  ros::init(argc, argv, "listener");

  /**
   * NodeHandle is the main access point to communications with the ROS system.
   * The first NodeHandle constructed will fully initialize this node, and the last
   * NodeHandle destructed will close down the node.
   */
  ros::NodeHandle n;

  /**
   * The subscribe() call is how you tell ROS that you want to receive messages
   * on a given topic.  This invokes a call to the ROS
   * master node, which keeps a registry of who is publishing and who
   * is subscribing.  Messages are passed to a callback function, here
   * called chatterCallback.  subscribe() returns a Subscriber object that you
   * must hold on to until you want to unsubscribe.  When all copies of the Subscriber
   * object go out of scope, this callback will automatically be unsubscribed from
   * this topic.
   *
   * The second parameter to the subscribe() function is the size of the message
   * queue.  If messages are arriving faster than they are being processed, this
   * is the number of messages that will be buffered up before beginning to throw
   * away the oldest ones.
   */
  /**
   * 同Publisher,这里的第一个参数是Topic名，第二个是缓冲队列大小
   * 
   * 第三个参数是callback函数,其才是接受消息的函数
   */
  ros::Subscriber sub = n.subscribe("chatter", 1000, chatterCallback);

  /**
   * ros::spin() will enter a loop, pumping callbacks.  With this version, all
   * callbacks will be called from within this thread (the main one).  ros::spin()
   * will exit when Ctrl-C is pressed, or the node is shutdown by the master.
   */
  
  /**
   * ros::spin()主要是进入Publisher的循环,返回回调值
   */
  ros::spin();

  return 0;
}
```

### CMakeLists

```cmake
cmake_minimum_required(VERSION 2.8.3)
project(beginner_tutorials)

## Find catkin and any catkin packages
find_package(catkin REQUIRED COMPONENTS roscpp rospy std_msgs genmsg)

## Declare ROS messages and services
add_message_files(DIRECTORY msg FILES Num.msg)
add_service_files(DIRECTORY srv FILES AddTwoInts.srv)

## Generate added messages and services
generate_messages(DEPENDENCIES std_msgs)

## Declare a catkin package
catkin_package()

## 会生成两个可执行程序:talker和listener,分别存储在~/catkin_ws/devel/lib/<package name>下
## rosrun执行两个可执行文件
## 可执行文件不会被安装到系统bin下，除非自己设置
add_executable(talker src/talker.cpp)
## ${catkin_LIBRARIES}会包含所有catkin的附件库
target_link_libraries(talker ${catkin_LIBRARIES})
## 保证本package的message headers是先生成再被使用
## 如果使用catkin其他程序的message,需要分别将其加入到下面,因为catkin编译是同步的
add_dependencies(talker beginner_tutorials_generate_messages_cpp)

add_executable(listener src/listener.cpp)
target_link_libraries(listener ${catkin_LIBRARIES})
add_dependencies(listener beginner_tutorials_generate_messages_cpp)
```

### Example

在``catkin_make``后，运行程序前需要一点步骤（遇到roscd失效等也应该运行此语句）

```shell
$ cd ~/catkin_ws
$ source ./devel/setup.bash
```

然后运行**Publisher**和**Subscriber**

```shell
$ rosrun beginner_tutorials talker      (C++)
$ rosrun beginner_tutorials talker.py   (Python) 
[INFO] [WallTime: 1314931831.774057] hello world 1314931831.77
[INFO] [WallTime: 1314931832.775497] hello world 1314931832.77
[INFO] [WallTime: 1314931833.778937] hello world 1314931833.78
[INFO] [WallTime: 1314931834.782059] hello world 1314931834.78
[INFO] [WallTime: 1314931835.784853] hello world 1314931835.78
[INFO] [WallTime: 1314931836.788106] hello world 1314931836.79
```

```shell
$ rosrun beginner_tutorials listener     (C++)
$ rosrun beginner_tutorials listener.py  (Python) 
[INFO] [WallTime: 1314931969.258941] /listener_17657_1314931968795I heard hello world 1314931969.26
[INFO] [WallTime: 1314931970.262246] /listener_17657_1314931968795I heard hello world 1314931970.26
[INFO] [WallTime: 1314931971.266348] /listener_17657_1314931968795I heard hello world 1314931971.26
[INFO] [WallTime: 1314931972.270429] /listener_17657_1314931968795I heard hello world 1314931972.27
[INFO] [WallTime: 1314931973.274382] /listener_17657_1314931968795I heard hello world 1314931973.27
[INFO] [WallTime: 1314931974.277694] /listener_17657_1314931968795I heard hello world 1314931974.28
[INFO] [WallTime: 1314931975.283708] /listener_17657_1314931968795I heard hello world 1314931975.28
```

## Client & Service

==此处以前文srv文件为例==

### Service

```c++
#include "ros/ros.h"
#include "beginner_tutorials/AddTwoInts.h"

/**
 * 定义了一个add功能service,其参数是request和response
 * 这些都是要根据srv文件中内容写的
 */
bool add(beginner_tutorials::AddTwoInts::Request  &req,
         beginner_tutorials::AddTwoInts::Response &res)
{
  res.sum = req.a + req.b;
  ROS_INFO("request: x=%ld, y=%ld", (long int)req.a, (long int)req.b);
  ROS_INFO("sending back response: [%ld]", (long int)res.sum);
  return true;
}

int main(int argc, char **argv)
{
  ros::init(argc, argv, "add_two_ints_server");
  ros::NodeHandle n;

  /**
  *生命一个新的service
  */
  ros::ServiceServer service = n.advertiseService("add_two_ints", add);
  ROS_INFO("Ready to add two ints.");
  ros::spin();

  return 0;
}
```

### Client

```c++
#include "ros/ros.h"
#include "beginner_tutorials/AddTwoInts.h"
#include <cstdlib>

int main(int argc, char **argv)
{
  ros::init(argc, argv, "add_two_ints_client");
  /**
   * 显然参数数量要跟srv文件对应
   */
  if (argc != 3)
  {
    ROS_INFO("usage: add_two_ints_client X Y");
    return 1;
  }

  ros::NodeHandle n;
  # 为 add_two_ints 创建 client ,类型显而易见是srv文件名
  ros::ServiceClient client = n.serviceClient<beginner_tutorials::AddTwoInts>("add_two_ints");
   /**
   * 此处我们定义了一个自动生成的service类变量
   *
   * 每个service类变量有两个成员:request和response
   */
  beginner_tutorials::AddTwoInts srv;
  srv.request.a = atoll(argv[1]);
  srv.request.b = atoll(argv[2]);
   /**
   * 调用service
   *
   * 成功返回true和合法的response,否则返回false和非法response
   */
  if (client.call(srv))
  {
    ROS_INFO("Sum: %ld", (long int)srv.response.sum);
  }
  else
  {
    ROS_ERROR("Failed to call service add_two_ints");
    return 1;
  }

  return 0;
}
```

### CMakeList

其他部分参考上一章Example，不同的部分是以下

```cmake
add_executable(add_two_ints_server src/add_two_ints_server.cpp)
target_link_libraries(add_two_ints_server ${catkin_LIBRARIES})
add_dependencies(add_two_ints_server beginner_tutorials_gencpp)

add_executable(add_two_ints_client src/add_two_ints_client.cpp)
target_link_libraries(add_two_ints_client ${catkin_LIBRARIES})
add_dependencies(add_two_ints_client beginner_tutorials_gencpp)
```

### Example

```shell
$ cd ~/catkin_ws
$ catkin_make
```

* **Terminal 1**

```shell
$ roscore
... logging to /u/takayama/.ros/logs/83871c9c-934b-11de-a451-
001d927076eb/roslaunch-ads-31831.log
... loading XML file 
[/wg/stor1a/rosbuild/shared_installation/ros/tools/roslaunch/roscore.xml]
Added core node of type [rosout/rosout] in namespace [/]
started roslaunch server http://ads:54367/

SUMMARY
======

NODES

changing ROS_MASTER_URI to [http://ads:11311/] for starting master locally
starting new master (master configured for auto start)
process[master]: started with pid [31874]
ROS_MASTER_URI=http://ads:11311/
setting /run_id to 83871c9c-934b-11de-a451-001d927076eb
+PARAM [/run_id] by /roslaunch
+PARAM [/roslaunch/uris/ads:54367] by /roslaunch
process[rosout-1]: started with pid [31889]
started core service [/rosout]
+SUB [/time] /rosout http://ads:33744/
+SERVICE [/rosout/get_loggers] /rosout http://ads:33744/
+SERVICE [/rosout/set_logger_level] /rosout http://ads:33744/
+PUB [/rosout_agg] /rosout http://ads:33744/
+SUB [/rosout] /rosout http://ads:33744/
```

* **Terminal 2** ==service==

```shell
$ rosrun beginner_tutorials add_two_ints_server
Ready to add two ints.
```

* **Terminal 3** ==client==

```shell
$ rosrun beginner_tutorials add_two_ints_client 1 3
Sum: 4
```

此时 **Terminal 2** ==service==

```shell
$ rosrun beginner_tutorials add_two_ints_server
Ready to add two ints.
request: x=1, y=3
sending back response: [4]
```

## Recording data

### demo

* **Terminal 1**

```shell
$ roscore
```

* **Terminal 2**

```shell
$ rosrun turtlesim turtlesim_node
```

* **Terminal 3**

```shell
$ rosrun turtlesim turtle_teleop_key
```

查看此时**Topic**

```shell
$ rostopic list -v
Published topics:
 * /turtle1/color_sensor [turtlesim/Color] 1 publisher
 * /turtle1/cmd_vel [geometry_msgs/Twist] 1 publisher
 * /rosout [rosgraph_msgs/Log] 2 publishers
 * /rosout_agg [rosgraph_msgs/Log] 1 publisher
 * /turtle1/pose [turtlesim/Pose] 1 publisher

Subscribed topics:
 * /turtle1/cmd_vel [geometry_msgs/Twist] 1 subscriber
 * /rosout [rosgraph_msgs/Log] 1 subscriber
```

为了记录**message**，我们需要以下步骤

```shell
$ mkdir ~/bagfiles
$ cd ~/bagfiles
$ rosbag record -a
## 表示将所有Topic都记录
```

然后随便在**turtle_teleop_key**界面动一下，在``~/bagfiles``文件夹下就能看到记录数据

### Examining and playing the bag file

```shell
$ rosbag info <your bagfile>
path:        2014-12-10-20-08-34.bag
version:     2.0
duration:    1:38s (98s)
start:       Dec 10 2014 20:08:35.83 (1418270915.83)
end:         Dec 10 2014 20:10:14.38 (1418271014.38)
size:        865.0 KB
messages:    12471
compression: none [1/1 chunks]
types:       geometry_msgs/Twist [9f195f881246fdfa2798d1d3eebca84a]
             rosgraph_msgs/Log   [acffd30cd6b6de30f120938c17c593fb]
             turtlesim/Color     [353891e354491c51aabe32df673fb446]
             turtlesim/Pose      [863b248d5016ca62ea2e895ae5265cf9]
topics:      /rosout                    4 msgs    : rosgraph_msgs/Log   (2 connections)
             /turtle1/cmd_vel         169 msgs    : geometry_msgs/Twist
             /turtle1/color_sensor   6149 msgs    : turtlesim/Color
             /turtle1/pose           6149 msgs    : turtlesim/Pose
```

```shell
$ rosbag play <your bagfile>
[ INFO] [1418271315.162885976]: Opening 2014-12-10-20-08-34.bag

## 可以看到需要等0.2s,因为要让各个subscriber有时间准备接收数据,可以用 -d 来指定等到时间
Waiting 0.2 seconds after advertising topics... done.
## -s 可以指定从.bag文件的什么位置开始play; -r 可以指定播放速度

Hit space to toggle paused, or 's' to step.
```

### Recording a subset of the data

因为有些大型项目有上千个**Topic**所以不可能一一记录，因此需要记录指定类型的，在bagfiles路径下执行下面命令

```shell
$ rosbag record -O subset /turtle1/cmd_vel /turtle1/pose
```

``-O``：表示输出文件名，在此处为==subset.bag==

``/turtle1/cmd_vel``：表示记录的Topic

``/turtle1/pose``：表示记录的Topic

