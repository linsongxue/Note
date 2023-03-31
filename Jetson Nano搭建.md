# Jetson Nano搭建

搭建手册：

https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit

增加交换内存：

https://blog.csdn.net/lyn631579741/article/details/123307869

## 安装ROS

### 换源

* 备份原文件

```bash
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
```

* 更换软件源

```bash
sudo gedit /etc/apt/sources.list
```

```bash
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic main multiverse restricted universe
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic-security main multiverse restricted universe
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic-updates main multiverse restricted universe
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic-backports main multiverse restricted universe
deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic main multiverse restricted universe
deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic-security main multiverse restricted universe
deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic-updates main multiverse restricted universe
deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic-backports main multiverse restricted universe
```

* 更新软件

```bash
sudo apt update
sudo sh -c '. /etc/lsb-release && echo "deb http://mirrors.tuna.tsinghua.edu.cn/ros/ubuntu/ `lsb_release -cs` main" > /etc/apt/sources.list.d/ros-latest.list'
sudo apt install curl # if you haven't already installed curl
curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -
sudo apt install
```

### 安装

* 安装ros

```bash
sudo apt install ros-melodic-desktop-full
```

* 环境设置

```bash
echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

* 后续配置

```bash
sudo apt install python-rosdep python-rosinstall python-rosinstall-generator python-wstool build-essential
```

```
sudo apt install python-rosdep
sudo rosdep init
rosdep update
```

**rosdep update如果不成功，按照以下方案修改**

(1)首先修改

```bash
sudo vim /usr/lib/python2.7/dist-packages/rosdep2/sources_list.py
```

把

```python
url="https://ghproxy.com/"+url
```

加入到download_rosdep_data()函数里的最前面；

(2)其次修改

```bash
sudo vim /usr/lib/python2.7/dist-packages/rosdistro/__init__.py
```

里面的DEFAULT_INDEX_URL参数，如下：

```python
DEFAULT_INDEX_URL = 'https://ghproxy.com/https://raw.githubusercontent.com/ros/rosdistro/master/index-v4.yaml'
```

(3)以下4个文件中也使用了“raw.githubusercontent.com”网址，同样的方法把“https://ghproxy.com/”添加到网址前：

```bash
sudo vim /usr/lib/python2.7/dist-packages/rosdep2/gbpdistro_support.py   #36行
sudo vim /usr/lib/python2.7/dist-packages/rosdep2/sources_list.py   #64行
sudo vim /usr/lib/python2.7/dist-packages/rosdep2/rep3.py	 #36行
sudo vim /usr/lib/python2.7/dist-packages/rosdistro/manifest_provider/github.py   #68行 119行
```

(4)在 /usr/lib/python2.7/dist-packages/rosdep2/gbpdistro_support.py

```bash
sudo vim /usr/lib/python2.7/dist-packages/rosdep2/gbpdistro_support.py 
```

的第204行添加如下代码：

```python
gbpdistro_url = "https://ghproxy.com/" + gbpdistro_url
```

## 安装ROS-navigation

首先建一个工作目录

```bash
mkdir catkin_ws_nav
cd catkin_ws_nav
mkdir src
catkin_make
```

首先通过apt安装一遍（目的是把依赖都解决），然后再卸载

```bash
sudo apt-get install ros-melodic-navigation* 
sudo apt-get remove ros-melodic-navigation ros-melodic-navigation-experimental
```

到catkin_ws_nav/src/目录下下载指定分支的navigation

```bash
cd src
git clone --branch melodic-devel https://github.com/ros-planning/navigation.git
```

退出到catkin_ws_nav/，首先解决一个tf2依赖问题，否则会编译报错；然后编译

```bash
sudo apt install ros-melodic-tf2-sensor-msgs
catkin_make
```

接下来安装一个teb_local_planner，修改navigation中老旧的局部规划算法

```bash
cd src
# 如果翻不了墙就使用前面提到的代理地址
git clone --branch melodic-devel https://ghproxy.com/https://github.com/rst-tu-dortmund/teb_local_planner.git
```

安装依赖

```bash
cd ..
source devel/setup.bash
rosdep install teb_local_planner
```

安装

```bash
catkin_make  -DCATKIN_WHITELIST_PACKAGES="teb_local_planner"
```

## Navigation仿真

参考：https://blog.csdn.net/qq_42727752/article/details/119220528