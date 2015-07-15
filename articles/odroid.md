# Overview
This article is a short guide about how to get started with a [Odroid single-board computer](http://www.hardkernel.com/main/main.php). In this example, we are using an ODROID-C1 and working from a Mac OS computer.  

# Flashing Odroid OS on a blank SD Card

## Getting the OS image
The images can be downloaded from [this Odroid wiki page](http://odroid.com/dokuwiki/doku.php?id=en:c1_release_linux_ubuntu). In our example, we will get the version 1.4.2 from [here](http://east.us.odroid.in/ubuntu_14.04lts/ubuntu-14.04.2lts-lubuntu-odroid-c1-20150320.img.xz).

```
$ cd ~/
$ curl -O http://east.us.odroid.in/ubuntu_14.04lts/ubuntu-14.04.2lts-lubuntu-odroid-c1-20150320.img.xz
```

From there, you will need to uncompress the xz file. You can use [homebrew](http://brew.sh/) to install the xz tool.

```
$ brew install xz
```

From there extract the image file from the compressed file that you just downloaded

```
$ xz -d <filename.img.xz>
```
The image is now ready to be copied to the SD card.

## Flashing the SD Card

+ List all the filesystems mounted on the computer
```
$ df -h
```
This will show a list of filesystems that are mounted on the computer.

+ Insert the SD card in your computer
+ Check where the card has been mounted using
```
$ df -h
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
$ sudo diskutil unmount /dev/disk1s1
```
+ Flash the card
> DANGER Always double check that you are flashing the correct drive. The `dd` command could erase your HD if wrongly used!
The `dd` command will flah the given drive

```
$ sudo dd if=~/path/to/image/file of=/dev/rdisk1
```
This process takes a few minutes...

+ Once the process is complete, eject the device and insert it in the ODROID device.
```
$ sudo diskutil eject /dev/rdisk1
```
