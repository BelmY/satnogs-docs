Transponders
============

The ``transponders`` method of the :doc:`SatNOGS DB API </db/api>` returns all Transponders data.

Endpoint
--------

     ``https://db.satnogs.org/api/transponders/``

Parameters
----------

    ``mode``
        *Optional* **string** - Returns only transponders with matching mode

    ``satellite__norad_cat_id``
        *Optional* **string** - Returns only transponders for the matching satellite

Examples
--------

Show transponders of a satellite with a specific Norad Cat ID:

    Request::

        /api/transponders/?format=json&satellite__norad_cat_id=23439

    Response::

        {
            "uuid": "ybJ86zjXzQxDReZ5skY56B",
            "description": "Mode H TLM",
            "alive": true,
            "uplink_low": null,
            "uplink_high": null,
            "downlink_low": 29352000,
            "downlink_high": null,
            "mode": "",
            "invert": true,
            "baud": 0,
            "norad_cat_id": 23439
        }

Show transponders of a specific mode:

    Request::

        /api/transponders/?format=json&mode=AFSK

    Response::

        {
            "uuid": "4Yp5mRhdNPURqAuVn77NMk",
            "description": "Mode V/V AFSK Packet",
            "alive": true,
            "uplink_low": 145990000,
            "uplink_high": null,
            "downlink_low": 145800000,
            "downlink_high": null,
            "mode": "AFSK",
            "invert": true,
            "baud": 0,
            "norad_cat_id": 25544
        }
