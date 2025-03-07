# Orange Pi Server Setup From Scratch
Behold the comprehensiveness of this documentation for rendering of even the most inept software developer into a server owner.

## Getting Started
If you are setting up the server using peripherals (such as mouse and keyboard) you will want a USB hub that you can use to connect multiple USB devices to the single port on the device.

Your will also want a microHDMI to HDMI cable or converter so that you can connect to a monitor.

If you are planning to use SSH then you will only need the wifi dongle which can be plugged in directly to the device via the single usb port.

## Flashing The SD Card
The image the capstone team uses is an Arch Linux image built for OrangePi by the manufacturer. Ubuntu or Debian images are avialable but Arch Linux has several features that simplify the setup somewhat.

The images are available at:
http://www.orangepi.org/html/hardWare/computerAndMicrocontrollers/service-and-support/Orange-Pi-Zero-3.html

The capstone team built thesed instruction for Arch but they can be adapted for other operating systems without too much difficulty.

### Imaging Software
You can use any imager you are comfortable with, but two of the most popular ones are the Rasperry Pi Imager and Balena Etcher. Either should work without much issue and documentation for both is readily available online.

Using the imager you prefer, flash the image to a microSD card. A 32G card is the minimum recommended given the current size of the educational library the server contains. Larger sizes are recommended.

If you are planning on using SSH to connect to the server most imagers have the option to enable SSH by default. It is recommended to do so even if using perpherals as it can make it easier to maintain the device later if necessary.

## Insert SD Card And Boot
Insert the SD card into the storage slot on the OrangePi and power the device. 

### Connecting To the Device
#### SSH

#### Peripherals

#### 

## Configure HostAPD
HostAPD is the service that will create our server's wireless access point. We want to do a few things here. First, set the wifi dongle as our access point interface, second, pick an SSID the access point will use and finally, set the service to start after the interface comes online on boot.

In /etc/hostapd/hostapd.conf:

- Change interface to wlan1
- Change SSID config line to you preferred wifi network name
        

## Configure Network Manager

## Configure Systemd-Networkd

## Configure DNSMasq

## Configure Nginx

## Starting The Server

## Copying The Modules

## Copying The Database

## Serving The Content

## Kolibri

## Zerotier

