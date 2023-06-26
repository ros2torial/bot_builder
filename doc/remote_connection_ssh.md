### Remote Connection

On the Server side, run

```bash
sudo apt-get install openssh-server
```

Enable the ssh service by running

```bash
sudo systemctl enable ssh
```

Start the ssh service by running

```bash
sudo systemctl start ssh
```

On the Client side (Linux), run

```bash
sudo apt-get install openssh-client
```

Login into the server by running

```bash
ssh username@ip_address_of_server
```

On the Client side (Windows),
Install PuTTY
Open Putty and type the IP address of the client and click on Open
Then type the username and password

In order to access, read and write the files remotely 
Use SFTP

In linux
Go to Files then click on Other Locations then on the right bottom side type
sftp://ip_address_of_server
or
sftp://user_name@ip_address_of_server
you can also open the server terminal by right clicking here and selecting Open in Remote Terminal 

In Windows
Use WinSCP
Type host name, user name and password and then login. Now you can copy and paste document from client to server or vice versa.
