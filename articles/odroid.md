# Overview
This article is a short guide about how to get started with a [Odroid single-board computer](http://www.hardkernel.com/main/main.php). In this example, we are using an ODROID-C1 and working from a Mac OS computer.  

# Flashing Odroid OS on a blank SD Card

## Getting the OS image
The images can be downloaded from [this Odroid wiki page](http://odroid.com/dokuwiki/doku.php?id=en:c1_release_linux_ubuntu). In our example, we will get the version 14.4.2 from [here](http://east.us.odroid.in/ubuntu_14.04lts/ubuntu-14.04.2lts-lubuntu-odroid-c1-20150401.img.xz).

```
cd ~/
curl -O http://east.us.odroid.in/ubuntu_14.04lts/ubuntu-14.04.2lts-lubuntu-odroid-c1-20150401.img.xz
```

Once the image has been downloaded, verify its integrity using the md5 checksum. Just use the identical url as above and add `.md5sum` extension.

```
$ curl http://east.us.odroid.in/ubuntu_14.04lts/ubuntu-14.04.2lts-lubuntu-odroid-c1-20150401.img.xz.md5sum

59c0b5fb9089bbfca337ad2fbd063105  ubuntu-14.04.2lts-lubuntu-odroid-c1-20150401.img.xz
```

Now you can compare the md5 hash of the downloaded image file to the value provided in the md5sum file. Just use the following command on the image file and compare with the output above.

```
$ md5 ubuntu-14.04.2lts-lubuntu-odroid-c1-20150401.img.xz

MD5 (ubuntu-14.04.2lts-lubuntu-odroid-c1-20150401.img.xz) = 59c0b5fb9089bbfca337ad2fbd063105
```

From there, you will need to uncompress the xz file. You can use [homebrew](http://brew.sh/) to install the xz tool.

```
brew install xz
```

Now extract the image from the compressed file that you just downloaded

```
xz ubuntu-14.04.2lts-lubuntu-odroid-c1-20150401.img.xz --decompress
```

> Add the `--keep` flag to keep the input file. By default the compressed file is deleted.

The image is now ready to be copied to the SD card.

## Flashing the SD Card

List all the filesystems mounted on the computer. This will show a list of filesystems that are mounted on the computer.
```
$ df -h

Filesystem                          Size   Used  Avail Capacity  iused    ifree %iused  Mounted on
/dev/disk0s2                       232Gi  160Gi   72Gi    69% 41899663 18940079   69%   /
devfs                              180Ki  180Ki    0Bi   100%      625        0  100%   /dev
map -hosts                           0Bi    0Bi    0Bi   100%        0        0  100%   /net
map auto_home                        0Bi    0Bi    0Bi   100%        0        0  100%   /home

```

Now insert the SD card in your computer and run the same command again.

```
$ df -h

Filesystem                          Size   Used  Avail Capacity  iused    ifree %iused  Mounted on
/dev/disk0s2                       232Gi  160Gi   72Gi    69% 41899761 18939981   69%   /
devfs                              182Ki  182Ki    0Bi   100%      631        0  100%   /dev
map -hosts                           0Bi    0Bi    0Bi   100%        0        0  100%   /net
map auto_home                        0Bi    0Bi    0Bi   100%        0        0  100%   /home
/dev/disk1s1                        14Gi  2.4Mi   14Gi     1%        0        0  100%   /Volumes/UNTITLED
```

You can see here a new line - `/dev/disk1s1/` - which is a partition on the SD card I just insterted. You can also verify that the size (14Gi in our case) corresponds to the size printed on the SD card itself.

Now that we have found the filesystem, we can determine which device is the card itself. That's fairly easy, `disk1s1` becomes `rdisk1`. 

In order to flash the card, the partition need to be unmounted
```
sudo diskutil unmount /dev/disk1s1
```

You can now proceed with flashing of the card

> DANGER Always double check that you are flashing the correct drive. The `dd` command could erase your HD if wrongly used!

The `dd` command will flah the given drive

```
# WARNING - Make sure to change the of=/dev/rdisk1 to match YOUR SD CARD!

sudo dd if=ubuntu-14.04.2lts-lubuntu-odroid-c1-20150401.img of=/dev/rdisk1 bs=1m

```
This process takes a few minutes and should output something similar to this if successful

```
4689+0 records in
4689+0 records out
4916772864 bytes transferred in 474.193160 secs (10368713 bytes/sec)
```

Now eject the drive and insert it in the ODROID device.

```
$ sudo diskutil eject /dev/rdisk1

Disk /dev/rdisk1 ejected
```

# Connecting to the ODROID-C1
In order to log into the ODROID you will need to find which IP address it has been assigned. You can find the IP address by connecting to your router and checking which addresses have been assigned to the device (It should be called odroid).

Once you have the IP address, you can connect using SSH

```
ssh odroid@<ip-address>
```
> note that the default password is "odroid"

# Install vim editor
For editing text/configuration files, you will need to install vim editor.  Use the following command to update apt-get and install vim.

```
sudo apt-get update && sudo apt-get install vim
```

# Setting a static IP address for the ODROID
By default the network configuration of the ODROID is set to use DHCP (Dynamic IP address). The problem is that you will need to look for the IP address each time you want to connect to the device. This can be changed to have a static IP address.

First make sure you have vim installed (see above), and open `/etc/network/interfaces`.

```
sudo vi /etc/network/interfaces
```

Add the following lines to the file, save and exit
```
auto eth0
iface eth0 inet static
address 192.168.0.100
netmask 255.255.255.0
gateway 192.168.0.1
```

> Change the address, netmask and gateway so that it corresponds to your network configuration

You can now reboot your ODROID

```
sudo reboot
```

Once it has rebooted, you should be able to ssh using the new IP address
```
ssh odroid@192.168.0.100
```
