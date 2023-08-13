FROM debian:22.04

LABEL maintainer "https://github.com/maciejrolecki"

RUN apt install -y openjdk17-jre-headless

ENV VERSION 8.9.0
ENV DOWNLOAD_URL "https://artifacts.elastic.co/downloads/elasticsearch"
ENV ES_TARBAL "${DOWNLOAD_URL}/elasticsearch-${VERSION}-arm64.deb"
#ENV ES_TARBALL_ASC "${DOWNLOAD_URL}/elasticsearch-${VERSION}-arm64.deb.asc"
#ENV EXPECTED_SHA_URL "${DOWNLOAD_URL}/elasticsearch-${VERSION}-arm64.deb.sha512"
#ENV ES_TARBALL_SHA "710ec2b7f99de0232d7d99fbb79cbe1d829b33dc5be5a01a8d8831e1f486819615bffb02c4f080399082c0316cd27d9deb09a6233457a25c7d80ddbc80e2d352"
#ENV GPG_KEY "46095ACC8548582C1A2699A9D27D666CD88E42B4"

RUN apt update && apt upgrade -y 
RUN apt install -y wget ca-certificates  openssl \
  && cd /tmp \
  && echo "===> Install Elasticsearch..." \
  && wget --progress=bar:force -O elasticsearch.deb "$ES_TARBAL"; \
  #if [ "$ES_TARBALL_ASC" ]; then \
  #wget --progress=bar:force -O elasticsearch.tar.gz.asc "$ES_TARBALL_ASC"; \
  #export GNUPGHOME="$(mktemp -d)"; \
  #( gpg --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys "$GPG_KEY" \
  #|| gpg --keyserver hkp://pgp.mit.edu:80 --recv-keys "$GPG_KEY" \
  #|| gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys "$GPG_KEY" ); \
  #gpg --batch --verify elasticsearch.tar.gz.asc elasticsearch.tar.gz; \
  #rm -rf "$GNUPGHOME" elasticsearch.tar.gz.asc || true; \
  #fi; \
  #tar -xf elasticsearch.tar.gz \
  && ls -lah \
  && mv elasticsearch-$VERSION /usr/share/elasticsearch \
  && adduser -D -h /usr/share/elasticsearch elasticsearch \
  && echo "===> Creating Elasticsearch Paths..." \
  && for path in \
  /usr/share/elasticsearch/data \
  /usr/share/elasticsearch/logs \
  /usr/share/elasticsearch/config \
  /usr/share/elasticsearch/config/scripts \
  /usr/share/elasticsearch/tmp \
  /usr/share/elasticsearch/plugins \
  ; do \
  mkdir -p "$path"; \
  chown -R elasticsearch:elasticsearch "$path"; \
  done \
  && rm -rf /tmp/* /usr/share/elasticsearch/jdk \
  && apt purge

# TODO: remove this (it removes X-Pack ML so it works on Alpine)
RUN rm -rf /usr/share/elasticsearch/modules/x-pack-ml/platform/linux-x86_64

COPY config/elastic /usr/share/elasticsearch/config
COPY config/logrotate /etc/logrotate.d/elasticsearch
COPY elastic-entrypoint.sh /
RUN chmod +x /elastic-entrypoint.sh
#COPY docker-healthcheck /usr/local/bin/

WORKDIR /usr/share/elasticsearch

ENV PATH /usr/share/elasticsearch/bin:$PATH
ENV ES_TMPDIR /usr/share/elasticsearch/tmp

VOLUME ["/usr/share/elasticsearch/data"]

EXPOSE 9200 9300
ENTRYPOINT ["/elastic-entrypoint.sh"]
CMD ["elasticsearch"]