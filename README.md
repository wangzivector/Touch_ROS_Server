# Overview
(This ROS package originates from https://github.com/bharatm11/Geomagic_Touch_ROS_Drivers) 
- Detailed installation steps in Ubuntu 18 and 20, for old and newer device
- Rviz visualization
- Force feedback

## Install Old Touch driver and OpenHaptics (identify as: /dev/ttyACM0, newer version Touch bought from 2023 see next tag)
1. Download two scripts from (https://github.com/jhu-cisst-external/3ds-touch-openhaptics): 
(a). install-3ds-openhaptics-3.4.sh
(b). install-3ds-touch-drivers-2019.sh
```
bash ./install-3ds-openhaptics-3.4.sh
bash ./install-3ds-touch-drivers-2019.sh
```
Not to reboot during installation, but Log Out and Log In again.

2. Set the Eev Path by:
```
sudo -H gedit /etc/environment
```
in gedit add the following line:
```
GTDD_HOME="/usr/share/3DSystems"
```

3. Every time add 777 admin to the ttyACM0 when link Touch device:
```
sudo chmod 777 /dev/ttyACM0
```

4. (There will be a new `Default Device.config` if running with `sudo Touch_Setup`) change its admins using the following line:
```
sudo chmod 777 Default Device.config
```
(This intriguing steps are to create a non-sudo running files for roslaunch)

5. Run `Touch_Setup`(without sudo), check the serial number is indentied correctly in GUI. Then CLICK `apply` and `ok`. Check the above .config file overide with correct serial number.

6. Test the device by run `Touch_Diagnostic`(without sudo), click different tags to test.


## Install New Touch driver and OpenHaptics (identify as:/dev/hidraw, for newer Touch bought from 2023)
0. Must for Ubuntu 20 (or newer)

1. Download the `Openhaptics for Linux v3.4` and `Touch Device Driver v2024.09.19` from the web: `https://support.3dsystems.com/s/article/OpenHaptics-for-Linux-Developer-Edition-v34?language=en_US`

2. Unzip two tar files, followting two steps to install `Openhaptics 3.4` and `Touch Device Driver v2024`:

a. In folder `openhaptics_3.4-0-developer-edition-amd64`, run (NOTE: The final step reboot is not necessary, but Log Out and Log In again):
```
sudo ./install
```

b. In folder `TouchDriver_2024_09_19`, run : 
```
bash ./install_haptic_driver
```

3. Test before using ROS package below:
```
cd TouchDriver_2024_09_19/bin 
./TouchCheckup
./Touch_AdvancedConfig
```
choose the right serial number device, the information will be automatically save to `/home/smart/.3dsystems`.


4. To easily find these tools when plug in Touch device, you can cp them to home:
```
cd TouchDriver_2024_09_19
mkdir ~/TouchTools
cp -r ./bin/* ~/TouchTools
```

## ROS package
1. clone this package from github: https://github.com/wangzivector/Touch_ROS_Server
```
catkin_make
```
Note: For newer version Touch in Ubuntu 20, if prompt error about `/usr/bin/ld: cannot find -lncurses` during `catkin_make`, try:
```
sudo apt-get install libncurses5-dev
```

7. Run roslaunch package
```
roslaunch omni_common omni_state.launch # publish pose and listen to force cmd.
# roslaunch omni_common omni.launch
```

## Pose Sensing and Force Feedback
There are two topics for publishing the Touch pose and exerting three-dimensional force:
```python
### Pulishing pose
rostopic echo /phantom/phantom/pose

### Exert force output:
# Bash cmd:
rostopic pub /phantom/phantom/force_feedback omni_msgs/OmniFeedback "{force: {x: 1.0, y: 0.0, z: 0.0}, position: {x: 1.0, y: 0.0, z: 0.0}}"
# Python code:
from omni_msgs.msg import OmniFeedback
self.pub_force_feedback = rospy.Publisher("/phantom/phantom/force_feedback", OmniFeedback, queue_size=1)
def touch_force_feedback(self, force, position):
    # rostopic pub /phantom/phantom/force_feedback omni_msgs/OmniFeedback
    # "{force: {x: 1.0, y: .0, z: 0.0}, position: {x: 0.240, y: 0.150, z: 0.160}}"
    fp = OmniFeedback()
    fp.force.x = force[0]
    fp.force.y = force[1]
    fp.force.z = force[2]
    fp.position.x = position[0]
    fp.position.y = position[1]
    fp.position.z = position[2]
    self.pub_force_feedback.publish(fp)
```


## Note
- remember to install the full-version ROS to use this ROS package
