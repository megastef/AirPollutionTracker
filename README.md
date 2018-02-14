# AirPollutionTracker

DIY IoT project for air-pollution measurement and real-time analysis. 

- Mobile device collecting PM10, PM2.5 data and GPS coordinates
- Publishing sensor data via MQTT
- Storing sensor data as time series with GPS coordinates in Elasticsearch or Sematext Cloud
- Visualization
- Alerts
- Public storage for 

# Status

__under construction__

# Hardware

- Banana-Pi M2
- PM sensor: Nova SDS011 PM Sensor with serial to USB interface 
- GPS sensor: [Ublox-7, Diymall Vk-172 vk 172 Gmouse G-Maus Usb Gps Dongle Glonass](https://www.amazon.de/dp/B015E2XSSO/ref=cm_sw_r_tw_dp_U_x_zBbHAbB7WPA2K) 

# Software

- Debian Linux / [Bananian](https://www.bananian.org/download) or [Armbian](https://www.armbian.com/banana-pi/) or [ArchLinux](https://wiki.archlinux.org/index.php/Banana_Pi) 
- [Node.js](https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions)
- [Logagent](https://sematext.com/logagent/) with [plugins](https://sematext.com/docs/logagent/plugins/)
- MQTT Broker [mosquitto](http://mosquitto.org/)
- [Node-Red](https://nodered.org/) or [Octoblu](https://octoblu.github.io/)
- [Elasticsearch](http://elastic.co) or Sematext Cloud [https://sematext.com/cloud]

# Banana-Pi setup

## Install Node.js

```
apt-get install build-essential
curl -sL https://deb.nodesource.com/setup_8.x | bash - 
apt-get install -y nodejs
```

## Install Logagent

Install @sematext/logagent and relevant plugins
```
npm i -g --unsafe-perm @sematext/logagent 
npm i -g --unsafe-perm logagent-gps logagent-novasds
npm i -g --unsafe-perm @sematext/logagent-nodejs-monitor 
```

Create a system service and default configuration file '/etc/sematext/logagent.conf': 

```
logagent-setup -t indexName -e http://localhost:9200  
service logagent stop
```

Adjust the Logagent configuration. Test the configuration with 

```
logagent --config logagent.conf 
```

Copy the working configuration to /etc/sematext/logagent.conf and start the service with 

```
service logagent start 
```

# Wi-Fi setup 

A smart phone will serve as mobile Wi-Fi hotspot.  
Enable internet tethering (iPhone hotspot) on your mobile phone. 

Insert WLAN USB device (if your device has no WLAN chip)
Then configure Banana-Pi wlan interface: 

1. Use the WPA passphrase to calculate the correct WPA PSK hash for your SSID (Wi-Fi Network name) by altering the following example


```
apt-get install wpasupplicant

wpa_passphrase my-mobile-phone-hotspot-name my_very_secret_passphrase

network={
        ssid="myssid"
        #psk="my_very_secret_passphrase"
        psk=ccb290fd4fe6b22935cbae31449e050edd02ad44627b16ce0151668f5f53c01b
}

```

Then edit `/etc/network/interfaces` and enter your WLAN SSID and PSK:

```
auto wlan0
iface wlan0 inet dhcp
        wpa-ssid myssid
        wpa-psk ccb290fd4fe6b22935cbae31449e050edd02ad44627b16ce0151668f5f53c01b
```

Restart the wlan0 interface: 

```
ifdown wlan0 && ifup wlan0
```

# Nova SDS011 setup 

Insert the Nova-SDS011 USB connector. 
Then search for new Ttty devices. 

```
ls -l /dev/tty* | grep 'dialout'
# /dev/ttyUSB0
```

Set the right name for the COM port in Logagent configuration:  

# GPS sensor setup 

Insert the USB GPS dongle. 
Then search for new Ttty devices. 

```
ls -l /dev/tty* | grep 'dialout'
# /dev/ttyACM0
```

Set the right name for the COM port in Logagent configuration.  
