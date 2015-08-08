===========
Finding PPM
===========

*This document assumes at least satnogs-client version 0.2.4*

The rtl-sdr dongles are not perfectly tuned and there is always a bit of shift in the crystal used.  To calibrate this we need to find PPM. While rtl_test comes with PPM detection now, it is not very accurate on the raspberry pi due to the lack of a real time clock.  To find our PPM from the command line we will use Kalibrate which finds the PPM against known GSM frequencies.::

   sudo apt-get install autoconf libtool libfftw3-dev
   cd ~/git
   git clone https://github.com/steve-m/kalibrate-rtl
   cd kalibrate-rtl
   ./bootstrap
   ./configure
   make
   sudo make install

Now we run kal to first scan for channels nearby, then picking a channel or two we run kal again to calculate the PPM offset.  In the USA scan the GSM850 range, in Europe GSM900::

   kal -s GSM850
   
   Found 1 device(s):
     0:  Generic RTL2832U OEM
   
   Using device 0: Generic RTL2832U OEM
   Found Elonics E4000 tuner
   Exact sample rate is: 270833.002142 Hz
   kal: Scanning for GSM-850 base stations.
   GSM-850:
      	chan: 145 (872.6MHz + 39.349kHz)	power: 226138.00
      	chan: 151 (873.8MHz + 39.379kHz)	power: 361536.36
      	chan: 157 (875.0MHz + 5.441kHz)	power: 385795.74

Now pick a channel and calibrate against it (note this process may run for a long time)::

   kal -c 151
   
   Found 1 device(s):
     0:  Generic RTL2832U OEM
   
     Using device 0: Generic RTL2832U OEM
     Found Elonics E4000 tuner
     Exact sample rate is: 270833.002142 Hz
     kal: Calculating clock frequency offset.
     Using GSM-850 channel 151 (873.8MHz)
     average       [min, max]  (range, stddev)
     + 39.943kHz       [39832, 39987]  (155, 33.017464)
     overruns: 0
     not found: 781
     average absolute error: -45.711 ppm

In this case, we use -45.711 for our PPM error setting, SATNOGS_PPM_ERROR
