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
xz -d <filename.img.xz>
```
The image is now ready to be copied to the SD card.

## Flashing the SD Card

+ List all the filesystems mounted on the computer
```
df -h
```
This will show a list of filesystems that are mounted on the computer.

+ Insert the SD card in your computer
+ Check where the card has been mounted using
```
df -h
```
You should be able to compare the output with the previous output and determine which one is the SD card you just inserted.
```
Filesystem                          Size   Used  Avail Capacity  iused    ifree %iused  Mounted on
/dev/disk0s2                       232Gi  151Gi   81Gi    65% 39550536 21289206   65%   /
devfs                              186Ki  186Ki    0Bi   100%      642        0  100%   /dev
map -hosts                           0Bi    0Bi    0Bi   100%        0        0  100%   /net
map auto_home                        0Bi    0Bi    0Bi   100%        0        0  100%   /home
localhost:/3C2XlGbrKOBhdd8EBlhRl8  232Gi  232Gi    0Bi   100%        0        0  100%   /Volumes/MobileBackups
/dev/disk1s1                       2.9Gi  512Ki  2.9Gi     1%        0        0  100%   /Volumes/VFAT
```

In our case, the card we want to flash is the card where the partition `/dev/disk1s1` is located. Since we want to reference the card `disk1s1` relates to `rdisk1`, which we will use for flashing.

+ Unmount the SD card so that it can be flashed
```
sudo diskutil unmount /dev/disk1s1
```
+ Flash the card

> DANGER Always double check that you are flashing the correct drive. The `dd` command could erase your HD if wrongly used!
The `dd` command will flah the given drive

```
sudo dd if=path/to/image/file of=/dev/rdisk1 bs=1m
```
This process takes a few minutes...

+ Once the process is complete, eject the device and insert it in the ODROID device.

```
sudo diskutil eject /dev/rdisk1
```

# Connecting to the ODROID-C1
In order to log into the ODROID you will need to find which IP address it has been assigned. You can find the IP address by connecting to your router and checking which addresses have been assigned to the device (It should be called odroid).

Once you have the IP address, you can connect using SSH

```
ssh odroid@<ip-address>
```
> note that the default password is "odroid"

# Install vim editor
For editing text/configuration files, you will need to install vim editor. First update `apt-get`.

```
sudo apt-get update
```

Then install vim
```
sudo apt-get install vim
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
