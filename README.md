### THIS REPOSITORY IS NO LONGER SUPPORTED OR MAINTAINED ###


mqtt-gpio-monitor
=================

Python 3 script for sending/receiving commands to/from GPIO pins via MQTT messages.

This was written for use on a RaspberryPi, with either the PiFace extension board, or just raw access to the GPIO pins. 

The example INI file contains the only configuration required. You must define the module to use, either GPIO or PFIO. Depending on which you will need to ensure the appropriate Python module is installed.

If using the PiFace extension board you will need to follow the instructions [here](http://piface.github.io/pifacedigitalio/installation.html) to install the digital IO libraries;

    sudo apt-get install python3-pifacedigitalio

If just using the raw GPIO pins then the RPi.GPIO module should be installed as part of Raspbian.

You will also need an MQTT client, in this case [Paho](https://pypi.python.org/pypi/paho-mqtt/0.9). To install;

    sudo pip3 install paho-mqtt

On Rapsbian Stretch, the kernel's SPI driver changed the default serial speed from 500Khz to 125Mhz.   The pifacedigitalio SPI doesn't initialize the SPI speed or ioctl() option so we have to to edit the spi.py file manually.

    sudo nano /usr/lib/python3/dist-packages/pifacecommon/spi.py

Change the spi transfer struct section to match the following.

    # create the spi transfer struct
        transfer = spi_ioc_transfer(
            tx_buf=ctypes.addressof(wbuffer),
            rx_buf=ctypes.addressof(rbuffer),
            len=ctypes.sizeof(wbuffer),
            speed_hz=ctypes.c_uint32(15000)
        )

You should now be ready to run the script. It will listen for incoming messages on {topic}/in/+ (where {topic} is specified in the INI file). The incoming messages need to arrive on {topic}/in/{pin} with a value of either 1 or 0. 

E.g. a message arriving on {topic}/in/3 with value 1 will set pin 3 to HIGH. 

Depending on what is set for MONITOR_PINS in the INI file, the script will also monitor these pins and if there are any changes publish a message on {topic}/out/{pin} with a value of either 1 or 0.

E.g. if pin 7 changes from 1 to 0 a message would be published to {topic}/out/7 with a value of 1.

So you are able to both monitor pins as well as set pins HIGH/LOW. Obviously you can't do both monitor and update for the same pin.

Control the pull up resistors using MONITOR_PINS_PUD set to either UP, DOWN or nothing.

Set the pin numbering to either BOARD or BCM (broadcom) using MONITOR_PIN_NUMBERING.

Invert the output of pins using MONITOR_OUT_INVERT.  i.e. high is read as 0 instead of 1 and vice-versa.

The last option in the INI file is MONITOR_REFRESH, which is a special topic the script will subscribe to if specified. Any message arriving on that topic will trigger the script to re-send publishes with the current state of all monitored pins. This is useful for requesting the current state of all pins if the calling system is restarted for example.
