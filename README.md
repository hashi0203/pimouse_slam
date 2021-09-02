# pimouse_slam

## Environment

- Raspberry Pi 3 ModelB
- Ubuntu 18.04
    - Ubuntu wiki (https://wiki.ubuntu.com/ARM/RaspberryPi)
    - Download Link (http://cdimage.ubuntu.com/releases/bionic/release/ubuntu-18.04.5-preinstalled-server-armhf+raspi3.img.xz)

## 0. Prepare

If you haven't install ROS, execute the following commands.

```bash
$ cd ~
$ git clone https://github.com/ryuichiueda/ros_setup_scripts_Ubuntu18.04_server
$ cd ros_setup_scripts_Ubuntu18.04_server/
$ source ~/.bashrc
```

If you haven't create catkin workspace, execute the following commands.

```bash
$ mkdir -p ~/catkin_ws/src
$ cd ~/catkin_ws/src
$ source /opt/ros/melodic/setup.bash
$ catkin_init_workspace
$ cd ~/catkin_ws
$ catkin_make
```

## 1. Install

We use the web_video_server and aync_web_server_cpp.

```bash
$ cd ~/catkin_ws/src
$ git clone https://github.com/hashi0203/pimouse_slam.git
$ cd ~/catkin_ws
$ catkin_make
```

Install necessary ROS packages.

```bash
$ sudo apt install ros-melodic-urg-node ros-melodic-slam-gmapping
```

## 2. Setup

### 2.1. Raspberry Pi

In `~/.bashrc`, rewrite `ROS_MASTER_URI` and `ROS_HOSTNAME`.

```bash
# export ROS_MASTER_URI=http://localhost:11311
export ROS_MASTER_URI=http://raspberry:11311
# export ROS_HOSTNAME=localhost
export ROS_HOSTNAME=raspberry
```

In `/etc/hosts`, add the following two lines.

```bash
192.168.x.x raspberry # set the IP address of Raspberry Pi
192.168.o.o note      # set the IP address of your PC
```

By using the `ping` command, you can check whether the settings are successfully completed.

```bash
$ ping raspberry
PING raspberry (192.168.x.x) 56(84) bytes of data.
64 bytes from raspberry (192.168.x.x): icmp_seq=1 ttl=64 time=0.112 ms
...
$ ping cad
PING cad (192.168.o.o) 56(84) bytes of data.
64 bytes from cad (192.168.o.o): icmp_seq=1 ttl=64 time=8.90 ms
...
```

### 2.2. Your PC

First, you should follow `0. Prepare` and `1. Install`.

But you shouled be careful of you Ubuntu version. (I don't explain the setups in other OS like Windows and Mac.)

For example, if you should use Ubuntu 20.04, you should use `ros_setup_scripts_Ubuntu20.04_desktop` (change `server` to `desktop`), and install `ros-noetic-slam-gmapping` instead of `ros-melodic-slam-gmapping`.

In `~/.bashrc`, rewrite `ROS_MASTER_URI` and `ROS_HOSTNAME`.

```bash
export ROS_MASTER_URI=http://raspberry:11311 # same as in Raspberry Pi
export ROS_HOSTNAME=note
```

In `/etc/hosts`, add the following two lines.

```bash
192.168.x.x raspberry # same as in Raspberry Pi
192.168.o.o note      # same as in Raspberry Pi
```

## 3. Run

- Terminal 1

    Start roscore.

    ```bash
    $ roscore
    ```

### 3.1. Run on Raspberry Pi only

- Terminal 2

    ```bash
    $ roslaunch pimouse_slam slam_stand_alone.launch
    ```

    After starting nodes, you can move the robot by pressing the following keys.

    - k (+ return): forward
    - j (+ return): backward
    - h (+ return): left
    - l (+ return): right
    - return: stop

- Terminal 4 (your PC)

    ```bash
    $ rviz rviz
    ```

    Press `Add` in `Displays` and add `LaserScan` and `Map` in `By topic` tab.

### 3.2. Run on Raspberry Pi and your PC

- Terminal 2

    ```bash
    $ roslaunch pimouse_slam slam_remote_robot.launch
    ```

    After starting nodes, you can move the robot in the same way as the previous section.

- Terminal 3 (your PC)

    ```bash
    $ roslaunch pimouse_slam slam_remote_desktop.launch
    ```

- Terminal 4 (your PC)

    Same as the previous section.

### 3.3. Save map file

- Terminal 5 (your PC)

    Before stopping, other commands, run the following commands to save the created map.

    ```bash
    $ sudo apt install ros-noetic-map-server # depends on the Ubuntu version
    $ rosrun map_server map_saver -f room # change room to any file name
    $ sudo apt install imagemagick
    $ convert room.pgm room.png # conver pgm file to png
    ```

## Reference

- 「[Raspberry Piで学ぶ ROSロボット入門](https://github.com/ryuichiueda/raspimouse_book_info)」