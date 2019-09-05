FROM node:8.15

RUN git clone https://github.com/tilemill-project/tilemill.git \
  && cd tilemill \
  && npm install

ENTRYPOINT cd tilemill && ./index.js --server=true --listenHost=0.0.0.0

EXPOSE 20008
EXPOSE 20009
