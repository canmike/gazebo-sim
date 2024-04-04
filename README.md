## Installation
### Ubuntu
Ubuntu 20.04 inside WSL2 used in this tutorial.

### ROS
The ROS noetic is used in this tutorial. You can use steps below to install it:
```
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
sudo apt install curl
curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -
sudo apt update
sudo apt install ros-noetic-desktop-full
echo "source /opt/ros/noetic/setup.bash" >> ~/.bashrc
source ~/.bashrc
sudo apt install python3-rosdep python3-rosinstall python3-rosinstall-generator python3-wstool build-essential python3-future python3-pip
pip install mavproxy
sudo rosdep init
rosdep update
```

### Gazebo
The Gazebo version 11 is used in this tutorial. ROS noetic has gazebo11 but if there is problem with it, you can use steps below to install it:
```
sudo sh -c 'echo "deb http://packages.osrfoundation.org/gazebo/ubuntu-stable `lsb_release -cs` main" > /etc/apt/sources.list.d/gazebo-stable.list'
sudo apt-get update
sudo apt-get install gazebo11 libgazebo11-dev
```

### Ardupilot
The latest version (4.3.6 at the time of writing) used in this tutorial. You can use steps below to install it:
```
git clone https://github.com/ArduPilot/ardupilot.git
cd ardupilot
git submodule update --init --recursive
alias waf="$PWD/modules/waf/waf-light"
waf configure --board=sitl
# If python error:
# sudo ln -s /usr/bin/python3 /usr/bin/python
waf all
./Tools/environment_install/install-prereqs-ubuntu.sh -y
```

### Ardupilot_gazebo
```
cd ..
git clone https://github.com/SwiftGust/ardupilot_gazebo
cd ardupilot_gazebo
mkdir build
cd build
cmake ..
make -j2
sudo make install
```
- After installation of this plugin, you need to add lines below inside your bash (bashrc, zshrc, ...):
```
source /usr/share/gazebo/setup.sh
gedit ./.bashrc
```
- Write these lines in bashrc.
```
export GAZEBO_MODEL_PATH=~/ardupilot_gazebo/models:${GAZEBO_MODEL_PATH}
export GAZEBO_MODEL_PATH=~/ardupilot_gazebo/models_gazebo:${GAZEBO_MODEL_PATH}
export GAZEBO_RESOURCE_PATH=~/ardupilot_gazebo/worlds:${GAZEBO_RESOURCE_PATH}
export GAZEBO_PLUGIN_PATH=~/ardupilot_gazebo/build:${GAZEBO_PLUGIN_PATH}
```

### Swarm
- Make 3 copies of iris_with_standoffs_demo.
```
cd ~/ardupilot_gazebo/models
export GAZEBO_MODEL_PATH=$GAZEBO_MODEL_PATH:$(pwd)
cp -r iris_with_standoffs_demo iris_with_standoffs_demo_1
cp -r iris_with_standoffs_demo iris_with_standoffs_demo_2
cp -r iris_with_standoffs_demo iris_with_standoffs_demo_3
```


- In iris_with_standoffs_demo_1,
```
cd iris_with_standoffs_demo_1
gedit model.sdf
```
- Change this:    
```
    <plugin name="arducopter_plugin" filename="libArduPilotPlugin.so">

      <fdm_addr>127.0.0.1</fdm_addr>
      
      <fdm_port_in>9002</fdm_port_in>
      
      <fdm_port_out>9003</fdm_port_out>
```
- To this:
```
    <plugin name="arducopter_plugin" filename="libArduPilotPlugin.so">
    
      <fdm_addr>127.0.0.1</fdm_addr>
      
      <fdm_port_in>9012</fdm_port_in>
      
      <fdm_port_out>9013</fdm_port_out>
```
      
- In iris_with_standoffs_demo_2,
```
cd ../iris_with_standoffs_demo_2
gedit model.sdf
```

- Change this:    
```
    <plugin name="arducopter_plugin" filename="libArduPilotPlugin.so">

      <fdm_addr>127.0.0.1</fdm_addr>
      
      <fdm_port_in>9002</fdm_port_in>
      
      <fdm_port_out>9003</fdm_port_out>
```

- To this:
```
    <plugin name="arducopter_plugin" filename="libArduPilotPlugin.so">
    
      <fdm_addr>127.0.0.1</fdm_addr>
      
      <fdm_port_in>9022</fdm_port_in>
      
      <fdm_port_out>9023</fdm_port_out>
```
      
- In iris_with_standoffs_demo_3,
```
cd ../iris_with_standoffs_demo_3
gedit model.sdf
```

- Change this:    
```
    <plugin name="arducopter_plugin" filename="libArduPilotPlugin.so">

      <fdm_addr>127.0.0.1</fdm_addr>
      
      <fdm_port_in>9002</fdm_port_in>
      
      <fdm_port_out>9003</fdm_port_out>
```

- To this:
```
    <plugin name="arducopter_plugin" filename="libArduPilotPlugin.so">
    
      <fdm_addr>127.0.0.1</fdm_addr>
      
      <fdm_port_in>9032</fdm_port_in>
      
      <fdm_port_out>9033</fdm_port_out>
```

- Make a copy of iris_ardupilot.world in ardupilot_gazebo/worlds
```
cd ~/ardupilot_gazebo/worlds
cp iris_ardupilot.world iris_multiuav.world
gedit iris_multiuav.world
```

- Open the new iris_multiuav.world file and change the bottom of file from this:
```
    <model name="iris">
      <pose> 0 0 0 0 0 0 </pose>
      <include>
        <uri>model://iris_with_standoffs_demo</uri>
         <pose> 0 0 0 0 0 0 </pose>
      </include>
    </model>
```
- To this:
```
    <model name="iris_demo_1">
      <pose> 0 0 0 0 0 0 </pose>
      <include>
        <uri>model://iris_with_standoffs_demo_1</uri>
      </include>
    </model>
    <model name="iris_demo_2">
      <pose> -10 -10 0 0 0 0 </pose>
      <include>
        <uri>model://iris_with_standoffs_demo_2</uri>
      </include>
    </model>
    <model name="iris_demo_3">
      <pose> 10 10 0 0 0 0 </pose>
      <include>
        <uri>model://iris_with_standoffs_demo_3</uri>
      </include>
    </model>
```
### Testing
- Open a terminal for each UAV and run the commands below in each of them (3 UAV example):

```
# First terminal:
cd ~/ardupilot1/Tools/autotest
./sim_vehicle.py -v ArduCopter -f gazebo-iris --console -I0

# Second terminal:
cd ~/ardupilot1/Tools/autotest
./sim_vehicle.py -v ArduCopter -f gazebo-iris --console -I1

# Third terminal:
cd ~/ardupilot1/Tools/autotest
./sim_vehicle.py -v ArduCopter -f gazebo-iris --console -I2
```
- Open a new terminal and run the command below:

```
gazebo --verbose ~/ardupilot_gazebo/worlds/iris_multiuav.world
```

