# Install Touch driver and OpenHaptics
1. Download two scripts from : https://github.com/jhu-cisst-external/3ds-touch-openhaptics
a. install-3ds-openhaptics-3.4.sh
b. install-3ds-touch-drivers-2019.sh

2. Not to reboot during installation, but Log Out and Log In again.

3. EVERYTIME link the device, add 777 admin to the ttyACM0 
```
sudo chmod 777 /dev/ttyACM0
```
or add the user to permanent mode (not work for me yet):
```
sudo gpasswd -a <username> dialout 
```

4. add `Default Device.config` to path `/usr/share/3DSystems/config/`. Or change its admins as:
```
sudo chmod 777 Default Device.config 
```

5. Run `Touch_Setup`(without sudo), see the serial number correctly, then CLICK `apply` and `ok`. Check the above .config file overide with correct serial number.

6. Test the device by run `Touch_Diagnostic`(without sudo), click different tags to test.

7. Run roslaunch package
```
roslaunch omni_common omni.launch # rviz mode with force
roslaunch omni_common omni_state.launch # topic
```

# Ros package
clone the following package from github:
https://github.com/bharatm11/Geomagic_Touch_ROS_Drivers

catkin_make
 
# Note
- remember to install the full-version ROS to use the Ros package



