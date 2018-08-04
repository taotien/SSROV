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
4. Modify pressure sensor to use I2C-3
5. Make sure wiringPi doesn't interfere with I2C hacks
6. Purchase and test ethernet add-on board or USB through the GPIO. 
7. Setup a full working hardware prototype (so the pins can be tested for conflicts)
8. Package and tutorial around ethernet gadget mode (for Pi0 only)
9. List of apt packages required
10. Screenshots for everything

## Introduction
The internet ROV is powered by the budget Raspberry Pi Zero and common off-the-shelf components found that is built around the Raspberry Pi platform as well as basic programming. The goal of this project is to enable a remote user to access a ROV without being on the docks.

The remote user connects to a proxy server (a workaround to port-forwarding restrictions) that forwards all connections to the ROV server, which is onboard the ROV itself. All calculations are excecuted on the ROV itself. The user can see a live image of the ROV, an overlay of various data, and control using onscreen buttons or a keyboard. The onsite operator should be able to simply connect the ROV to a laptop and power source, connect to the internet, and run a script which handles all the initialisation of software, such that no configuration or programming is needed. 

### Hardware:
- Raspberry Pi Zero (or Raspberry Pi Zero W, soldered headers optional)
- 1x SD card for Raspberry Pi OS and software storage
- 4x Motor control boards
- 1x Raspberry Pi Camera
- 1x OpenROV combo sensor: Adds compass heading, orientation, pressure, and temperature data retrieval
- 1x Salinity sensor
- 1x Portable computer for configuration and tethering
- 1x Mobile hotspot

| Item                                            | Link                                                                                        | Quantity | Price/Unit |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------- | -------- | ---------- |
| Raspberry Pi Zero W                             | https://www.adafruit.com/product/3400                                                       | 1        | $10.00     |
| Raspberry Pi Zero                               | https://www.adafruit.com/product/2885                                                       | 1        | $5.00      |
| NOTE: Most sites only allow 1 RPi per purchase  | Alternate links: https://www.raspberrypi.org/products/                                      |
| 2x20 Breakaway Header Strip                     | https://www.adafruit.com/product/2822                                                       | 1        | $0.95      |
| 8GB SD Card                                     | https://www.adafruit.com/product/1294                                                       | 1        | $9.95      |
| Adafruit DRV8871 DC Motor Driver Breakout Board | https://www.adafruit.com/product/3190                                                       | 4        | $7.50      |
| Zero Spy Camera for Raspberry Pi Zero           | https://www.adafruit.com/product/3508                                                       | 1        | $19.95     |
| OpenROV IMU/Compass/Depth Module                | https://store.openrov.com/products/openrov-imu-depth-module                                 | 1        | $120.00    |
| Water level or salinity sensor module           | http://www.elechouse.com/elechouse/index.php?main_page=product_info&cPath=&products_id=2233 | 1        | $4.99      |
|                                                 |                                                                                             | Total    | $168.34    |

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
8. Run [init scripts]

# Build from scratch:

### Preparing the image:
1. Download latest Raspian image from Raspberry Pi's site [https://www.raspberrypi.org/downloads/raspbian/].
7. Burn the image to SD card using Etcher [https://etcher.io/] or Win32DiskImager[https://sourceforge.net/projects/win32diskimager/].
	1. Steps:
1. Skip steps [#] to setup internet over USB instead.
8. Create a file *wpa_supplicant.conf* (ignore filetype change warning). You can edit this file with Notepad.
9. Paste the following into the file, substituting **WIFI NAME** and **WIFI PASS** for the network you're using. (NOTE: only works with networks that don't require logging in, and be on the same network as the computer. Make sure your uppercase and lowercases, as well as punctuation is correct):

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
3. To setup and connect the Raspberry Pi Zero to the internet over USB:
	1. https://learn.adafruit.com/turning-your-raspberry-pi-zero-into-a-usb-gadget/ethernet-gadget [expand]
4. Install SD card into Raspberry Pi and plug it in. Give it a few minutes to boot fully. 
5. Download and install Advanced IP Scanner from [https://www.advanced-ip-scanner.com/]. To use this program to find the IP address of the Raspberry Pi:
	1. Launch program and select the button with **C** (tooltip: `Subnet of class "C" will be scanned`) 
	2. Hit the **Scan** button to begin finding devices on the current network. Find the IP address of the Raspberry Pi in the list.
	3. Remember this IP address
6. Download PuTTY from [link].
7. In PuTTY input *pi@IP_ADDRESS*, replacing **IP_ADDRESS** with the IP found in the previous steps to log into the Raspberry Pi console.
8. Enter `raspberry` for the password.
9. Copy `sudo raspi-config` and right click to paste, then press enter to run. Use arrow keys to choose menu options, tab to select buttons, and enter to select:
	1. Go to [Interfacing]
	2. Enable Camera, I2C, SSH
10. Run `sudo apt update && sudo apt -y upgrade && sudo apt -y dist-upgrade && sudo rpi-update`
11. Reboot the Pi by typing `sudo reboot`.

### Installing dependencies:
11. Run `sudo apt install -y git python-smbus python-pip build-essential python-dev`
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
	1. Under [folder] type `cam`.
	2. Under [server] delete `apache` and type [`lighttpd`] 
	3. Under autostart type [boi].
	4. Press enter to save everything and finish installation.
	5. Select [no] as we don't need to start the camera just yet.
15. Install wiringPi:
```bash
cd ~/wiringPi
git pull origin
./build
```
16. Setup I2C over GPIO
	1.  `sudo nano /boot/config.txt`
	2.  Use arrow keys to navigate to the very bottom and add the line `dtoverlay=i2c-gpio,i2c_gpio_sda=<pin>,i2c_gpio_scl=<pin>`. Edit the two `<pin>` numbers to the correct SDA and SCL pins used. It should be [###] and [###] accoding to the diagram.
	1.  Press CTRL+X to begin saving the file, press Y to accept the filename, and press ENTER to exit nano. 
	2.  `sudo reboot` to reboot the Raspberry Pi.
	3.  `modprobe i2c-dev` to finish the setup.
17. Install BNO055 sensor:
```bash
cd ~/Adafruit_Python_BNO055
sudo python setup.py install
```
### Testing all the modules:
1. Test the camera:
	1. `cd ~/RPi_Cam_Web_Interface`
	2. `./start.sh` to turn on the camera.
	3. On your computer, open a browser and type `http://[raspberrypi ip address]/cam/min.php`
	4. There should be a live feed. 
	5. `./stop.sh` to turn off the camera.
2. Test wiringPi (motors):
	1. `gpio readall` will print a diagram of all the pins on the Raspberry Pi.
	2. If motor is connected:
		1. `gpio mode <pin> out` once for each data pin of the motors being tested.
		2. `gpio write <pin> 1` will enable the motor to turn. Run `gpio write <pin> 0` to turn off motor.
		3. `gpio unexportall` to finish testing.
3. Test the BNO055 sensor:
	1. `cd ~/Adafruit_Python_BNO055/examples`
	2. `python simpletest.py` This should show a live steam of data about the sensor. Move the sensor module around to see if it's working.
	3. Press CTRL+C to quit the program.
4. Test the ms5837 sensor:
	1. `cd ~/ms5837-python`
	2. `python example.py`


### Configuring the server:
WIP Steps:
1. Create index.php in /var/www
2. `sudo lighttpd-enable-mod cgi`
3. `sudo lighttpd-enable-mod fastcgi`
4. `sudo nano /etc/lighttpd/lighttpd.conf`
	1. Change the line `server.documet-root="/var/www/html"` to read `"server.document-root="/var/www/"`.
	2. In `server.modules = ()` add `"mod_cgi",`.
5. `sudo /etc/init.d/lighttpd stop`
6. `sudo /etc/init.d/lighttpd start`
7. 

<!--
ssh tunnel
-->



## Steps to setup hardware:
