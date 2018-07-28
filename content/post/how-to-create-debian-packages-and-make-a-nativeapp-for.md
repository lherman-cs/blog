+++
categories = []
date = "2018-01-19T16:50:33-04:00"
image = "/uploads/bench-accounting-49908.jpg"
tags = []
title = "How to create Debian packages and make a native app for any web app"
weight = 5
writer = "Lukas Herman"

+++
To intro, I am an undergraduate Computer Science student at Clemson University. I was having issues with printing to the Clemson network printers, Clemson has desktop printer client apps for macs and windows machines to be able to print to the network printers but only a web app to print from Linux machines, with no desktop app, therefore I wanted to make one to serve Debian Linux machines.

I found a wrapper class for being able to wrap most web apps into desktop apps  [jiahaog/nativefier](https://github.com/jiahaog/nativefier). This is an open source project on GitHub. I installed this locally and took the Clemson web app and wrapped it as a desktop app.

Then I wanted to be able to package this for anyone who uses a Debian Linux distro to install with a one-line command, which was surprisingly harder than getting the app itself to work.

In this post, I'll be talking about how to make a native app for any web app and package the app into a Debian package (.deb). Throughout this post, I'll be using [YouTube](https://www.youtube.com/) as the web app that I'll convert and package.

So, without further ado, let's get started.

![](/uploads/Screenshot-from-2018-01-18-14-39-20.png)
_Note: YouTube desktop app that we're trying to build_

***

# Dependencies

For the system dependency you just need to install npm and gnome-panel. Then, use npm to install [nativefier](https://github.com/jiahaog/nativefier) (a third party nodejs module).

_Install npm:_

```sh
sudo apt-get update && sudo apt-get install -y npm gnome-panel
```

_Note: If the installation failed, please refer to_ [_this official documentation_](https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions)

_Install nativefier:_

```sh
sudo npm install -g nativefier
```

***

# Steps how to convert any web app to a native app

1. Make a directory for the project (for this post, I'll name it "youtube". Feel free to use any name here)

   ```sh
   mkdir youtube
   ```
2. Change your directory into the newly created project folder

   ```sh
   cd youtube
   ```
3. Convert YouTube web app to a native app

   ```sh
   nativefier --name youtube https://youtube.com
   ```

***

# Steps how to package the native app

The hardest part of this section is we need to know how Linux organized applications in the filesystem. There are 3 directories that we need to understand for this particular post:

* **/usr/share/{package_name}**: our application directory. It contains everything from our application's shared objects, binary files, etc.
* **/usr/bin**: where our binary file is (we should make a link to the binary file in /usr/share/{package_name} if it's a dynamically compiled program)
* **/usr/share/applications**: where our .desktop file is (this is for making a desktop shortcut).

Understanding the structure is very important in packaging our application because all directories and files within our project folder will be copied relatively to the root. For example, if I have this project tree:

![](/uploads/Screenshot-from-2018-01-18-19-44-54.png)

**youtube/usr/bin** will be copied to **/usr/bin**.
**youtube/usr/share/applications** will be copied to **/usr/share/applications**.
...

And so on except **DEBIAN** which will make sense later.

To illustrate the concept, below are the specific steps I need to do for packaging our YouTube app.

 1. Make a directory for our application's shared objects, binary file, etc.

    ```sh
    mkdir -p usr/share
    ```
 2. Move the generated YouTube native app to the newly created directory:

    ```sh
    mv youtube-linux-x64 usr/share/youtube
    ```
 3. Change the permissions for some directories so that it can be read for all users

    ```sh
    chmod -R +rx usr/share/youtube/resources/app
    ```
 4. Make a directory for our application's binary file

    ```sh
    mkdir -p usr/bin
    ```
 5. Change directory to the newly created directory

    ```sh
    cd usr/bin
    ```
 6. Make a relative link to our project base's binary file

    ```sh
    ln -sr ../share/youtube/youtube youtube
    ```
 7. Change directory back to the project base directory

    ```sh
    cd ../..
    ```
 8. Make a directory for our application's .desktop file

    ```sh
    mkdir -p usr/share/applications
    ```
 9. Change directory to the newly created directory

    ```sh
    cd usr/share/applications
    ```
10. Create a .desktop file

```sh
gnome-desktop-item-edit --create-new .
```

![](/uploads/gnome-panel.png)

* Name: YouTube (the app's name that will be used as the desktop shortcut).
* Command: youtube (this should be pointing to the binary file).
* Comment: you can leave it blank.

1. Change directory back to the project base directory

```sh
cd ../../..
```

1. Make a directory for **DEBIAN** control (remember that I said all the directories and files will be copied except **DEBIAN**?). _Note: DEBIAN folder is used for our package configurations._

```sh
mkdir -p DEBIAN
```

1. Add **control** file into **DEBIAN** directory

```sh
vim DEBIAN/control
```

1. Add this into the file

    Package: youtube
    Version: 1.0
    Maintainer: Lukas Herman
    Architecture: amd64
    Description: YouTube Desktop App

1. Change directory back to one level before our project base directory

   ```sh
   cd ..
   ```
2. Package the YouTube app

   ```sh
   fakeroot dpkg-deb --build youtube
   ```
3. That's it! You'll see in your directory there is the debian package called **youtube.deb**.

***

# Extra

If you don't want to do any of these steps, I've made an automation script [here](https://github.com/lherman-cs/debian-nativefier). You just need to run the script and give necessary information. The script will automatically create a debian package of any converted web app.

Here's the one-line command line:

```sh
git clone git@github.com:lherman-cs/debian-nativefier.git && cd debian-nativefier && ./build.sh 
```