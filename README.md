Preliminary Dockerfiles for running tilemill and/or osm-bright.

Tilemill runs fine, with a very simple setup based on an official node.js image.

The integration of osm-bright is more difficult and includes imposm.
I use it together with https://hub.docker.com/r/kartoza/postgis/
(that is - there's an [open issue](https://github.com/mapbox/osm-bright/issues/132) about the connection).

## Tilemill

Building:

    cd tilemill
    docker build -t tilemill .

Running:

    docker run --rm --name tilemill -t \
        --net=host \
	    -v ~/Documents/MapBox:/root/Documents/MapBox \
	    tilemill

## OSM-Bright

Building:

    cd osm_bright
    docker build -t osm_bright .

Running:

    docker run --rm --name tilemill -t \
        --net=host \
	    -v ~/Documents/MapBox:/root/Documents/MapBox\
        osm_bright
	
	# this has to be run once, but after tilemill has been started
	# and has set up the ~/Documents/MapBox directory
    docker exec -it -w /osm-bright tilemill ./make.py

### TODO

* Fix the connection issue (https://github.com/mapbox/osm-bright/issues/132)
* Fetch postgres connection information from `os.environ`, so that it is configurable from the outside, when starting the container.
* Write a docker-compose.yml.
