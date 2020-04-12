# Automated Habitat

## Project to automate Rabbit hutch

### Getting started

This project is using a Raspberry Pi 3. The Raspberry Pi 4 is available as we start the project, however it is overkill to spend out on new Pi 4 when a Pi 3 will suffice.

Raspberry Pi 3 setup:

Firstly, [download](https://www.raspberrypi.org/downloads/raspbian/) and write rasbian buster lite.
Install the following packages - [official instructions](https://www.raspberrypi.org/documentation/installation/installing-images/README.md):

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
ssh-keygen -t rsa -b 4096 -C &lt;some-tag-value-maybe-email&gt;
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
Click ```&lt;Yes&gt;``` to question: ```Would you like the camera interface to be enabled?```.
You will see ```The camera interface is enabled```.
Click ```&lt;Ok&gt;``` and ```&lt;Finish&gt;```.
Now the camera should be enabled so it's time to give it a test.
Take a still picture and copy it off the pi to view on your favourite OS:
```shell
raspistill -o image.jpg
```
From another computer running Windows or MacOS:
```shell
scp pi@<pi-hostname>:/home/pi/image.jpg <local-path-destination>
```

Installing Motion package and activate official driver on Pi - [reference](https://hackernoon.com/spy-your-pet-with-a-raspberry-pi-camera-server-e71bb74f79ea): 
```shell
sudo apt-get install motion
sudo modprobe bcm2835-v4l2
```

Persist the driver
```shell
sudo echo "bcm2835-v4l2" >> /etc/modules
```

Set camera as daemon process that starts automatically.
Upate file ```/etc/default/motion``` by setting ```start_motion_daemon=yes```.

Configure Motion by making the following updates to file ```/etc/motion/motion.conf```:
```shell
# create a backup before we start
sudo cp /etc/motion/motion.conf /etc/motion/motion.conf.bak
```

```shell
daemon on
logfile /tmp/motion.log
stream_localhost off
output_pictures off 
ffmpeg_output_movies off
framerate 100
width 640
height 480
webcontrol_port 8081
```

Start the Motion service
```shell
sudo service motion start
```

Now the service should be running and a live feed should be available at: http://&lt;service-ip-address&gt;:8081.
If it's notworking (as with my first 4 attempts), view the log file (```/tmp/motion.log```) and debug your motion configuration.

Update Message of the day using [http://www.kammerl.de/ascii/AsciiSignature.php](http://www.kammerl.de/ascii/AsciiSignature.php).
Font: ```flowerpot```
Output:
```
.-------.       ____     _______    _______  .-./`) ,---------.            .-------. .-./`) 
|  _ _   \    .'  __ `. \  ____  \ \  ____  \\ .-.')\          \           \  _(`)_ \\ .-.')
| ( ' )  |   /   '  \  \| |    \ | | |    \ |/ `-' \ `--.  ,---'           | (_ o._)|/ `-' \
|(_ o _) /   |___|  /  || |____/ / | |____/ / `-'`"`    |   \  _ _    _ _  |  (_,_) / `-'`"`
| (_,_).' __    _.-`   ||   _ _ '. |   _ _ '. .---.     :_ _: ( ' )--( ' ) |   '-.-'  .---. 
|  |\ \  |  |.'   _    ||  ( ' )  \|  ( ' )  \|   |     (_I_)(_{;}_)(_{;}_)|   |      |   | 
|  | \ `'   /|  _( )_  || (_{;}_) || (_{;}_) ||   |    (_(=)_)(_,_)--(_,_) |   |      |   | 
|  |  \    / \ (_ o _) /|  (_,_)  /|  (_,_)  /|   |     (_I_)              /   )      |   | 
''-'   `'-'   '.(_,_).' /_______.' /_______.' '---'     '---'              `---'      '---' 
```

Create file `/etc/motd` containing the ascii text above.

Username: `pi`




### Project overview

To start with this project will simply enable a standard (none night vision) camera with single lense.
The camera feed needs to be made available to a web endpoint allowing any user on the home network to view the feed.
However, the feed must not to be opened up to the internet other than via a VPN connection.

Any code required for this project as it progresses will be written in either Python or Ruby.

Later on in the project it may be necessary to connect external devices to the project. In which case the profered HW is an Arduino board (as I have several knocking around in drawers).

### Wish list

* configuration of Raspberry Pi using ~~Chef~~ Ansible