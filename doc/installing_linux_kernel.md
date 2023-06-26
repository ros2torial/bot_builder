In order to downgrade the kernal from 5.4 to 4.15

```bash
cd /tmp/

wget http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.15/linux-headers-4.15.0-041500_4.15.0-041500.201802011154_all.deb

wget http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.15/linux-headers-4.15.0-041500-generic_4.15.0-041500.201802011154_amd64.deb

wget http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.15/linux-image-4.15.0-041500-generic_4.15.0-041500.201802011154_amd64.deb

sudo dpkg -i *.deb
```

find the exact kernel number from *http://kernel.ubuntu.com/~kernel-ppa/mainline/* link and change accordingly
