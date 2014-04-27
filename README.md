mqtt-gpio-monitor
=================

Python script for sending/receiving commands to/from GPIO pins via MQTT messages.

This was written for use on a RaspberryPi, with either the PiFace extension board, or just raw access to the GPIO pins. 

The example INI file contains the only configuration required. You must define the module to use, either GPIO or PFIO. Depending on which you will need to ensure the appropriate Python module is installed.

If using the PiFace extension board you will need to do the following;

sudo apt-get install python-dev python-gtk2-dev git
git clone https://github.com/thomasmacpherson/piface.git
cd ~/piface/python/
sudo python setup.py install
cd ~/
sudo ./piface/scripts/spidev-setup

If just using the raw GPIO pins then the RPi.GPIO module should be installed as part of Raspbian.

You will also need an MQTT broker, in this case Mosquitto. To install;

curl -O http://repo.mosquitto.org/debian/mosquitto-repo.gpg.key
sudo apt-key add mosquitto-repo.gpg.key
rm mosquitto-repo.gpg.key
cd /etc/apt/sources.list.d/
sudo curl -O http://repo.mosquitto.org/debian/mosquitto-repo.list
sudo apt-get update
sudo apt-get install python-mosquitto

You should now be ready to run the script. It will listen for incoming messages on <topic>/in/+ (where <topic> is specified in the INI file). The incoming messages need to arrive on <topic>/in/<pin> with a value of either 1 or 0. 

E.g. a message arriving on <topic>/in/3 with value 1 will set pin 3 to HIGH. 

Depending on what is set for MONITOR_PINS in the INI file, the script will also monitor these pins and if there are any changes publish a message on <topic>/out/<pin> with a value of either 1 or 0.
