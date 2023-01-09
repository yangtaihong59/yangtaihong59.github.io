---
layout: post
title:  虚拟机ubuntu18安装 Ros lego-loam
subtitle: 环境配置流程
date:   2022-08-10
categories: ROS
tags: [ROS,环境配置,SLAM]
---

## 我的安装环境

VMware Workstation 16 Pro

ubuntu18.04

## ros 安装

直接去官网按照[ros wiki](http://wiki.ros.org/melodic/Installation/Ubuntu)来

### 1.Setup your sources.list
```
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
```
> **问题 安装速度缓慢**
>
> - **原因：** 软件源的问题
> - **解决方案：** 登录 http://wiki.ros.org/ROS/Installation/UbuntuMirrors网站
> - **有USTC，清华，北外，上海交大的源，任意一个复制替换即可**
>

### 2.Set up your keys

```
sudo apt install curl # if you haven't already installed curl
curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -
```
### 3.Installation（建议：Desktop-Full Install）

```
ssudo apt update
```
```
sudo apt install ros-melodic-desktop-full
```
### 4.Environment setup
```
echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
source ~/.bashrc
```
### 5.Dependencies for building packages
```
sudo apt install python-rosdep python-rosinstall python-rosinstall-generator python-wstool build-essential
```

### 6.Initialize rosdep
```
sudo apt install python-rosdep
sudo rosdep init
rosdep update
```
 在执行`sudo rosdep init` 会出现境外资源被屏蔽的问题（2023.01.03）
 > **问题 境外资源屏蔽**
 > 直接使用 VPN [Linux 安装配置 Clash GUI 图形界面版代理上网]https://www.yangtaihong.xyz/linux/2023/01/03/Linux-%E5%AE%89%E8%A3%85%E9%85%8D%E7%BD%AE-Clash-GUI-%E5%9B%BE%E5%BD%A2%E7%95%8C%E9%9D%A2%E7%89%88%E4%BB%A3%E7%90%86%E4%B8%8A%E7%BD%91.html)

 在执行`rosdep update` 会出现超时，主要的解决方法参考

> **问题 超时**
>
> dckwin（解决rosdep update一直timeout的问题）
>
> 本质是把要下载的文件全部下载到本地，然后修改相应的URL。
>
> - 把 https://github.com/ros/rosdistro 的配置文件下载到本地
>   - `git clone https://github.com/ros/rosdistro` 这里下载到/home/uestc/目录下。
>
> > **进入root**
> >
> > ```
> > sudo passwd root
> > ```
> >
> > 再输入用户密码，接下来会让你设置root密码，并再次确认。
> > 接下来输入`su`，再输入刚刚设置的root密码就可以进入root了。
>
> - 修改 `/etc/ros/rosdep/sources.list.d/20-default.list` 文件，
>
>   - 如果在第一步`sudo rosdep init` 没有生成`20-default.list`文件，那么可以自己手动在`/home/XXX`下：
>     `touch 20-default.list`
>     打开这个文件：写入以下内容，请注意，路径必须和第1步中你下载的rosdistro 里的文件路径对应
>
>   - 
>     ```
>     yaml file:///home/xxx/rosdistro/rosdep/osx-homebrew.yaml osx
>      
>     # generic
>     yaml file:///home/xxx/rosdistro/rosdep/base.yaml
>     yaml file:///home/xxx/rosdistro/rosdep/python.yaml
>     yaml file:///home/xxx/rosdistro/rosdep/ruby.yaml
>     gbpdistro file:///home/xxx/rosdistro/releases/fuerte.yaml fuerte
>     ```
>
> - 修改`/usr/lib/python2.7/dist-packages/rosdep2/sources_list.py`， 在这个文件的75行
>   `DEFAULT_SOURCES_LIST_URL = 'file:///home/xxx/rosdistro/rosdep/sources.list.d/20-default.list'`
>
> - 请注意，如果`20-default.list`是你自己生成的文件，那么就填你自己生成文件的路径; 如果你是改的`/etc/ros/rosdep/sources.list.d/20-default.list`文件，那么应该填写 `DEFAULT_SOURCES_LIST_URL = ‘file:///etc/ros/rosdep/sources.list.d/20-default.list’`
>
> - 修改 `/usr/lib/python2.7/dist-packages/rosdep2/rep3.py` 39行 把 URL 改成自己`targets.yaml`文件本地的文件路径
>   `REP3_TARGETS_URL = 'file:///home/uestc/rosdistro/releases/targets.yaml'`
>
> - 修改 `/usr/lib/python2.7/dist-packages/rosdistro/__init__.py` 68 行 把 URL 改成自己`index-v4.yaml`文件本地的文件路径
>   `DEFAULT_INDEX_URL = 'file:///home/uestc/rosdistro/index-v4.yaml'`
>
> 所有文件保存之后退出。然后重新打开一个终端
>
>
> ```
> sudo rosdep init
> rosdep update
> ```
>
> 然后再执行`roscore`，这样我们的ros服务都启动成功了
>

## lego-loam 安装教程
### 1.安装GTSAM
```
git clone https://bitbucket.org/gtborg/gtsam.git
```
如果发现类似，`GnuTLS`错误，那么最简单的方式：`export GIT_SSL_NO_VERIFY=1`

这样每次启动shell又失效了，如果想永久，可以在`bashrc`里配置，`source`下就可以
```
vim ~/.bashrc

export GIT_SSL_NO_VERIFY=1

source ~/.bashrc
```
### 2.编译安装GTSAM
```
cd gtsam

mkdir build
cd build
cmake ..
sudo make install
```
### 3.建立自己的ROS工作空间
```
mkdir -p catkin_ws/src

cd catkin_ws/src

catkin_init_workspace
```
### 4.下载lego-loam
```
cd ~/catkin_wc_/src

git clone https://github.com/RobustFieldAutonomyLab/LeGO-LOAM.git
```
```
如果觉得慢，可以用别人的gitee库
git clone https://gitee.com/Yeah2333/LeGO-LOAM.git
```
### 5.编译lego-loam
```
cd ..
catkin_make -j1
source ~/devel/setup.bash
```
### 6.运行lego-loam

```
roslaunch lego_loam run.launch

rosbag play *.bag --clock --topic /velodyne_points /imu/data
```
至此，lego-loam编译安装结束

> **问题 虚拟机下运行rviz时报错闪退或者跑LEGO-LOAM却只有一条路径的现象**
>
> - 关闭硬件加速	
>
>   - ```
>     echo " export SVGA_VGPU10=0" >> ~/.bashrc
>     source ~/.bashrc
>     ```
>
>
> - 关闭虚拟机的3D图形加速
>

> **问题 运行lego_loam时报错**`[mapOptmization-7] process has died [pid 11653, exit code 127, cmd /home/p/legoloam/dev`
>
> 方法一、直接安装`libparmetis-dev`即可解决
>
> ```
> sudo apt-get install libparmetis-dev
> ```
>
> 方法二、网上也有说因为没有把动态链接库的安装路径（例如 `/usr/local/lib` ）放到变量 `LD_LIBRARY_PATH` 里
> 可在`.bashrc`中添加
>
> ```
> export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
> ```
>
> 也可以运行时
>
> ```
> export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
> roslaunch lego_loam run.launch
> ```
>

### 7.loam建图配置(假设我们使用的是velodyne激光雷达)

1.运行loam

```
roslaunch loam_velodyne loam_velodyne.launch
```

```
rosbag play 文件名.bag
```

2.保存地图

rviz 选择右侧最上面Global Options列表下的Fixed Frame选择Laser _odom

选择pointClouds列表下的Topic选择laser_cloud_surround

3.录制

```
rosbag record -o out /laser_cloud_surround
```

或者

```
rosbag record -o out /laser_cloud -b 40960
#laser_cloud是因为surround会消失新建的话题 等同于laser_cloud_surround只是不会消失
#ctrl+C结束录制生成out_（时间）.bag
```

4.转PCD

```
rosrun pcl_ros bag_to_pcd out_（时间）.bag /laser_cloud_surround pcd
```

5.查看

```
pcl_viewer （文件名）如：1566443218.880078077.pcd
```


##  感谢本文所参考链接的博主们的分享

[1] https://blog.csdn.net/s261676224/article/details/120639282

[2] https://blog.csdn.net/weixin_47272122/article/details/112567893

[3] https://blog.csdn.net/qq_28901541/article/details/116134317

[4] https://blog.csdn.net/weixin_42301918/article/details/123716711

[5] https://blog.csdn.net/plejahhshsh/article/details/125773243

[6] https://blog.csdn.net/weixin_43678195/article/details/113588729

