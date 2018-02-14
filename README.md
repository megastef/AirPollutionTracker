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

- Banana-Pi (or Raspberry-Pi) ![](https://images-na.ssl-images-amazon.com/images/I/71Y8shG5r-L._AC_UL160_SR160,160_.jpg)
- PM sensor: Nova SDS011 PM Sensor with serial to USB interface ![](https://images-na.ssl-images-amazon.com/images/I/51XbIvd3RIL.jpg)
- GPS sensor: [Ublox-7, Diymall Vk-172 vk 172 Gmouse G-Maus Usb Gps Dongle Glonass](https://www.amazon.de/dp/B015E2XSSO/ref=cm_sw_r_tw_dp_U_x_zBbHAbB7WPA2K) ![](https://images-na.ssl-images-amazon.com/images/I/51Vqkrr5SeL.jpg

# Software

- Debian / Bananian 
- Node.js
- Logagent with plugins
- MQTT Broker
- [Node-Red](https://nodered.org/) or [Octoblu](https://octoblu.github.io/)
- Time series database with Geo-Data support: Elasticsearch or Sematext Cloud 

# Banana-Pi setup

- Wi-Fi setup 
- Nova SDS011 setup 
- GPS sensor setup 
- MQTT setup 
- Elasticsearch setup 

