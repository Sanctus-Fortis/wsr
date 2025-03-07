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
Simply start the device and it will boot to GUI and take you through inital setup, including hostname and password.

## Configure HostAPD
HostAPD is the service that will create our server's wireless access point. We want to do a few things here. First, set the wifi dongle as our access point interface, second, pick an SSID the access point will use and finally, set the service to start after the interface comes online on boot.

In /etc/hostapd/hostapd.conf:

- Change interface to wlan1
- Change SSID config line to you preferred wifi network name

now move to /etc/systemd/system/hostapd.service.d/override.conf

If the hostapd.service.d directory does not exist, create it:

    cd /etc/systemd/system
    sudo mkdir hostapd.service.d

Then enter the directory and create the override.conf file:

    cd hostapd.service.d
    touch override.conf

Now using your preferred file editor add the following to the ovveride.conf:

    [Unit]
    BindsTo=sys-subsystem-net-devices-wlan1.device
    After=sys-subsystem-net-devices-wlan1.device

Now the service wont start until after the interface becomes available.

## Configure Network Manager
We now want to disable management of the wlan1 interface by the Network Manager service.

in /etc/NetworkManager/conf.d/unmanaged.conf:

    [keyfile]
    unmanaged-devices=interface-name:wlan1;interface-name:end1

Notice that this set two interfaces as unmanaged, both wlan1 and end0. end0 is the ethernet interface would be the interface used if you have a seperate router or access point.

## Configure Systemd-Networkd
Next we want to set specific IP addresses on boot. Once again this will include IPs for wlan1 and end0:

create file /etc/systemd/network/wlan1.network

    [Match]
    Name=wlan1
    [Network]
    Address=192.168.4.1/24

Then create file /etc/systemd/network/end0.network

    [Match]
    Name=end0
    [Network]
    Address=192.168.5.1/24

Then restart systemd-networkd

    sudo systemctl restart systemd-networkd

## Configure DNSMasq

DNSmasq will be the DNS and DHCP server for the 192.168.4.0/24 subnet. We want it to resolve all domain requests to itself because that tells connected devices that there is a captive portal they are intended to visit.

in /etc/dnsmasq.conf add the following lines at the top of the file above the existing configuration:

    interface=wlan1
    dhcp-range=192.168.4.10,192.168.4.240,8h
    dhcp-option=3,192.168.4.1
    dhcp-option=6,192.168.4.1
    address=/#/192.168.4.1

This makes dnsmasq act as the DNS and DHCP server for the 192.168.4.0/24 subnet, and resolves all domain requests to itself. You can override specific domains if desired

finally disable systemd-resolved so that port 53 is available

    sudo systemctl disable systemd-resolved --now

Then enable and start dnsmasq

## Configure Nginx

In /etc/nginx/nginx.conf we want to make the following changes:

below the server_name line for the http server listening on port 80:

    return 302 http://oc4d.cdn

Then delete the server_name line and change the listen line to:

    listen              80 default_server;

Now add another server configuration above the others:

    server {
        listen 80;
        server_name oc4d.cdn;
        location / {
            proxy_pass http://localhost:3000/;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Host $host;
            proxy_cache_bypass $http_upgrade;
        }
    }

Finally, enable and start nginx.

## Starting The Server

The first thing we'll need for the server is the actual server content which can be acquired from the oc4d github. However this will not include the modules which contain the actual educational content. 

## Copying The Modules

## Copying The Database

## Serving The Content

## Kolibri

## Zerotier

