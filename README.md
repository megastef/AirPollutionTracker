# AirPollutionTracker

DIY IoT project for mobile air-pollution measurements and real-time analysis. 

- Mobile device collecting PM10, PM2.5 sensor data with GPS coordinates
- Publishing air pollution data via MQTT
- Storing sensor data as time series with GPS coordinates in [Elasticsearch](https://github.com/elastic/elasticsearch) or [Sematext Cloud](https://sematext.com/cloud)
- Node.js software for data collection with low memory and CPU footprint (<50 MB RAM, <2% CPU)
- Device monitoring and log file collection for troubleshooting
- Visualization
- Alerts

![](https://raw.githubusercontent.com/megastef/AirPollutionTracker/master/image10.png)
![](https://raw.githubusercontent.com/megastef/AirPollutionTracker/master/image13.png)


# Hardware

- Banana-Pi M2
- PM sensor: Nova SDS011 PM Sensor with serial to USB interface 
- GPS sensor: [Ublox-7, Diymall Vk-172 vk 172 Gmouse G-Maus Usb Gps Dongle Glonass](https://www.amazon.de/dp/B015E2XSSO/ref=cm_sw_r_tw_dp_U_x_zBbHAbB7WPA2K) 

# Software

## On the device 
- Debian Linux / [Bananian](https://www.bananian.org/download) or [Armbian](https://www.armbian.com/banana-pi/) or [ArchLinux](https://wiki.archlinux.org/index.php/Banana_Pi) 
- [Node.js](https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions)
- [Logagent](https://sematext.com/logagent/) with [plugins](https://sematext.com/docs/logagent/plugins/)
  - GPS tracking: [logagent-gps](https://www.npmjs.com/package/logagent-gps)  
  - PM measurement: [logagent-novasds](https://www.npmjs.com/package/logagent-novasds)
  - Collect system log files: [file input](https://sematext.com/docs/logagent/input-plugin-files/) for log file collection 
  - Share sensor data via MQTT topic: [output-mqtt](https://sematext.com/docs/logagent/output-plugin-mqtt/) 
  - Store sensor-data and device logs in Elasticsearch: [elasticsearch](https://sematext.com/docs/logagent/output-elasticsearch/) 
  - Monitor Logagent/Node.js performance and device metrics: [@sematext/logagent-nodejs-monitor](https://www.npmjs.com/package/@sematext/logagent-nodejs-monitor) 
  
## In the Cloud 
- MQTT Broker [mosquitto](http://mosquitto.org/)
- [Elasticsearch](http://elastic.co) or [Sematext Cloud](https://sematext.com/cloud)
  Create in Sematext Cloud two logging apps. One for linux logs and one for sensor data.
  If you like to monitor the Node.js and device performance create a Node.js monitorig app.  

## On your desktop 
- [Node-Red](https://nodered.org/) or [Octoblu](https://octoblu.github.io/)
- Web browser
- SSH terminal

# Banana-Pi setup

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



## Nova SDS011 setup 

Insert the Nova-SDS011 USB connector. 
Then search for new Ttty devices. 

```
ls -l /dev/tty* | grep 'dialout'
# /dev/ttyUSB0
```

Note the name for the `input.novaSDS011.comPort setting in the [Logagent configuration file](https://github.com/megastef/AirPollutionTracker/blob/master/logagent-config.yml#L22).  

## GPS sensor setup 

Insert the USB GPS dongle. 
Then search for new Ttty devices. 

```
ls -l /dev/tty* | grep 'dialout'
# /dev/ttyACM0
```

Note the name `input.gps.comPort` setting in the [Logagent configuration file](https://github.com/megastef/AirPollutionTracker/blob/master/logagent-config.yml#L31). 



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

Adjust the [Logagent configuration](https://github.com/megastef/AirPollutionTracker/blob/master/logagent-config.yml) and check following settings: 
 - input.novaSDS011.comPort
 - input.gps.comPort
 - input.nodejsMonitor.SPM_TOKEN
 - output.mqtt.url
 - output.elasticsearch.url
 - output.elasticsearch.indices

Test the configuration with 

```
logagent --config logagent.yml 
```

Copy the working configuration file logagent-config.yml to /etc/sematext/logagent.conf and start the service with 

```
service logagent start 
```

