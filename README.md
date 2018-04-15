# ros_environment
ROS src packages that support the computer vision team's result

System: Ubuntu 16.04 LTS

ROS Version: Kinetic

## Software architecture

- modules
  - 0_driver
    - external library
  - 1_perception_cv
  - 2_perception_others
  - 3_estimator
  - 4_control
  - 5_planning
  - 6_decision
- tools

## Get started
1. install Robotics Operating System Kinetic in your Ubuntu computer
http://wiki.ros.org/ROS/Installation

2. `cd ~ && mkdir ws && cd ws`

3. `git clone --recursive git@github.com:robomasterhkust/ros_environment.git src`

4. `cd perception_cv/lib && sudo cp libMVSDK.so /usr/lib`

5. `cd ~/ws && catkin_make`

## Toolchain
1. install the following dependency for fast development
`sudo apt-get install openocd minicom -y`

`sudo apt-get install terminator cmake vim htop -y`

2. install pixhawk toolchain
To install the development toolchain:
https://dev.px4.io/en/setup/dev_env_linux.html

3. set up the .bashrc for speed up the development process

`vim ~/.bashrc`

and add the following:

`alias cmk='cd ~/ws && catkin_make -j4 -l4 && source devel/setup.bash && cd -'`

`alias ll='ls -alF'`

`alias la='ls -A'`

`alias l='ls -cF'`

`source ~/ws/devel/setup.bash`

and `source ~/.bashrc` after saving the vim file.

4. install CV related dependences

openCV, Eigen, PCL, Ceres

5. install RMOC open sourced dependency
https://github.com/robomasterhkust/RoboRTS

Reference: My TA GAO Wenliang's repository for ELEC 5660 code setup:

https://github.com/gaowenliang/ELEC5660_lab_code

## Multimachine debugging
To debug using another ROS machine, using multimachine by modifying the `/etc/hosts` abd setting three environment variables in the `~/.bashrc`

For the master node, add the ip address of the itself and the slave in the `/etc/hosts`, for instance, like this:

`
127.0.0.1       localhost
127.0.1.1       desktop
192.168.1.237   master
192.168.1.123   desktop
`

Then add threee environment variables in the `~/.bashrc`:

`
export ROS_HOSTNAME=master
export ROS_MASTER_URI=http://master:11311
export ROS_IP=master
`

In the slave machine, do all the same thing except for ROS_IP:

`
export ROS_HOSTNAME=master
export ROS_MASTER_URI=http://master:11311
export ROS_IP=desktop
`

Then launch roscore in the master machine, and all nodes don't need to launch roscore and can recieve topics from each others.
