
# systemd

create a my-ros2-publisher.service file in /etc/systemd/system directory

```bash
sudo gedit /etc/systemd/system/my-ros2-publisher.service
```

then copy the below commands to paste it in my-ros2-publisher.service file

```bash
[Unit]
Description=ROS 2 Publisher

[Service]
Type=simple
Environment="HOME=root"
Environment="ROS_DOMAIN_ID=0"
Environment="ROS_LOG_DIR=/home/user_name/log"
User=user_name
WorkingDirectory=/home/user_name/
ExecStart=/bin/bash -c 'source /opt/ros/humble/setup.bash; ros2 topic pub -1 -w 0 /abc std_msgs/msg/String "data: \'abc\'"'

[Install]
WantedBy=multi-user.target
```

here we are running without root privilages **User=user_name** as root privilages causes ros2 publisher to be discovered only by root privilaged ros2 subscriber. Hence we are ruuning it with user_name privilage only. Also in order to run the ros2 commands through udev it is necessary to define HOME, ROS_DOMAIN_ID and ROS_LOG_DIR environment variables, otherwise ros2 command won't run.

get user_name value from running

```bash
whoami
```

then load the service and enable it. After that test it using *systemctl start*

```bash
sudo systemctl daemon-reload
sudo systemctl enable my-ros2-publisher.service
sudo systemctl start my-ros2-publisher.service
```


### To add other features


```bash
[Unit]
Description=ROS 2 Publisher

[Service]
Type=simple
Environment="HOME=root"
Environment="ROS_DOMAIN_ID=0"
Environment="ROS_LOG_DIR=/home/user_name/log"
User=user_name
WorkingDirectory=/home/user_name/
ExecStart=/bin/bash -c 'source /opt/ros/humble/setup.bash; ros2 topic pub -1 -w 0 /abc std_msgs/msg/String "data: \'abc\'"'
KillMode=mixed
KillSignal=SIGINT
RestartKillSignal=SIGINT
Restart=on-failure
RestartSec=30s

[Install]
WantedBy=multi-user.target
```
