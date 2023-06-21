# Udev

By default, when you plug in a USB device, it will be assigned a name like ttyACM0, ttyUSB0 etc. If you are writing code and need to communicate with that device, you will need to know the name of the device. However, if you plug the devices into different ports or in a different order, the automatically assigned names might change. To solve this, you can create custom udev rules to always assign specific devices to a particular name.

### To give read/write/execute access permission to IMU

Create 70-imu.rules file in /etc/udev/rules.d folder (here 70 represents priority, a priority of 99 is highest)

```bash
sudo touch /etc/udev/rules.d/70-imu.rules
sudo gedit /etc/udev/rules.d/70-imu.rules
``` 

Set permission to read write and execute

```bash
SUBSYSTEM=="tty" , ATTRS{idVendor}=="2639", ATTRS{idProduct}=="0300", MODE="0777"
``` 

to get idVendor, idProduct etc find out the connection, if IMU is connected through serial then connection will be /dev/ttyS*, if connected through usb then connection will be /dev/ttyUSB*. If the connection is /dev/ttyS0 then run 

```bash
udevadm info -a -n /dev/ttyS0
``` 

then run below command to apply the above changes

```bash
sudo udevadm control --reload-rules && sudo service udev restart && sudo udevadm trigger
``` 

### To Ensure USB (and other) devices always keep same port

Create 70-arduino.rules file in /etc/udev/rules.d folder (here 70 represents priority, a priority of 99 is highest)

```bash
sudo touch /etc/udev/rules.d/70-arduino.rules
sudo gedit /etc/udev/rules.d/70-arduino.rules
``` 

To create a symlink in /dev/ for this device.

```bash
SUBSYSTEM=="tty" , ATTRS{idVendor}=="2639", ATTRS{idProduct}=="0300", ATTRS{serial}=="85334343638351804042", SYMLINK+="arduino"
``` 

here serial attribute is added so that /dev/arduino is only assigned to that perticular arduino and if other arduino are connected then we can set another rule for that serial number and assign it something like /dev/arduino2
to get idVendor, idProduct, serial etc find out the connection, if arduino is connected through /dev/ttyACM0 then run 

```bash
udevadm info -a -n /dev/ttyACM0
``` 

then run below command to apply the above changes

```bash
sudo udevadm control --reload-rules && sudo service udev restart && sudo udevadm trigger
``` 


### To get the connected port detail

* for ttyUSB, ttyACM devices (USB or Serial)
  
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


### To get real-time info of connected/disconnected devices

```bash
udevadm monitor
``` 

With udevadm monitor command, you can tap into udev in real time and see what it sees when you plug in different devices

