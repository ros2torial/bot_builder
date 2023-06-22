# ros2 environment variables

It contains some ros2 environment variables for easy finding.

### To choose RMW 

for fastdds

```bash
export RMW_IMPLEMENTATION=rmw_fastrtps_cpp
``` 

to add fastdds profile

```bash
export FASTRTPS_DEFAULT_PROFILES_FILE=path/to/xml/ros_example.xml
``` 

for cyclonedds

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
``` 

to add cyclonedds profile

```bash
export CYCLONEDDS_URI=path/to/xml/ros_example.xml
``` 

### To set domain id

```bash
export ROS_DOMAIN_ID=<your_domain_id>
``` 

to keep this setting permanent in ths system

```bash
echo "export ROS_DOMAIN_ID=<your_domain_id>" >> ~/.bashrc
``` 

to just use a particular domain id in a ros2 executable or launch file

```bash
ROS_DOMAIN_ID=3 ros2 run demo_nodes_cpp talker
``` 

### To limit ROS 2 communication to localhost only

```bash
export ROS_LOCALHOST_ONLY=1
``` 

to keep this setting permanent in ths system

```bash
echo "export ROS_LOCALHOST_ONLY=1" >> ~/.bashrc
``` 

to just use localhost only in a particular ros2 executable or launch file

```bash
ROS_LOCALHOST_ONLY=1 ros2 run demo_nodes_cpp talker
``` 

### To check the ROS 2 environment variables with their values

```bash
printenv | grep -i ROS
``` 


