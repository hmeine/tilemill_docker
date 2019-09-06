Preliminary Dockerfiles for running tilemill and/or osm-bright.

Builds available from dockerhub:
* https://hub.docker.com/r/hansmeine/tilemill
* https://hub.docker.com/r/hansmeine/osm-bright

Tilemill runs fine, with a very simple setup based on an official node.js image.

The integration of osm-bright is more difficult and includes imposm.
I use it together with https://hub.docker.com/r/kartoza/postgis/.

## Docker-Compose with OSM-Bright

The easiest way to get tilemill, osm-bright, imposm and PostGIS
running is to use docker-compose:

    cd osm-bright
    docker-compose up

This will load postgis and osm-bright images from dockerhub and run
them containerized, talking to each other.  Immediately, you should be
able to connect to http://localhost:20009 and see the Tilemill
GUI.

The directory ~/Documents/MapBox will be mapped from your host
into the containers.  If you have not run Tilemill before, the
directory will be populated by Tilemill, but in order to use
OSM-Bright, you also need to set up a corresponding project like so:

    docker exec -it -w /osm-bright osm-bright_tilemill_1 ./make.py

You also want to import data into the postgis database using imposm.
Example:

    docker cp myhometown-latest.osm.pbf osm-bright_tilemill_1:/import.osm.pbf

    docker exec osm-bright_tilemill_1 \
        imposm -m /osm-bright/imposm-mapping.py \
        --connection postgis://docker:docker@postgis/gis
        --read --write --optimize --deploy-production-tables \
        /import.osm.pbf

## Tilemill

Building:

    cd tilemill
    docker build -t tilemill .

Running (using image from docker hub):

    docker run --rm --name tilemill -t \
        -p 20009:20009 -p 20008:20008 \
        -v ~/Documents/MapBox:/root/Documents/MapBox \
        hansmeine/tilemill

## OSM-Bright

Building:

    cd osm-bright
    docker build -t osm-bright .

Running (using image from docker hub):

    docker run --rm --name postgis \
        -p 5432:5432 \
        -v pg_data:/var/lib/postgresql \
        kartoza/postgis

    docker run --rm --name tilemill -t \
        -p 20009:20009 -p 20008:20008 --link postgis \
        -v ~/Documents/MapBox:/root/Documents/MapBox \
        -e PGHOST=postgis -e PGDATABASE=gis -e PGUSER=docker -e PGPASSWORD=docker \
        hansmeine/osm-bright
    
    # this has to be run once, but after tilemill has been started
    # and has set up the ~/Documents/MapBox directory
    docker exec -it -w /osm-bright tilemill ./make.py
