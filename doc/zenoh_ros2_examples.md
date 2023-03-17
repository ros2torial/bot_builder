
# Zenoh ros2 examples

It contains examples of ros2 topic, service, action communication using zenoh along with some security examples.

### Topic

- Terminal 1

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
ROS_DOMAIN_ID=4 ros2 run examples_rclcpp_minimal_publisher publisher_member_function
``` 

- Terminal 2

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
zenoh-bridge-dds -d 4
``` 

- Terminal 3

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
zenoh-bridge-dds -d 6
``` 

- Terminal 4

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
ROS_DOMAIN_ID=6 ros2 run examples_rclcpp_minimal_subscriber subscriber_member_function
``` 

### Service

- Terminal 1

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
ROS_DOMAIN_ID=4 ros2 run examples_rclcpp_minimal_service service_main
``` 

- Terminal 2

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
zenoh-bridge-dds -d 4
``` 

- Terminal 3

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
zenoh-bridge-dds -d 6
``` 

- Terminal 4

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
ROS_DOMAIN_ID=6 ros2 run examples_rclcpp_minimal_client client_main
``` 

### Action

- Terminal 1

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
ROS_DOMAIN_ID=4 ros2 run examples_rclcpp_minimal_action_server action_server_member_functions
``` 

- Terminal 2

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
zenoh-bridge-dds -d 4
``` 

- Terminal 3

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
zenoh-bridge-dds -d 6
``` 

- Terminal 4

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
ROS_DOMAIN_ID=6 ros2 run examples_rclcpp_minimal_action_client action_client_member_functions
``` 

### TLS

- Terminal 1

```bash
cd /tmp/
mkdir zenoh
cd zenoh
mkdir minica
cd minica
minica localhost
cd ..
touch router.json
touch client.json
```

```bash
gedit router.json
``` 

then paste the below code

```json
{
  mode: "router",
  listen: {
    endpoints: [ "tls/localhost:7447" ]
  },
  transport: {
    link: {
      tls: {
        server_private_key: "/tmp/zenoh/minica/localhost.key",
        server_certificate: "/tmp/zenoh/minica/localhost.crt",
      },
    },
  },
}
``` 

```bash
gedit client.json
``` 

then paste the below code

```json
{
  mode: "client",
  connect: {
    endpoints: [ "tls/localhost:7447" ]
  },
  transport: {
    link: {
      tls: {
        root_ca_certificate: "/tmp/zenoh/minica/cacert.crt",
      },
    },
  },
}
``` 

- Terminal 2

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
zenohd -c /tmp/zenoh/router.json
``` 

- Terminal 3

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
ROS_DOMAIN_ID=4 ros2 run examples_rclcpp_minimal_publisher publisher_member_function
``` 

- Terminal 4

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
export RUST_LOG=info
zenoh-bridge-dds -c /tmp/zenoh/client.json -d 4
``` 

- Terminal 5

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
export RUST_LOG=info
zenoh-bridge-dds -c /tmp/zenoh/client.json -d 6
``` 

- Terminal 6

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
ROS_DOMAIN_ID=6 ros2 run examples_rclcpp_minimal_subscriber subscriber_member_function
``` 

- Terminal 7

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
export RUST_LOG=info
zenoh-bridge-dds -d 8 -m client
``` 

- Terminal 8

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
ROS_DOMAIN_ID=8 ros2 run examples_rclcpp_minimal_subscriber subscriber_member_function
``` 


### mTLS

- Terminal 1

```bash
cd /tmp/
mkdir zenoh
cd zenoh
mkdir client
cd client
minica localhost
cd ..
mkdir server
cd server
minica localhost
cd ..
touch router.json
touch client.json
```

```bash
gedit router.json
``` 

then paste the below code

```json
{
  mode: "router",
  listen: {
    endpoints: [ "tls/localhost:7447" ]
  },
  transport: {
    link: {
      tls: {
        root_ca_certificate: "/tmp/zenoh/client/cacert.crt",
        client_auth: true,
        server_private_key: "/tmp/zenoh/server/localhost.key",
        server_certificate: "/tmp/zenoh/server/localhost.crt",
      },
    },
  },
}
``` 

```bash
gedit client.json
``` 

then paste the below code

```json
{
  mode: "client",
  connect: {
    endpoints: [ "tls/localhost:7447" ]
  },
  transport: {
    link: {
      tls: {
        root_ca_certificate: "/tmp/zenoh/server/cacert.crt",
        client_auth: true,
        client_private_key: "/tmp/zenoh/client/localhost.key",
        client_certificate: "/tmp/zenoh/client/localhost.crt",
      },
    },
  },
}
``` 

- Terminal 2

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
zenohd -c /tmp/zenoh/router.json
``` 

- Terminal 3

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
ROS_DOMAIN_ID=4 ros2 run examples_rclcpp_minimal_publisher publisher_member_function
``` 

- Terminal 4

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
export RUST_LOG=info
zenoh-bridge-dds -c /tmp/zenoh/client.json -d 4
``` 

- Terminal 5

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
export RUST_LOG=info
zenoh-bridge-dds -c /tmp/zenoh/client.json -d 6
``` 

- Terminal 6

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
ROS_DOMAIN_ID=6 ros2 run examples_rclcpp_minimal_subscriber subscriber_member_function
``` 

- Terminal 7

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
export RUST_LOG=info
zenoh-bridge-dds -d 8 -m client
``` 

- Terminal 8

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
ROS_DOMAIN_ID=8 ros2 run examples_rclcpp_minimal_subscriber subscriber_member_function
``` 


### user_password

- Terminal 1

```bash
cd /tmp/
mkdir zenoh
cd zenoh
touch router.json
touch client1.json
touch client2.json
touch client3.json
touch credentials.txt
```

```bash
gedit credentials.txt
``` 

then paste the below code

```bash
clientusername1:clientpassword1
clientusername2:clientpassword2
``` 

```bash
gedit router.json
``` 

then paste the below code

```json
{
  mode: "router",
  transport: {
    auth: {
      usrpwd: {
        user: "routerusername",
        password: "routerpassword",
        dictionary_file: "/home/abhijeet/zenoh/credentials.txt",
      },
    },
  },
}
``` 

```bash
gedit client1.json
``` 

then paste the below code

```json
{
  mode: "client",
  transport: {
    auth: {
      usrpwd: {
        user: "clientusername1",
        password: "clientpassword1",
      },
    },
  },
}
```

```bash
gedit client2.json
``` 

then paste the below code

```json
{
  mode: "client",
  transport: {
    auth: {
      usrpwd: {
        user: "clientusername2",
        password: "clientpassword2",
      },
    },
  },
}
```

```bash
gedit client3.json
``` 

then paste the below code

```json
{
  mode: "client",
  transport: {
    auth: {
      usrpwd: {
        user: "clientusername3",
        password: "clientpassword3",
      },
    },
  },
}
```

- Terminal 2

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
zenohd -c /tmp/zenoh/router.json
``` 

- Terminal 3

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
ROS_DOMAIN_ID=4 ros2 run examples_rclcpp_minimal_publisher publisher_member_function
``` 

- Terminal 4

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
export RUST_LOG=info
zenoh-bridge-dds -c /tmp/zenoh/client1.json -d 4
``` 

- Terminal 5

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
export RUST_LOG=info
zenoh-bridge-dds -c /tmp/zenoh/client2.json -d 6
``` 

- Terminal 6

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
ROS_DOMAIN_ID=6 ros2 run examples_rclcpp_minimal_subscriber subscriber_member_function
``` 

- Terminal 7

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
export RUST_LOG=info
zenoh-bridge-dds -c /tmp/zenoh/client3.json -d 8 
``` 

- Terminal 8

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
ROS_DOMAIN_ID=8 ros2 run examples_rclcpp_minimal_subscriber subscriber_member_function
``` 



### Reference

https://www.youtube.com/watch?v=5ZR3zw_luc4

https://vimeo.com/769972405

https://zenoh.io/blog/2021-04-28-ros2-integration/

https://github.com/eclipse-zenoh/zenoh-plugin-dds/#trying-it-out

https://zenoh.io/docs/manual/tls/

https://zenoh.io/docs/manual/user-password/

https://github.com/orgs/eclipse-zenoh/projects/2/views/4

https://www.youtube.com/watch?v=1NE8cU72frk

https://www.youtube.com/watch?v=9h01_MSKPS0

https://cyclonedds.io/docs/cyclonedds/latest/security.html

https://fast-dds.docs.eprosima.com/en/latest/fastdds/security/auth_plugin/auth_plugin.html
https://fast-dds.docs.eprosima.com/en/v2.9.1/fastdds/ros2/ros2_configure.html



