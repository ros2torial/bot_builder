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
