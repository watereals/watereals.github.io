---
layout: default
title: Anaconda
---

# Install Software

## Software source

some source list:
mirrors.tuna.tsinghua.edu.cn
mirror.bjtu.edu.cn
mirrors.163.com

* Change source configure

```bash
sudo cp /etc/apt/sources.list /etc/apt/sources.list.old
sudo sed -i 's/us.archive.ubuntu.com/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list
sudo sed -i 's/cn.archive.ubuntu.com/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list
sudo apt-get update
```

* Install some common software

```bash
sudo apt-get install openssh-server
sudo apt-get install unzip
sudo apt-get install lrzsz
sudo apt-get install vim
sudo apt-get install gcc
sudo apt-get install g++
```

## VMware Tools

* Mount VMware tools CD

```bash
sudo mkdir /mnt/cdrom
sudo mount /dev/cdrom /mnt/cdrom/
```

* Install VMware tools and share fold

```bash
cp /mnt/cdrom/VMwareTools-9.9.2-2496486.tar.gz .
tar xvfz VMwareTools-9.9.2-2496486.tar.gz
cd vmware-tools-distrib/
./vmware-install.pl
```

* Install open vm-tools

```bash
sudo apt-get install open-vm-tools
sudo apt-get install open-vm-tools-dkms
```

* Mount hgfs

```bash
sudo vmhgfs-fuse .host:/ /mnt/hgfs   					# can only listed by root
sudo vmhgfs-fuse -o allow_other .host:/ /mnt/hgfs 		# recommend
```

## MongoDB

https://docs.mongodb.com/master/tutorial/install-mongodb-on-ubuntu/

### Install MongoDB

Import the public key used by the package management system.

sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6

Create a list file for MongoDB.

Ubuntu 14.04
echo "deb [ arch=amd64 ] https://mirrors.tuna.tsinghua.edu.cn/mongodb/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list

Ubuntu 16.04
echo "deb [ arch=amd64 ] https://mirrors.tuna.tsinghua.edu.cn/mongodb/apt/ubuntu xenial/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list

Reload local package database.
sudo apt-get update

Install the MongoDB packages.
sudo apt-get install mongodb-org

### Run MongoDB Community Edition

The MongoDB instance stores its data files in /var/lib/mongodb and its log files in /var/log/mongodb by default, and runs using the mongodb user account. You can specify alternate log and data file directories in /etc/mongod.conf. See systemLog.path and storage.dbPath for additional information.

If you change the user that runs the MongoDB process, you must modify the access control rights to the /var/lib/mongodb and /var/log/mongodb directories to give this user access to these directories.

sudo service mongod start

tail -f /var/log/mongodb/mongod.log
[initandlisten] waiting for connections on port <port>
where <port> is the port configured in /etc/mongod.conf, 27017 by default.

sudo service mongod stop
sudo service mongod restart

Remove Packages.
sudo apt-get purge mongodb-org*

sudo rm -r /var/log/mongodb
sudo rm -r /var/lib/mongodb













conda env remove -n py27

conda create -n pyqt4 anaconda python=2.7 pyqt=4.11.4




