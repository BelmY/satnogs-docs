Docker Installation
===================

#. **Requirements**

   You will need `docker <https://docs.docker.com/installation/#installation>`_ and `docker-compose <https://docs.docker.com/compose/install/>`_.


#. **Build the containers**

   Clone source code from the `repository <https://github.com/satnogs/satnogs-network>`_::

     $ git clone https://github.com/satnogs/satnogs-network.git
     $ cd satnogs-network

   Set your environmental variables::

     $ cp .env-dist .env

   Start database container::

     $ docker-compose up -d db

   Build satnogs-network container::

     $ docker-compose build web

   Run the initialize script to populate the database with scheme and demo data::

     $ docker-compose run web python manage.py initialize

   Note that the above command requires internet connection, since it fetches
   Satellite and Transmitter data from `SatNOGS-DB <https://db.satnogs.org/>`_


#. **Run it!**

   Run satnogs-network::

     $ docker-compose up

   Your satnogs-network development instance is available in localhost:8000. Go hack!
