FROM alpine:3.4

RUN \
#major part
  apk --update --no-progress add nodejs-lts \
#system utility
  unrar bash unzip inotify-tools curl wget\
#python and other stuff
  py-setuptools python-dev libxml2-dev libxslt-dev py-pip py-libxslt py-lxml

# Install Ghost
ENV GHOST_VERSION 0.8.0

RUN \
  cd /tmp && \
  curl -sSL "https://ghost.org/archives/ghost-${GHOST_VERSION}.zip" -o ghost.zip && \
  unzip ghost.zip -d /ghost && \
  rm -f ghost.zip && \
  echo "unzip completed" ; \
  cd /ghost && \
#Fixing version of sqlite3
  sed -i.bak 's/  "sqlite3".*/    "sqlite3": "3.1.4",/' package.json ;\
#finaly installing all that stuff
  npm install --production -d && \
  npm cache clean


RUN \
  sed 's/127.0.0.1/0.0.0.0/' /ghost/config.example.js > /ghost/config.js && \
  adduser ghost -D -h /ghost -u 987

#install buster
RUN \
  pip install buster

#install md exporter
RUN \
  cd /ghost && \
#Downloading git snapshot because of sqlite3 version problem
  curl -sSL https://github.com/brianokeefe/ghost-export/archive/master.zip -o gexport.zip && \
  unzip gexport.zip && \
  cd /ghost/ghost-export-master && \
#Fixing version of sqlite3
  sed -i.bak 's/  "sqlite3".*/    "sqlite3": "3.1.4"/' package.json  && \
  npm install --production -d -g && \
#clean up all the stuff
  npm cache clean && \
  cd /ghost && \
  rm -Rf gexport.zip /ghost/ghost-export-master

ADD start.bash /ghost-start
ADD publish.sh /ghost/publish.sh
RUN chmod +x /ghost/publish.sh

VOLUME ["/data", "/ghost-override"]

WORKDIR /ghost

CMD ["bash", "/ghost-start"]

EXPOSE 2368
