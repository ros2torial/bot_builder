### URDF Previewer

Open the package in the VS code and type Ctrl+Shift+X to go to the Marketplace and install the ROS extension. 
In order to use the ROS extension, first, go to a .urdf file (or .urdf.xacro file, either works), click on it, and then press Ctrl+Shift+P to be able to search for the extension usage.
As you type in ROS, the option ROS: Preview URDF will show up. Make sure to click on that option.
Once that option is clicked, you should be able to see what the model will look like. An example is shown below.


https://hiro-group.ronc.one/vscode_urdf_previewer.html#:~:text=urdf%20file%20(or%20.,search%20for%20the%20extension%20usage.&text=As%20you%20type%20in%20ROS,Preview%20URDF%20will%20show%20up.

https://github.com/gkjohnson/vscode-urdf-preview

### Gazebo server on Cloud

If your gazebo world is big and complex then you can run gazebo server on cloud or on other system.
When running gazebo server on Linux server, there will be an issue that camera's image topic won’t be getting published. Other things like dynamics, laser sensor, ultrasonic, bumper etc. will work properly. In order to solve we have to install virtual xservers like Xvfb. Then in the AWS terminal where you are about to run gazebo server just type 

```bash
Xvfb :1 -screen 0 1600x1200x16 &
export DISPLAY=:1.0
```

then launch the gazebo server launch file
https://answers.gazebosim.org//question/14625/running-a-camera-sensor-headless/

