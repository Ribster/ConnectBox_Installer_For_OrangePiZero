#!/bin/bash
# =============================================================================
#               ConnectBox Installer for OrangePi Zero
# =============================================================================
#
#   By:     GeoDirk
#   Ver:    alpha
#   Date: December 24, 2017
#   License: Apache License - Version 2.0, January 2004
#
#   git:    https://github.com/GeoDirk/ConnectBox_Installer_For_OrangePiZero
#
# =============================================================================
#
# wget -qO- https://raw.githubusercontent.com/GeoDirk/ConnectBox_Installer_For_OrangePiZero/master/inst.sh --no-check-certificate | bash
#

# =============================================================================
# 	    ConnectBox Installer for OrangePi Zero License
# =============================================================================

    # ConnectBox Installer for OrangePi Zero : A quick installer for ConnectBox
	# on an OrangePi Zero
    # Copyright (C) {2016}  GeoDirk
	# geodirk at gmail.com 

    # This program is free software: you can redistribute it and/or modify
    # it under the terms of the Apache License Version 2.0, January 2004
	#
	# A full copy of the licence can be found here: http://www.apache.org/licenses/

    # This program is distributed in the hope that it will be useful,
    # but WITHOUT ANY WARRANTY; without even the implied warranty of
    # MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

# =============================================================================
# 		End ConnectBox Installer for OrangePi Zero License
# =============================================================================


clear
echo "
# =============================================================================
#            ConnectBox Installer for OrangePi Zero installer!
# =============================================================================
#   Please ensure that you are connected to the internet through the LAN port
#   
#   This installer comes with no warranty, use at your own risk.
#
#   Also, please make sure to not install this on an already in use server.
#   Consequences may occur from running this script if you do.
#
# =============================================================================
"

# Run only as root or sudo privileges
if [ "$(id -u)" != "0" ]; then
	echo "Exiting - Run this script as root or with sudo prefix"
	exit 1
fi

# Read the sda1 drive to make sure that it is there
#TODO


# Get User Feedback
read -p "What do you want to name your WiFi's AP SSID?: " cbSSID </dev/tty
wait
read -p "What channel for the AP (0-14)?: " cbCHANNEL </dev/tty
wait

# Getting newest apt-cache.
apt-get update
wait

# Let's do a quick upgrade.
apt-get dist-upgrade -y
wait

# Installing basic depends.
apt-get install screen -y
wait
apt-get install nano -y
wait
apt-get install nginx -y
wait
apt-get install git -y
wait
apt-get install htop -y
wait
apt-get install psmisc -y
wait
apt-get install zip -y
wait
apt-get install unzip -y
wait

#DEBUG - Install the Armbian Monitor
# output on http://local_ip_address:8888
armbianmonitor -r


#========================================
# make a mount point for the usb drive
#========================================
if [[ ! -e /mnt/usb ]]; then
	mkdir /mnt/usb
	echo "/dev/sda1 /mnt/usb auto auto,user,rw,exc 0 0" >> /etc/fstab
fi


#========================================
#     setup nginx webserver and point
#     it to the usb drive for www
#========================================
apt-get install nginx -y
wait
#set the www root to point to the usb stick
sed -i 's|root /var/www/html;|root /mnt/usb/www/html/Content;|' /etc/nginx/sites-enabled/default
#reload nginx to process the changes
service nginx reload


#========================================
#     install h5ai
#========================================
#
#  May not use this...probably nginx directory structure
#  changes first


#========================================
#     obtain ConnectBox files
#========================================
if [[ ! -e /mnt/usb/www ]]; then
	#make www file path
	mkdir /mnt/usb/www/html
	#get the default ConnectBox web structure
	wget https://connectbox.technology/wp/connectbox_basic_web_structure.zip
	#unzip those files to the usb
	
	#TODO
	
fi


#========================================
#     setup wifi as hostapd
#========================================
#create the server config file
hostapd_settings="
interface=wlan0
driver=nl80211
ssid=ConnectBox-Free Media
channel=1"
echo "$hostapd_settings" > /etc/hostapd/hostapd.conf

# get the WiFi's MAC address
cbMAC=`cat /sys/class/net/wlan0/address`
# exclude the MAC address from the Network Manager
echo "
[keyfile]
unmanaged-devices=mac:$cbMAC" >> /etc/NetworkManager/NetworkManager.conf

service wpa_supplicant stop
wait
hostapd /etc/hostapd/hostapd.conf
