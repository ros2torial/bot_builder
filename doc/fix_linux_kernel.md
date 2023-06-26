Sometimes it may happen that the device like CAN etc. may requires fixed kernel(for which it was compiled) or if the device doesn't support latest kernel or we want to fix linux kernel for a product. then we have to set the default kernel 

Open grub file

```bash
sudo gedit /etc/default/grub
```

And change the **GRUB_DEFAULT=0** to 
```bash
GRUB_DEFAULT=”Advanced options for Ubuntu>Ubuntu, with Linux 4.15.0-135-generic” 
```

Find the exact kernel name in the grub option when you start the system.
here example for **Linux 4.15.0-135** kernel is mentioned. Replace it with the kernel version needed.  
Then type
```bash
sudo update-grub
```
then reboot


