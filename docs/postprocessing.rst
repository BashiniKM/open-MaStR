***************
Post-processing
***************

Pre-requisites
==============

Major parts of data cleansing, correction and enrichment are written in SQL. We recommend to run these scripts in a
dockered PostgreSQL database. If you want to use a native installation of PostgreSQL, help yourself and continue with
:ref:`Run postprocessing`.

Make sure you have `docker-compose <https://docs.docker.com/compose/install/>`_ installed. Run

.. code-block:: bash

   docker-compose up -d

to start a PostgreSQL database.

You can connect to the database with the following credentials (if not changed in `docker-compose.yml`).

======== ==========
Field    Value
======== ==========
host     localhost
port     55443
database open-mastr
User     open-mastr
Password open-mastr
======== ==========


Once you're finished with working in/with the database, shut it down with

.. code-block:: bash

   docker-compose down


Run postprocessing
==================

During post-processing downloaded :ref:`raw data <Downloading raw data>` gets imported to a PostgreSQL database,
cleaned and enriched.
To run the postprocessing, use the following code snippet.

.. code-block:: python

   from postprocessing.postprocessing import postprocess

   postprocess()


.. note::

   It is assumed raw data resides in `~/.open-MaStR/data/<data version>/` as explained in :ref:`Configuration`.


Database import
---------------

Where available, geo location data, given in lat/lon (*Breitengrad*, *Längengrad*), is converted into a PostGIS geometry
data type during database import. This allows spatial data operations in PostgreSQL/PostGIS.


Data cleansing
--------------

Units inside Germany and inside German offshore regions are selected and get distinguished from units that are (falsely)
located outside of Germany.
Data is stored in separate tables.


Data enrichment
---------------

For units without geo location data, a location is estimated based on the zip code. The centroid of the zip code region
polygon is used as proxy for the exact location.
To determine the zip code area, zip code data of OSM is used which is stored in
`boundaries.osm_postcode <https://openenergy-platform.org/dataedit/view/boundaries/osm_postcode>`_.
If a unit originally had correct geo data and the origin of estimated geom data is documented in the column `comment`.
