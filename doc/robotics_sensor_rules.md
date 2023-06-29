# Udev

By default, when you plug in a USB/Serial device, it will be assigned a device name like ttyACM0, ttyUSB0, ttyS0, input/event0 etc. 

* If you are writing code and need to communicate with that device, you will need to know the name of the device. However, if you plug the devices into different ports or in a different order, then the device assigned names might change. To solve this, you can create a custom udev rule to always assign fixed devices name.
* Sometimes some device name needs executable permission to be given everytime we plug the device. To solve this, you can create a custom udev rule to automatically change its mode when the specific device is plugged.
* Sometimes you may want to run a shell, python etc. script when a specific device is plugged or unplugged. To solve this, you can create udev rule to run the python or shell file when the event occurs.


### To get the connected port detail

* for ttyUSB, ttyACM, ttyS devices (arduino, IMUs, CAN etc. devices)
  
either after connecting the device run

```bash
dmesg | grep tty
``` 

or before connecting the device run

```bash
ls /dev/tty* > /tmp/1
``` 

then after connecting the device run

```bash
ls /dev/tty* > /tmp/2
``` 

then compare both files

```bash
diff /tmp/1 /tmp/2
``` 

* for input devices (intel realsense)

before connecting the device run

```bash
ls /dev/input* > /tmp/1
``` 

then after connecting the device run

```bash
ls /dev/input* > /tmp/2
``` 

then compare both files

```bash
diff /tmp/1 /tmp/2
``` 

* for other devices
  
before connecting the device run

```bash
ls /dev/* > /tmp/1
``` 

then after connecting the device run

```bash
ls /dev/* > /tmp/2
``` 

then compare both files

```bash
diff /tmp/1 /tmp/2
``` 

### To give read/write/execute access permission to IMU

Get the idVendor, idProduct etc find out the connection, if IMU is connected through serial then connection will be /dev/ttyS*, if connected through usb then connection will be /dev/ttyUSB*. You can find this using the *To get the connected port detail* section.  If the connection is /dev/ttyS0 then run 

```bash
udevadm info -a -n /dev/ttyS0
```

Create 70-imu.rules file in /etc/udev/rules.d folder (here 70 represents priority, a priority of 99 is highest)

```bash
sudo touch /etc/udev/rules.d/70-imu.rules
sudo gedit /etc/udev/rules.d/70-imu.rules
```

Set permission to read, write and execute

```bash
ATTRS{idVendor}=="2639", ATTRS{idProduct}=="0300", MODE="0777"
``` 

this will find the device in udev which has vendor id 2639 and product id 0300 and assigns it mode 777.  


then run below command to apply the above changes

```bash
sudo udevadm control --reload-rules && sudo service udev restart && sudo udevadm trigger
``` 

### To Ensure USB (and other) devices always keep same port

Get the idVendor, idProduct, serial etc find out the connection, usually arduino gets connection like /dev/ttyACM*. You can find this using the *To get the connected port detail* section.  If the connection is /dev/ttyACM0 then run 

```bash
udevadm info -a -n /dev/ttyACM0
```

Create 70-arduino.rules file in /etc/udev/rules.d folder (here 70 represents priority, a priority of 99 is highest)

```bash
sudo touch /etc/udev/rules.d/70-arduino.rules
sudo gedit /etc/udev/rules.d/70-arduino.rules
```

To create a symlink in /dev/ for this device.

```bash
ATTRS{idVendor}=="2639", ATTRS{idProduct}=="0300", ATTRS{serial}=="85334343638351804042", SYMLINK+="arduino"
``` 

here serial attribute is added so that /dev/arduino is only assigned to that perticular arduino and if other arduino are connected then we can set another rule for that serial number and assign it something like /dev/arduino2

then run below command to apply the above changes

```bash
sudo udevadm control --reload-rules && sudo service udev restart && sudo udevadm trigger
``` 

### To get real-time info of connected/disconnected devices

```bash
udevadm monitor
``` 

With udevadm monitor command, you can tap into udev in real time and see what it sees when you plug in different devices. When a device is plugged in then action such as add, bind are shown. In case of device unplugging unbind, remove are shown.


### To run shell/python/ros2 code when the sensor is plugged or removed

Get the idVendor, idProduct, serial etc find out the connection, usually intel relasense gets connection like /dev/input/event*. You can find this using the *To get the connected port detail* section.  If the connection is /dev/input/event0 then run 

for triggering when device is plugged use ATTRS{idVendor} kind of attributes, you will get this values from 

```bash
udevadm info -a -n /dev/input/event0
```

for triggering when device is unplugged use ENV{ID_VENDOR} kind of attributes, you will get this values from 

```bash
udevadm monitor --property
```

Create 10-trigger.rules file in /etc/udev/rules.d folder (here 10 represents priority, a priority of 99 is highest)

```bash
sudo touch /etc/udev/rules.d/10-trigger.rules
sudo gedit /etc/udev/rules.d/10-trigger.rules
```

Set trigger actions

```bash
ATTRS{manufacturer}=="Intel(R) RealSense(TM) Depth Camera 45 ", ATTRS{idVendor}=="8066", ATTRS{idProduct}=="0a63", ATTRS{serial}=="93022392", ACTION=="add", RUN+="/usr/bin/touch /tmp/realsense_connected.txt"

ENV{ID_VENDOR_ID}=="8066", ENV{ID_MODEL_ID}=="0a63", ENV{ID_VENDOR}=="Intel_R__RealSense_TM__Depth_Camera_45", ENV{ID_SERIAL_SHORT}=="93022392", ACTION=="remove", RUN+="/usr/bin/touch /tmp/realsense_disconnected.txt"

ENV{ID_VENDOR_ID}=="8066", ENV{ID_MODEL_ID}=="0a63", ENV{ID_VENDOR}=="Intel_R__RealSense_TM__Depth_Camera_45", ENV{ID_SERIAL_SHORT}=="93022392", ACTION=="remove", RUN+="/usr/bin/python3 /home/user/realsense_disconnected.py"

ENV{ID_VENDOR_ID}=="8066", ENV{ID_MODEL_ID}=="0a63", ENV{ID_VENDOR}=="Intel_R__RealSense_TM__Depth_Camera_45", ENV{ID_SERIAL_SHORT}=="93022392", ACTION=="remove", RUN+="/usr/bin/bash /home/user/realsense_disconnected.sh"
```

if this realsense device is plugged then /tmp/realsense_connected.txt file is created, if it is unplugged then /tmp/realsense_disconnected.txt file is created, /home/user/realsense_disconnected.py and /home/user/realsense_disconnected.sh files are executed.

```bash
SUBSYSTEMS=="usb", ATTRS{serial}=="HY45GTYU", ATTRS{idProduct}=="0360", ATTRS{idVendor}=="8689", ATTRS{manufacturer}=="IMU", ACTION=="add", RUN+="/bin/su user_name -c 'export HOME=root; export ROS_DOMAIN_ID=0; export ROS_LOG_DIR=/home/user_name/log; source /opt/ros/humble/setup.bash; ros2 topic pub -1 -w 0 /imu_status std_msgs/msg/String \"data: \"device_plugged_in\"\"'"
```

get **user_name** value from running 
```bash
whoami
```
here we are running without root privilages */bin/su user_name* as root privilages causes ros2 publisher to be discovered only by root privilaged ros2 subscriber. Hence we are ruuning it with *user_name* privilage only. Also in order to run the ros2 commands through udev it is necessary to define **HOME, ROS_DOMAIN_ID and ROS_LOG_DIR** environment variables, otherwise ros2 command won't run.


### To observe the execution of run commands through udev rules 

```bash
sudo udevadm control --log-priority=debug
journalctl -f
```

to set it back to previous level

```bash
sudo udevadm control --log-priority=info
```

### To connect udev event to systemd service

```bash
ATTRS{idProduct}=="0360", ATTRS{idVendor}=="8689", ATTRS{manufacturer}=="IMU", ACTION=="add", TAG+="systemd", ENV{SYSTEMD_WANTS}+="my-ros2-publisher.service"
```
