# SSROV
The code repository for all things needed to create an internet-controllable ROV. 
WARNING: This is a work-in progress!

## Dependancies:

## Hardware:

## Steps to setup software:

### From scratch:
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
1. Move the *wpa_supplicant.conf* file to the SD card. ***DO NOT RUN A REPAIR WHEN WINDOWS COMPLAINS***. The correct drive should be named *boot*.
1. Duplicate *wpa_supplicant.conf* on the SD card. Open it and replace all text with a single space, then rename the file to *ssh*.
1. Download and install Advanced IP Scanner from [link]. To use this program to find the IP address of the Raspberry Pi:
	a. Launch program and select the button with **C** (tooltip: `Subnet of class "C" will be scanned`)
	b. Hit the **Scan** button to begin finding devices on the current network. Find the IP address of the Raspberry Pi in the list.
	c. Remember this IP address
1. Download PuTTY from [link].


### From pre-built image:

## Steps to setup hardware:
