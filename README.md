Preliminary Dockerfiles for running tilemill and/or osm-bright.

Builds available from dockerhub:
* https://hub.docker.com/r/hansmeine/tilemill
* https://hub.docker.com/r/hansmeine/osm-bright

Tilemill runs fine, with a very simple setup based on an official node.js image.

The integration of osm-bright is more difficult and includes imposm.
I use it together with https://hub.docker.com/r/kartoza/postgis/
(that is - there's an [open issue](https://github.com/mapbox/osm-bright/issues/132) about the connection).

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
