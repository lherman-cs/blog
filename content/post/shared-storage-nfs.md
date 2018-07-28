+++
categories = []
date = "2017-12-29T02:57:47-04:00"
image = "/uploads/network_layout.png"
tags = []
title = "Shared Storage (NFS)"
weight = 5
writer = "Lukas Herman"

+++
At home, I have 2 cheap single boards: Rock64 and Raspberry pi 3. I've been using both of them as my servers. Today, I did a maintenance to both of my servers, including upgrading, restructuring the network, and add a **shared storage**. In this story, I will share step-by-step how I installed NFS shared storage to my server and also my thought process a little bit.

There are 3 popular shared storage protocols that I found on the internet:

1. SSH
2. SMB
3. NFS

Each of them has its own pros and cons. For example, using SSH protocol is great because it is flexible, secure, and available anywhere. However, it is the slowest protocol for transferring data (For more detail: [here](https://askubuntu.com/questions/289544/ssh-vs-smb-vs-nfs-for-gui-file-transfer)). So, before I decided which protocol that I wanted to use for my shared protocol, I thought about what I need from having a shared storage and what kind of network that these servers will be running on.

Finally, I decided to choose NFS because it's the fastest protocol and because I can trust my network.

***

## Network layout

![](/uploads/network_layout.png)

***

## Installation

_These installation steps are for Debian based Linux._

### Server

Install NFS server:

```sh
sudo apt-get update && sudo apt-get install -y nfs-kernel-server
```

Configure NFS:

```sh
sudo vim /etc/exports
```

**or if you don't have vim:**

```sh
sudo nano /etc/exports
```

Add a line for configuring a shared folder by following this format:

```sh
/path/to/your/shared/directory  hostname(options)
```

_For more detail about the configuration, you can find it by running this command:_

```sh
man exports
```

Example:

```sh
/mnt  *(rw,async,all_squash,anonuid=1000,anongid=1000)
```

* **/mnt**: the path where my shared folder located (note: _/mnt is usually being used for a temporary mount point_).
* \*: wildcard notation. It just means that it will allow all local IP addresses to access the shared folder.
* **rw**: give read/write access.
* **all_squash**: map the user id and the user's group id. This is usually combined with **anonuid** and **anongid** to specify the mapped UID and GID.
* **anonuid**: mapped UID
* **anongid**: mapped GID

Now, we have configured our NFS server. The next thing we need to do is to restart our NFS server so that it will use the new configuration. To do so, we need to run this command:

```sh
sudo /etc/init.d/nfs-kernel-server restart
```

Our server is now ready to accept users!

***

### Client

Install NFS client:

    sudo apt-get update && sudo apt-get install -y nfs-common

There are 2 ways of mounting the shared folder:
1\. Use "mount" command line **(temporary)**
2\. Add a configuration to /etc/fstab **(permanent)**

#### 1. Use "mount" command line **(temporary)**

Make a directory for the target mountpoint later:

```sh
mkdir mnt
```

Mount the shared folder to the directory:

```sh
mount 192.168.0.2:/mnt mnt
```

* **192.168.0.2**: my server IP address (look at the network layout). _You should use your own server's IP address._
* **/mnt**: my server's shared directory path.
* **mnt**: the target mountpoint (the directory that we just created).

We're done! However, this setup has to be repeated every time we restart our computers. For the permanent configuration, I'll discuss it in the next section.

### 2. Add a configuration to /etc/fstab **(permanent)**

Edit /etc/fstab using your favorite text editor:

```sh
sudo vim /etc/fstab
```

**or**

```sh
sudo nano /etc/fstab
```

Add a new line and add the following configuration:

```sh
192.168.0.2:/mnt mnt nfs defaults 0 0
```

* **192.168.0.2**: my server IP address (look at the network layout). _You should use your own server's IP address._
* **/mnt**: my server's shared directory path.
* **mnt**: the target mountpoint (the directory that we just created).
* **nfs**: filesystem type.
* **defaults**: default mount option.
* **0**: Enable or disable backing up of the device/partition (the command dump).
* **0**: Controls the order in which fsck checks the device/partition for errors at boot time.

For more info about **/etc/fstab**, you can find the official documentation from ubuntu [here](https://help.ubuntu.com/community/Fstab).

...And that's it! You just finished installing a shared storage using **NFS protocol**!