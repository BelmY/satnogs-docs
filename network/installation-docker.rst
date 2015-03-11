Docker Installation
===================

#. **Requirements**

   You will need `docker <https://docs.docker.com/installation/#installation>`_ and `fig <http://www.fig.sh/install.html>`_.

#. **Build the containers**

   Clone source code from the `repository <https://github.com/satnogs/satnogs-network>`_::

     $ git clone https://github.com/satnogs/satnogs-network.git
     $ cd satnogs-network

   Set your environmental variables::

     $ cp .env-dist .env

   Start database containers::

     $ fig up -d db

   Build satnogs-network container::

     $ fig build web
     
   Optionally you can load some demo data::
   
     $ fig run web python manage.py initialize

#. **Run it!**
     
   Run satnogs-network::
   
     $ fig up
   
   Your satnogs-network development instance is available in localhost:8000. Go hack!
