# Raspberry Pi DarkIce/IceCast Player
 A turnkey IceCast player solution for Raspberry Pi, perfect for vinyl or other streaming media.

 The goal of this collection of scripts and pre-made packages is to make it incredibly simple to create an m3u streaming endpoint on a Raspberry Pi that uses configurable audio from a supported interface.

 ## Requirements

- 1x Raspberry Pi 3B or higher model number (it plausibly works on the zeros or other single board ARM compatible solutions, i'm just lazy and only validated my own solution at this point)
- 1x compatible audio interface i.e. Behringer UFO202 to provide audio inputs
- Any required cabling etc. to support your solution
- Basic working knowledge of Linux command terminals and interfaces

## Thoughts

- Life is much easier by cloning this repo into a known folder, versions can be tracked and amendments just require an update from git
- You can substitute copying files with `ln -s` to soft link to the config files
- Don't try and create a dump temporary file in DarkIce. This causes a slow memory leak and will eventually crash DarkIce
- DarkIce is an average solution at best, but it works just fine for this solution
- IceCast can be replaced with ShoutCast or any other streaming server as required
- If you are creating multiple inputs, a centralised IceCast server is highly recommended for ease of use
- Don't use this solution anywhere you make money from it, it's a great little home solution but really not rock solid for production

## Preparing your Pi

1. Install whatever flavour of Linux you want on your Pi (My preference is just Raspbian without a desktop environment for simplicity but this works on any distro supporting .deb packages)
2. Change your pi user password and/or replace the user!!!
3. Update your Pi with the latest packages
4. Enable SSH for remote management (you'll thank me later if you need to give it a kick or it misbehaves)
5. Use a unique hostname for ease of access in home settings (or record the MAC and do some DHCP static addressing if you have a large number of devices on your network)

## Checking Your Audio

1. Connect your audio interface and ensure it's powered up
2. run `arecord -l` to get a printout of your audio devices that support recording.

````sh
**** List of CAPTURE Hardware Devices ****
card 2: CODEC [USB Audio CODEC], device 0: USB Audio [USB Audio]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
````

3. Make note of the card number (in this case it's 2). This will be used in the DarkIce Configuration

## Installing DarkIce

There are a wide range of articles on how to configure DarkIce, install it on a raspberry pi, and a number of separate options to build from source etc.

It should be noted that by default DarkIce doesn't include MP3 support.

For the purposes of this, there is a prebuilt.deb binary in the repo you can install

1. Download the darkice_1.3-1_armhf.deb package from GitHub using wget or curl
2. Run `dpkg -i %filename%` to install DarkIce
3. Download the DarkIce.sh script and copy to a suitable location
4. Run `chmod +x %path to file%` to enable execution
5. Run `chmod 755 %path to file%` to enable suitable permissions (this can be locked down further for better security)

If you want to enable hard mode, use the super helpful guide [HERE](https://www.linuxwolfpack.com/compile-darkice.php) to manually build the installer

## Installing IceCast2

1. Run `apt install icecast2` to install IceCast2 server
2. Configure IceCast2 and use the default configurations, these will be overwritten later

## Configuring the Solution

1. Download the darkice.cfg file from GitHub with wget or curl.
2. Open the config file with `sudo nano /etc/darkice.cfg` and replace the entire config file with what is provided from the GitHub sample. # Where suitable, change the variables to match your setup
3. In the config file, replace the card number in line 14 (Device Param) i.e. `device = plughw:1,0` in our configuration should be `device = plughw:2,0` to match hardware card 2
4. Download the icecast.xml from GitHub with wget or curl.
5. Open the config file with `sudo nano /etc/icecast2/icecast.xml` and replace the entire contents of the file with what is provided from the GitHub sample. # Where suitable, change the variables to match your setup

I would strong recommend changing items like passwords and hostnames to be more secure. Make sure you update these configuration lines in both the darkice and icecast2 config files.

## Testing The Solution

1. On the Pi, run the darkice.cfg file, i.e. `/home/pi/darkice.cfg` and leave the terminal window open
2. Navigate to http://DEVICE-IP:8000 on another device. You should see IceCast load and the stream be visible in the window. Clicking on M3U in the top right hand corner should start the stream.
3. If you aren't hearing anything, check your card settings are correct and any other settings are aligned.

## Automating on Reboot

1. Login to the Pi and run `sudo crontab -e`. If you haven't done this previously you'll need to select an editor, I recommend Nano.
2. Add the line `@reboot sleep 15 && /home/pi/darkice.sh` replacing /home/pi/darkice.sh with the correct file location.
3. Reboot the Pi and wait approximately 40 seconds for the Pi to load and the stream to become active. This should appear as a stream in http://DEVICE-IP:8000/
