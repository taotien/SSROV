# SSROV
The code repository for all things needed to create an internet-controllable ROV. 
##### WARNING: This is a work-in progress!
TODO: 
1. Excel budget and inventory
2. Finish combining working modules
	1. Web interface:
		1. Buttons that call the cgi-bin scripts without refreshing or leaving the page
		2. Graphical rather than text overlay for data
		3. Options page to set streaming parameters in case badwidth isn't enough
3. Develop salinity sensor module
4. Purchase and test ethernet add-on board or USB through the GPIO. 
5. Package and tutorial around ethernet gadget mode (for Pi0 only)
6. List of apt packages required
7. Screenshots for everything

## Introduction
The internet ROV is powered by the budget Raspberry Pi Zero and common off-the-shelf components found that is built around the Raspberry Pi platform as well as basic programming. The goal of this project is to enable a remote user to access a ROV without being on the docks.

The remote user connects to a proxy server (a workaround to port-forwarding restrictions) that forwards all connections to the ROV server, which is onboard the ROV itself. All calculations are excecuted on the ROV itself. The user can see a live image of the ROV, an overlay of various data, and control using onscreen buttons or a keyboard. The onsite operator should be able to simply connect the ROV to a laptop and power source, connect to the internet, and run a script which handles all the initialisation of software, such that no configuration or programming is needed. 

### Hardware:
- Raspberry Pi Zero (Rasberry Pi Zero W preferred, soldered headers optional)
- 1x SD card for Raspberry Pi OS and software storage
- 4x [Motor control boards]
- 1x [Raspberry Pi Camera]
- 1x [OpenROV combo sensor]: Adds compass heading, orientation, pressure, and temperature data retrieval
- 1x [Salinity sensor]
- 1x Portable computer for configuration and tethering
- 1x Mobile hotspot

### Software:
- Latest Raspbian image
- [RPi Web Cam Control Interface]
- Adafruit Python libraries for sensors
- Customizations of software
- Remote server for SSH tunnel to serve data to user

# Simple setup (pre-built image):
1. Grab the image from [link].
2. Use [Etcher] to burn the image to an sd card.
3. Open *wpa_supplicant.conf* on the image and configure your network settings. See step 4 from the "Build from Scratch" section.
4. Save the file, and eject the SD card.
5. Insert the SD card into the Raspberry Pi.
6. Power on laptop and connect to mobile hotspot.
7. Connect Raspberry Pi to power source and laptop.
8. Run [initialization script]

# Build from scratch:

### Preparing the image:
1. Download latest Raspian image from Raspberry Pi's site [https://www.raspberrypi.org/downloads/raspbian/].
7. Burn the image to SD card using [Etcher] or [Win32DiskImager].
8. Create a file *wpa_supplicant.conf* (ignore filetype change warning). You can edit this file with Notepad.
9. Paste the following into the file, substituting **WIFI NAME** and **WIFI PASS** for the network you're using. (NOTE: only works with networks that don't require logging in, and be on the same network as the computer):

```javascript
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=US

network={
	ssid="WIFI NAME"
	psk="WIFI PASSWORD"
	scan_ssid=1
}
```

5. Move the *wpa_supplicant.conf* file to the SD card. ***DO NOT RUN A REPAIR WHEN WINDOWS COMPLAINS***. The correct drive should be named *boot*.
2. Duplicate *wpa_supplicant.conf* on the SD card. Open it and replace all text with a single space, then rename the file to *ssh* without any file extensions.
3. Install SD card into Raspberry Pi and plug it in. Give it a few minutes to boot fully. 
4. Download and install Advanced IP Scanner from [link]. To use this program to find the IP address of the Raspberry Pi:
	1. Launch program and select the button with **C** (tooltip: `Subnet of class "C" will be scanned`) 
	2. Hit the **Scan** button to begin finding devices on the current network. Find the IP address of the Raspberry Pi in the list.
	3. Remember this IP address
5. Download PuTTY from [link].
6. In PuTTY input *pi@IP_ADDRESS*, replacing **IP_ADDRESS** with the IP found in the previous steps to log into the Raspberry Pi console.
7. Enter `raspberry` for the password.
8. Copy `sudo raspi-config` and right click to paste, then press enter to run. Use arrow keys to choose menu options, tab to select buttons, and enter to select:
	1. Go to [Interfacing]
	2. Enable Camera, I2C, SSH
9.  Run `sudo apt update && sudo apt -y upgrade && sudo apt -y dist-upgrade && sudo rpi-update`
10. Reboot the Pi by typing `sudo reboot`.

### Installing dependencies:
11. Run `sudo apt install -y git python-smbus python-pip`
12. Run:
```bash
git clone https://github.com/silvanmelchior/RPi_Cam_Web_Interface.git #Software bundle to easily use the Raspberry Pi camera module
git clone git://git.drogon.net/wiringPi 	#Software to control output to General Purpose Input Output pins on the Raspberry Pi
git clone https://github.com/johnweber/Adafruit_Python_BNO055	#Python library customized to allow interfacing with the orientation sensor over the I2C interface, works around the hardware driver bug that the Raspberry Pi has
git clone https://github.com/bluerobotics/ms5837-python.git	#Python library for using the pressure/temperature sensor
```
13. Begin install of the RPi Camera Web Interface:
```bash
cd ~/RPi_Cam_Web_Interface
./install.sh
```
14. A configuration will pop up. User arrow keys to choose menu options, tab to select buttons, and enter to select:
	1. Under [folder] delete everything.
	2. Under [server] delete `apache` and type [`lighttpd`] 
	3. Under autostart type [boi].
	4. Press enter to save everything and finish installation.
	5. Select no as we don't need to start the camera just yet.


<!-- 
Install Rpi Cam Web Interface
Install wiringpi
git clone git://git.drogon.net/wiringPi
Install pip
Install sensors
Setup configs (bitbangin), i2c hax
git clone https://github.com/johnweber/Adafruit_Python_BNO055
git clone https://github.com/bluerobotics/ms5837-python.git
Edit configs for lhttpd to enable mod-cgi
Make files for the cockpit
Add pipe for the overlay

 -->



## Steps to setup hardware:
1. 