FROM tilemill

RUN set -x \
    && apt-get -yq update \
    && apt-get -yq --no-install-recommends install \
        build-essential \
        protobuf-compiler \
        libprotobuf-dev \
        libtokyocabinet-dev \
        python-psycopg2 \
        python-dev \
        python-setuptools \
        python-pip \
        python-wheel \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

RUN pip --no-cache-dir install imposm

RUN git clone https://github.com/mapbox/osm-bright/

RUN mkdir osm-bright/shp \
    && cd osm-bright/shp \
    && wget https://osmdata.openstreetmap.de/download/simplified-land-polygons-complete-3857.zip \
    && unzip simplified-land-polygons-complete-3857.zip \
    && wget https://osmdata.openstreetmap.de/download/land-polygons-split-3857.zip \
    && unzip land-polygons-split-3857.zip \
    && rm *.zip

RUN mkdir osm-bright/shp/ne_10m_populated_places \
    && cd osm-bright/shp/ne_10m_populated_places \
	&& wget https://www.naturalearthdata.com/http//www.naturalearthdata.com/download/10m/cultural/ne_10m_populated_places.zip \
	&& unzip ne_10m_populated_places.zip \
	&& rm ne_10m_populated_places.zip

#/tilemill/node_modules/mapnik/lib/binding/bin/shapeindex

COPY configure.py osm-bright/configure.py

RUN sed -i.bak 553s/,\$// osm-bright/osm-bright/osm-bright.imposm.mml

#RUN cd osm-bright && ./make.py
