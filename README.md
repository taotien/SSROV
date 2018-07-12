# SSROV
The code repository for all things needed to create an internet-controllable ROV. 
WARNING: This is a work-in progress!

## Dependancies:

## Hardware:

## Steps to setup software:

### Build from scratch:
1. Download latest Raspian image from Raspberry Pi's site (don't get NOOBs).
1. Burn the image to SD card using [Etcher] or [Win32DiskImager].
1. Create a file *wpa_supplicant.conf* (ignore filetype change warning). You can edit this file with Notepad.
1. Paste the following into the file, substituting **WIFI NAME** and **WIFI PASS** for the network you're using. 
(NOTE: only works with networks that don't require logging in, and be the same network as the computer):
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
1. Duplicate *wpa_supplicant.conf* on the SD card. Open it and replace all text with a single space, then rename the file to *ssh* without any file extensions.
2. Install SD card into Raspberry Pi and plug it in. Give it a few minutes to boot fully. 
3. Download and install Advanced IP Scanner from [link]. To use this program to find the IP address of the Raspberry Pi:
	1. Launch program and select the button with **C** (tooltip: `Subnet of class "C" will be scanned`) 
	2. Hit the **Scan** button to begin finding devices on the current network. Find the IP address of the Raspberry Pi in the list.
	3. Remember this IP address
4. Download PuTTY from [link].
5. In PuTTY input *pi@IP_ADDRESS*, replacing **IP_ADDRESS** with the IP found in the previous steps to log into the Raspberry Pi console.
6. Enter `raspberry` for the password.
7. Copy `sudo raspi-config` and right click to paste, then press enter to run. Use arrow keys to choose menu options, tab to select buttons, and enter to select:
	1. Go to 
2. Run `sudo apt update && sudo apt -y upgrade`.
3. Run `sudo apt install -y git `


### From pre-built image:
1. Grab the latset image from [link].
2. Use [Etcher] to burn the image to an sd card.
3. Open *wpa_supplicant.conf* on the image and configure your network settings. See step 4 from the "Build from Scratch" section.
4. Save the file, and eject the SD card.
5. Insert the SD card into the Raspberry Pi


## Steps to setup hardware:
1. 