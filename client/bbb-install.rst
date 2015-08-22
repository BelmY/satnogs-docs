========================================
Installing SatNOGS on a BeagleBone Black
========================================

This tutorial assumes the following:

1. You have a BeagleBone Black already installed. This tutorial was written using the Debian 2015-03-01 image, found [here](http://beagleboard.org/latest-images).

2. You have working network connectivity for your SatNOGS tracker (some adapters may take extra work, get those hurdles out of the way first)

5. You will be installing and running as the default `debian` user.

6. You are using an rtl-sdr dongle per the reference platform.

7. You have an account on either network.satnogs.org or network-dev.satnogs.org and have 1) your ground station ID number, 2) your API key

8. Written for SatNOGS client v0.2.4, found at https://pypi.python.org/pypi/satnogsclient/0.2.4 or https://github.com/satnogs/satnogs-client.

-----------------------
Install OS dependencies
-----------------------

Let's get some required packages out of the way first::

   sudo apt-get update
   sudo apt-get upgrade
   sudo apt-get install -y python-pip python-dev supervisor cmake libusb-1.0-0-dev libhamlib-utils vorbis-tools

--------------------
OS optional packages
--------------------
(these may help in testing but are not required for SatNOGS)

* gpredict - for testing the rotor functionality of the tracker
* tightvncserver - running gpredict through VNC instead of ssh x-forwarding takes less resources, allowing you to stream rtl_tcp at the same time

These are optional, install them with::

   sudo apt-get install -y gpredict

------------
Installation
------------

^^^^^^^^^^^^^^^^^^^^^^^
Install SatNOGS rtl-sdr
^^^^^^^^^^^^^^^^^^^^^^^

*SatNOGS uses a custom modified rtl_fm binary made to change the frequency for doppler shifting.*

First, uninstall the pre-existing dvb_usb_rtl28xxu driver if it exists for your dongle::

   sudo bash -c "echo blacklist dvb_usb_rtl28xxu > /etc/modprobe.d/rtl28xxu-blacklist.conf"
   sudo rmmod dvb_usb_rtl28xxu

Next, clone and build the rtl-sdr tools::

   mkdir ~/git
   cd ~/git
   git clone https://github.com/satnogs/rtl-sdr.git
   mkdir rtl-sdr/build
   cd rtl-sdr/build
   cmake ../
   make
   sudo make install
   sudo ldconfig
   sudo cp ../rtl-sdr.rules /etc/udev/rules.d
   sudo udevadm trigger

At this point you should be able to run rtl_test with your dongle plugged in and it will be detected. Press CTRL-C to exit the test.

^^^^^^^^^^^^^^^^^^^^^^
Install satnogs-client
^^^^^^^^^^^^^^^^^^^^^^

Building from source is outside of the scope of this document, we will use the packaged install for now::

   sudo pip install satnogsclient==0.2.4


^^^^^^^^^^^^^^^^^^^^^^^^^^^
supervisord & configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^

I like to manage SatNOGS with supervisord. There are plenty of other ways to do it and your mileage may vary. Before we get to the SatNOGS client we have one a dependency that has not yet been discussed: rotctld for providing rotor control interface to the SatNOGS Arduino board. This should have been installed with libhamlib-utils above.

As with SatNOGS, I run rotctld through supervisord. Open your favorite editor and drop this into
/etc/supervisord/conf.d/rotctld.conf::

   [program:rotctld]
   command=/usr/bin/rotctld -m 202 -r /dev/ttyACM0 -s 19200 -T 127.0.0.1
   autostart=true
   autorestart=true
   user=debian
   priority=1

Now, for the SatNOGS supervisord config file. This is also where you will configure your client as today the settings are all passed in environment variables. Drop this into 
/etc/supervisord/conf.d/satnogs.conf::

   [program:satnogs]
   command=/usr/local/bin/satnogs-poller
   directory=/home/debian/
   autostart=true
   autorestart=true
   user=debian
   environment=SATNOGS_SQLITE_URL="sqlite:////tmp/jobs.sqlite",SATNOGS_API_URL="https://network.satnogs.org/api/",SATNOGS_API_TOKEN="<TOKEN>",SATNOGS_VERIFY_SSL="TRUE",SATNOGS_STATION_ID="<ID>",SATNOGS_STATION_LAT="<LATITUDE>",SATNOGS_STATION_LON="<LONGITUDE>",SATNOGS_STATION_ELEV="<ELEVATION>",SATNOGS_PPM_ERROR="<PPM>"

Obviously there are fields above that will need to be configured appropriately, your latitude/longitude/elevation (example: 43.210 -86.123, elevation is in meters), API token, station ID, and PPM. Log in to the SatNOGS Network console and click on your user icon in the upper-right, then "My Profile". If you have not already added your ground station to the web site please do so now with the "Add Ground Station" button. Once that is done your ground station ID will be shown. In this screen as well you can click the "API Key" button for the token needed in the configuration above. All settings that can be changed in the environment can be found in the [settings.py file](https://github.com/satnogs/satnogs-client/blob/master/satnogsclient/settings.py)

With these files in place, run **sudo supervisorctl reload** and the new configuration files will be picked up and the apps started. You can follow the logs in /var/log/supervisord/.

Other configuration variables can be found by looking at the settings file at https://github.com/satnogs/satnogs-client/blob/0.2.3pypi/satnogsclient/settings.py

**At this point your client should be fully functional! It will check in with the network URL at a 5 minute interval. You should check your ground station page on the website, the station ID will be in a red box until the station checks in, at which time it will turn green.**

-----------------------
BeagleBone Black issues
-----------------------

^^^
USB
^^^

Given that the BeagleBone Black has only one USB interface there will need to be a hub to support the rotor controller (arduino) and the SDR dongle (your configuration may also include a USB wifi device as well). Ensure that your hub is externally powered, and that you can see the devices you need by running `lsusb`. In addition, to see if your hub is going to cause issues run `rtl_test` and watch for dropped packets. If this count is too high you may need to replace it with a different hub.
