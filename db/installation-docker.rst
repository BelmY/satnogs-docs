Docker Installation
===================

#. **Requirements**

   You will need `docker <https://docs.docker.com/installation/#installation>`_ and `fig <http://www.fig.sh/install.html>`_.

#. **Build the containers**

   Clone source code from the `repository <https://github.com/satnogs/satnogs-db>`_::

     $ git clone https://github.com/satnogs/satnogs-db.git
     $ cd satnogs-db

   Set your environmental variables::

     $ cp .env-dist .env

   Start database containers::

     $ fig up -d db

   Build satnogs-db container::

     $ fig build web

   Load some satellites fixtures::

    $ fig run web python manage.py loaddata satellites

   Load some transmitters fixtures::

    $ fig run web python manage.py loaddata transmitters

#. **Run it!**

   Run satnogs-db::

     $ fig up

   Your satnogs-db development instance is available in localhost:8000. Go hack!
