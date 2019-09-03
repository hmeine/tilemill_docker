FROM node:8.11

COPY tilemill /usr/src/tilemill

RUN cd /usr/src/tilemill && npm install

ENTRYPOINT cd /usr/src/tilemill && npm start

EXPOSE 20008
EXPOSE 20009
