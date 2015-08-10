SatNOGS Client Installation
----------------------------

tl;dr Installation Steps
~~~~~~~~~~~~~~~~~~~~~~~~

1. Install the SatNOGS custom rtl-sdr package, https://github.com/satnogs/rtl-sdr
2. Install hamlib tools for rotctld, this will interface with the controller arduino (libhamlib-utils in debian distros)
3. Install the satnogs-client package from either pip (satnogsclient) or https://github.com/satnogs/satnogs-client
4. Register your ground station on the network site, get its ID and your API key (https://network.satnogs.org or https://network-dev.satnogs.org)
5. Configure rotctld and satnogs-poller, ensure both start at boot and remain running
6. Check back to the network site, within 5 minutes your station should show green

Detailed Installation Steps
~~~~~~~~~~~~~~~~~~~~~~~~~~~

More detailed and system-specific steps can be found below:

.. toctree::
   :maxdepth: 2

   bbb-install
   raspi2-install
   finding-ppm

