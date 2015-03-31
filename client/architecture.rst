SatNOGS client architecture
----------------------------

Overview
~~~~~~~~

**SatNOGS client** is the part of our software stack that:

* Fetches observation jobs from the network.
* Schedules locally when the observation starts/ends.
* Does orbital calculation for the position of the observer and the tracked object (using ``PyEphem``).
* Sends ``rotctl/rigctl`` commands to control **SatNOGS** rotator.
* Spawns processes to run demodulation/decoding software with the signal received as input.
* Stores the received signal locally.
* Posts observation data back to the network.

Except of it's core functionality, **SatNOGS client** is responsible to send logs and report back to
the network if an issue is encountered.

Modules
~~~~~~~

Following the paradigm of **SatNOGS** being extensively modular, **SatNOGS** client is designed to have
discrete modules with specific functionality.

=============
``scheduler``
=============
* Build using ``apscheduler`` library.
* Stores tasks in ``sqlite``.

^^^^^
Tasks
^^^^^
* ``get_jobs``: Queries **SatNOGS Network API** to get jobs scheduled for the ground station.
* ``spawn_observation``: Initiates an ``Observer`` instance and runs the observation.
* ``post_observation_data``: Gathers output files, parses filename and posts data back to **SatNOGS Network API**.

=====================
``observer.observer``
=====================
Given initial description of the observation (``tle``, ``start``, ``end``)
 * Checks input for sanity.
 * Initializes ``WorkerTrack`` and ``WorkerFreq`` instances that start ``rigctl/rotctl``.
   communication using ``trackstart`` method.

===================
``observer.worker``
===================
* Facilitates as a worker for ``rigctl/rotctl``.
* Is the lowest abstraction level on ``rigctl/rotctl`` communications.
* Tracks object until end of observation is reached.

====================
``observer.orbital``
====================
* Implements orbital calculations using ``PyEphem``.
* Provides ``pinpoint`` method the returns alt/az position of tracked object.

============
``receiver``
============
* Orchestrates signal demodulation/decoding.
* Spawns ``rtl_fm``, ``oggenc`` or ``multimon-ng`` processes.
* Implements interprocess communication using a ``pipe``.
* Stores the signal received using a specific (unique) filename format.

===============
``bin/scripts``
===============
Except of the automated tasks, our python package exposes a couple of scripts for operations such as
running the scheduler in the background or help the development procedure.

Our current scripts under ``bin/scripts`` are:

* ``satnogs-poller``: Run the scheduler queue in the background and fetch jobs from the network.
* ``satnogs-task``: Given the required data, manually spawn an observation task.
