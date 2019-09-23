# Automated Habitat

## Project to automate Rabbit hutch

### Getting started

This project is using a Raspberry Pi 3. The Raspberry Pi 4 is available as we start the project, however it is overkill to spend out on new Pi 4 when a Pi 3 will suffice.

Raspberry Pi 3 setup:

Firstly, download and write rasbian buster lite
Install the following packages:

Change user ```pi``` password
```shell
sudo passwd pi
```
Change hostname in the following files and then reboot:
```shell
/etc/hostname
/etc/hosts
```

Configure SSH:
```shell
sudo systemctl enable ssh
sudo systemctl start ssh
```
Generate SSH key pair for remote machine:
```shell
ssh-keygen -t rsa -b 4096 -C <some-tag-value-maybe-email>
```
Copy public key to Raspberry Pi for passwordless authentication.
I didn't use this method but can do it like this:
```shell
cat ~/.ssh/id_rsa.pub | ssh user@machine "mkdir ~/.ssh; cat >> ~/.ssh/authorized_keys"
```

Install the following packages:
```shell
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install -y vim
sudo apt-get install -y git
```

Enable camera modue:
```shell
sudo raspi-config
```
Scroll down to ```5 Interfacing Options``` and click ```Enter```.
Select ```P1 Camera``` and click ```Enter```.
Click ```<Yes>``` to question: ```Would you like the camera interface to be enabled?```.
You will see ```The camera interface is enabled```.
Click ```<Ok>``` and ```<Finish>```.
Now the camera should be enabled so it's time to give it a test.
Take a still picture and copy it off the pi to view on your favourite OS:
```shell
raspistill -o image.jpg
```
From another computer running Windows or MacOS:
```shell
scp pi@<pi-hostname>:/home/pi/image.jpg <local-path-destination>
```

### Project overview

To start with this project will simply enable a standard (none night vision) camera with single lense.
The camera feed needs to be made available to a web endpoint allowing any user on the home network to view the feed.
However, the feed must not to be opened up to the internet other than via a VPN connection.

Any code required for this project as it progresses will be written in either Python or Ruby.

Later on in the project it may be necessary to connect external devices to the project. In which case the profered HW is an Arduino board (as I have several knocking around in drawers).

### Wish list

* configuration of Raspberry Pi using Chef