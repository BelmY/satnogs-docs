VirtualEnv Installation
=======================

Requirements: You will need python, python-virtualenvwrapper, pip and git

#. **Build the environment**

   Clone source code from the `repository <https://github.com/satnogs/satnogs-network>`_::

     $ git clone https://github.com/satnogs/satnogs-network.git

   Set up the virtual environment. On first run you should create it and link it to your project path.::

     $ cd satnogs-network
     $ mkvirtualenv satnogs-network -a .

   Set your environmental variables::

     $ cp .env-dist .env

   Activate your python virtual environment::

     $ workon satnogs-network

   Install local development requirements::

     $ (satnogs-network)$ pip install -r requirements/dev.txt


#. **Database**

   Create and setup the database::

     (satnogs-network)$ ./manage.py migrate

   Create a superuser::

     (satnogs-network)$ ./manage.py createsuperuser

   Push some demo data::

     (satnogs-network)$ ./manage.py initialize

#. **Run it!**

  Just run it::

    (satnogs-network)$ ./manage.py runserver

  Your satnogs-network development instance is available in localhost:8000. Go hack!
