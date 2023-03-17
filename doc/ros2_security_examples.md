
# ros2 security examples

It contains examples of ros2 security implementations.

### Specific connection restriction

We can restrict the nodes in the ros2 securiy i.e. only nodes with name "talker" and "listener" will work with this security.
We can restrict whether a perticular node will publish or subscribe and the topic name also.

- Terminal 1

```bash
ros2 run demo_nodes_cpp talker
``` 

- Terminal 2

```bash
ros2 run demo_nodes_py listener
``` 

- Terminal 3

```bash
cd /tmp/
mkdir security
cd security
ros2 security generate_policy abc_policy.xml
ros2 security create_keystore abc_keystore
ros2 security generate_artifacts -k abc_keystore -p abc_policy.xml
``` 

then kill talker and listerner

- Terminal 1

```bash
export ROS_SECURITY_ENABLE=true
export ROS_SECURITY_STRATEGY=Enforce
export ROS_SECURITY_ROOT_DIRECTORY=/tmp/security/abc_keystore
export ROS_SECURITY_LOOKUP_TYPE=MATCH_PREFIX
export ROS_SECURITY_KEYSTORE=/tmp/security/abc_keystore
ros2 run demo_nodes_cpp talker
``` 

- Terminal 2

```bash
export ROS_SECURITY_ENABLE=true
export ROS_SECURITY_STRATEGY=Enforce
export ROS_SECURITY_ROOT_DIRECTORY=/tmp/security/abc_keystore
export ROS_SECURITY_LOOKUP_TYPE=MATCH_PREFIX
export ROS_SECURITY_KEYSTORE=/tmp/security/abc_keystore
ros2 run demo_nodes_cpp listener
``` 

### Security for all nodes

- Terminal 1

```bash
ros2 run demo_nodes_cpp talker
``` 

- Terminal 2

```bash
cd /tmp/
mkdir security
cd security
ros2 security generate_policy tb_policy.xml
ros2 security create_keystore tb_keystore
``` 

then open tb_policy.xml and remove talker profile and add

```xml
<profile node="_ros2cli" ns="/">
  <services reply="ALLOW" request="ALLOW">
    <service>*</service>
  </services>
  <topics publish="ALLOW" subscribe="ALLOW">
    <topic>*</topic>
  </topics>
  <actions call="ALLOW" execute="ALLOW">
    <action>*</action>
  </actions>
</profile>
``` 

- Terminal 2

```bash
ros2 security generate_artifacts -k tb_keystore -p tb_policy.xml
``` 

- Terminal 3

```bash
export ROS_SECURITY_ENABLE=true
export ROS_SECURITY_STRATEGY=Enforce
export ROS_SECURITY_ROOT_DIRECTORY=/tmp/security/tb_keystore
export ROS_SECURITY_LOOKUP_TYPE=MATCH_PREFIX
export ROS_SECURITY_KEYSTORE=/tmp/security/tb_keystore
export TURTLEBOT3_MODEL=waffle
export GAZEBO_MODEL_PATH=$GAZEBO_MODEL_PATH:/opt/ros/humble/share/turtlebot3_gazebo/models
ros2 launch nav2_bringup tb3_simulation_launch.py headless:=True
``` 

you will observe that ros2 node list or ros2 topic list doesn't show anything

### Security across multiple domains

- Terminal 1

```bash
ros2 run demo_nodes_cpp talker
``` 

- Terminal 2

```bash
cd /tmp/
mkdir security
cd security
ros2 security generate_policy md_policy.xml
ros2 security create_keystore md_keystore
``` 

then open md_policy.xml and remove talker profile and add

```xml
<profile node="_ros2cli" ns="/">
  <services reply="ALLOW" request="ALLOW">
    <service>*</service>
  </services>
  <topics publish="ALLOW" subscribe="ALLOW">
    <topic>*</topic>
  </topics>
  <actions call="ALLOW" execute="ALLOW">
    <action>*</action>
  </actions>
</profile>
``` 

- Terminal 2

modify md_keystore/enclaves/permissions.xml in 2 places

```xml
<domains>
  <id_range>
	 <min>0</min>
	 <max>100</max>
  </id_range>
</domains>
```

modify md_keystore/enclaves/governance.xml

```xml
<domains>
  <id_range>
	 <min>0</min>
	 <max>100</max>
  </id_range>
</domains>
``` 

```bash
cd /tmp/security/md_keystore/enclaves
openssl smime -sign -text -in permissions.xml -out permissions.p7s -signer permissions_ca.cert.pem -inkey /tmp/security/md_keystore/private/permissions_ca.key.pem
openssl smime -sign -text -in governance.xml -out governance.p7s -signer permissions_ca.cert.pem -inkey /tmp/security/md_keystore/private/permissions_ca.key.pem
```

- Terminal 3

```bash
export ROS_SECURITY_ENABLE=true
export ROS_SECURITY_STRATEGY=Enforce
export ROS_SECURITY_ROOT_DIRECTORY=/tmp/security/md_keystore
export ROS_SECURITY_LOOKUP_TYPE=MATCH_PREFIX
export ROS_SECURITY_KEYSTORE=/tmp/security/md_keystore
export TURTLEBOT3_MODEL=waffle
export GAZEBO_MODEL_PATH=$GAZEBO_MODEL_PATH:/opt/ros/humble/share/turtlebot3_gazebo/models
export ROS_DOMAIN_ID=3
ros2 launch nav2_bringup tb3_simulation_launch.py headless:=True
``` 

### Enabling security through FastDDS profiles

- Terminal 1

```bash
ros2 run demo_nodes_cpp talker
``` 

- Terminal 2

```bash
cd /tmp/
mkdir security
cd security
ros2 security generate_policy fd_policy.xml
ros2 security create_keystore fd_keystore
``` 

then open fd_policy.xml and remove talker profile and add

```xml
<profile node="_ros2cli" ns="/">
  <services reply="ALLOW" request="ALLOW">
    <service>*</service>
  </services>
  <topics publish="ALLOW" subscribe="ALLOW">
    <topic>*</topic>
  </topics>
  <actions call="ALLOW" execute="ALLOW">
    <action>*</action>
  </actions>
</profile>
``` 

```bash
touch secure_config_fastdds.xml
gedit secure_config_fastdds.xml
``` 

then paste the below code

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<dds>
	<profiles xmlns="http://www.eprosima.com/XMLSchemas/fastRTPS_Profiles" >
	
		<participant profile_name="secure_domainparticipant_conf_auth_plugin_xml_profile" is_default_profile="true">
			<rtps>
				<propertiesPolicy>
				    <properties>
				    
				        <!-- Activate DDS:Auth:PKI-DH plugin -->
				        <property>
				            <name>dds.sec.auth.plugin</name>
				            <value>builtin.PKI-DH</value>
				        </property>
				        <!-- Configure DDS:Auth:PKI-DH plugin -->
				        <property>
				            <name>dds.sec.auth.builtin.PKI-DH.identity_ca</name>
				            <value>file:///tmp/security/fb_keystore/enclaves/identity_ca.cert.pem</value>
				        </property>
				        <property>
				            <name>dds.sec.auth.builtin.PKI-DH.identity_certificate</name>
				            <value>file:///tmp/security/fb_keystore/enclaves/cert.pem</value>
				        </property>
				        <property>
				            <name>dds.sec.auth.builtin.PKI-DH.private_key</name>
				            <value>file:///tmp/security/fb_keystore/enclaves/key.pem</value>
				        </property>
				        
				        <!-- Activate DDS:Access:Permissions plugin -->
				        <property>
				            <name>dds.sec.access.plugin</name>
				            <value>builtin.Access-Permissions</value>
				        </property>
				        <!-- Configure DDS:Access:Permissions plugin -->
				        <property>
				            <name>dds.sec.access.builtin.Access-Permissions.permissions_ca</name>
				            <value>file:///tmp/security/fb_keystore/enclaves/permissions_ca.cert.pem</value>
				        </property>
				        <property>
				            <name>dds.sec.access.builtin.Access-Permissions.governance</name>
				            <value>file:///tmp/security/fb_keystore/enclaves/governance.p7s</value>
				        </property>
				        <property>
				            <name>dds.sec.access.builtin.Access-Permissions.permissions</name>
				            <value>file:///tmp/security/fb_keystore/enclaves/permissions.p7s</value>
				        </property>
				        
				        <!-- Activate DDS:Crypto:AES-GCM-GMAC plugin -->
				        <property>
				            <name>dds.sec.crypto.plugin</name>
				            <value>builtin.AES-GCM-GMAC</value>
				        </property>
				        <!-- Only if DDS:Access:Permissions plugin is not enabled -->
				        <!-- Configure DDS:Crypto:AES-GCM-GMAC plugin -->
				        <property>
				            <name>rtps.participant.rtps_protection_kind</name>
				            <value>ENCRYPT</value>
				        </property>
				        
				    </properties>
				</propertiesPolicy>
			</rtps>
		</participant>

	</profiles>
</dds>
``` 

- Terminal 3

```bash
export RMW_IMPLEMENTATION=rmw_fastrtps_cpp
export FASTRTPS_DEFAULT_PROFILES_FILE=/tmp/security/secure_config_fastdds.xml
ros2 run demo_nodes_cpp talker
``` 

- Terminal 4

```bash
export RMW_IMPLEMENTATION=rmw_fastrtps_cpp
export FASTRTPS_DEFAULT_PROFILES_FILE=/tmp/security/secure_config_fastdds.xml
ros2 run demo_nodes_cpp listener
``` 

- Terminal 5

```bash
export RMW_IMPLEMENTATION=rmw_fastrtps_cpp
ros2 run demo_nodes_cpp listener
``` 

To check fastrtps version
```bash
dpkg -l | grep fastrtps
``` 

### Enabling security through CycloneDDS profiles

- Terminal 1

```bash
ros2 run demo_nodes_cpp talker
``` 

- Terminal 2

```bash
cd /tmp/
mkdir security
cd security
ros2 security generate_policy cd_policy.xml
ros2 security create_keystore cd_keystore
``` 

then open cd_policy.xml and remove talker profile and add

```xml
<profile node="_ros2cli" ns="/">
  <services reply="ALLOW" request="ALLOW">
    <service>*</service>
  </services>
  <topics publish="ALLOW" subscribe="ALLOW">
    <topic>*</topic>
  </topics>
  <actions call="ALLOW" execute="ALLOW">
    <action>*</action>
  </actions>
</profile>
``` 

```bash
touch secure_config_cyclonedds.xml
gedit secure_config_cyclonedds.xml
``` 

then paste the below code

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<CycloneDDS xmlns="https://cdds.io/config" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="https://cdds.io/config https://raw.githubusercontent.com/eclipse-cyclonedds/cyclonedds/master/etc/cyclonedds.xsd">
	<Domain id="any">
		<Security>
			<Authentication>
				<Library initFunction="init_authentication" finalizeFunction="finalize_authentication" path="dds_security_auth"/>
				<IdentityCA>file:/tmp/security/cb_keystore/enclaves/identity_ca.cert.pem</IdentityCA>
				<IdentityCertificate>file:/tmp/security/cb_keystore/enclaves/cert.pem</IdentityCertificate>
				<PrivateKey>file:/tmp/security/cb_keystore/enclaves/key.pem</PrivateKey>
			</Authentication>
			<Cryptographic>
				<Library initFunction="init_crypto" finalizeFunction="finalize_crypto" path="dds_security_crypto"/>
			</Cryptographic>
			<AccessControl>
				<Library initFunction="init_access_control" finalizeFunction="finalize_access_control" path="dds_security_ac"/>
				<PermissionsCA>file:/tmp/security/cb_keystore/enclaves/permissions_ca.cert.pem</PermissionsCA>
				<Governance>file:/tmp/security/cb_keystore/enclaves/governance.p7s</Governance>
				<Permissions>file:/tmp/security/cb_keystore/enclaves/permissions.p7s</Permissions>
			</AccessControl>
		</Security>
	</Domain>
</CycloneDDS>
``` 

- Terminal 3

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
export CYCLONEDDS_URI=/tmp/security/secure_config_cyclonedds.xml
ros2 run demo_nodes_cpp talker
``` 

- Terminal 4

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
export CYCLONEDDS_URI=/tmp/security/secure_config_cyclonedds.xml
ros2 run demo_nodes_cpp listener
``` 

- Terminal 5

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
ros2 run demo_nodes_cpp listener
``` 

To check cyclonedds version
```bash
dpkg -l | grep cyclonedds
``` 

### Reference

https://docs.ros.org/en/humble/Tutorials/Advanced/Security/Introducing-ros2-security.html

https://docs.ros.org/en/humble/Tutorials/Advanced/Security/Access-Controls.html

https://github.com/ros-swg/turtlebot3_demo

https://arxiv.org/pdf/2208.02615.pdf

https://github.com/open-rmf/rmf_demos/blob/main/docs/secure_office_world.md

https://www.omg.org/spec/DDS-SECURITY/1.1/PDF

https://fast-dds.docs.eprosima.com/en/v2.6.4/fastdds/security/security.html

https://cyclonedds.io/docs/cyclonedds/latest/security/example_configuration.html?highlight=security
https://github.com/eclipse-cyclonedds/cyclonedds/blob/master/docs/manual/_static/security_by_config.xml
