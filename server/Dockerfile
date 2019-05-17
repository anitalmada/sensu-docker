FROM ubuntu:18.04

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
  && apt-get install -y wget apt-utils apt-transport-https gnupg2 \
  && wget -q https://sensu.global.ssl.fastly.net/apt/pubkey.gpg -O- | apt-key add - \
  && echo "deb     https://sensu.global.ssl.fastly.net/apt sensu main" | tee /etc/apt/sources.list.d/sensu.list \
  && apt-get update \
  && apt-get install -y sensu

# Cleanup
RUN rm -rf /opt/sensu/embedded/lib/ruby/gems/2.4.0/{cache,doc}/* &&\
    find /opt/sensu/embedded/lib/ruby/gems/ -name "*.o" -delete

# Runtime Config
ENV TRANSPORT_NAME=rabbitmq \
    RABBITMQ_PORT=5672 \
    RABBITMQ_HOST=rabbitmq \
    RABBITMQ_USER=sensu \
    RABBITMQ_PASSWORD=sensu \
    RABBITMQ_VHOST=/sensu \
    RABBITMQ_PREFETCH=1 \
    RABBITMQ_SSL_SUPPORT=false \
    RABBITMQ_SSL_CERT='' \
    RABBITMQ_SSL_KEY='' \
    REDIS_HOST=redis \
    REDIS_PORT=6379 \
    REDIS_DB=0 \
    REDIS_AUTO_RECONNECT=true \
    REDIS_RECONNECT_ON_ERROR=false \
    # Common config
    LOG_LEVEL=warn \
    CONFIG_FILE=/etc/sensu/config.json \
    CONFIG_DIR=/etc/sensu/conf.d \
    CHECK_DIR=/etc/sensu/check.d \
    EXTENSION_DIR=/etc/sensu/extensions \
    PLUGINS_DIR=/etc/sensu/plugins \
    HANDLERS_DIR=/etc/sensu/handlers \
    # Config for gathering host metrics
    HOST_DEV_DIR=/dev \
    HOST_PROC_DIR=/proc \
    HOST_SYS_DIR=/sys \
    # Include Sensu installation embedded bin in path
    PATH=/opt/sensu/embedded/bin:$PATH \
    # Set default locale & collations
    LC_ALL=en_US.UTF-8 \
    # -W0 avoids Sensu process output to be spoiled with ruby 2.4 warnings
    RUBYOPT=-W0

RUN chown -R sensu:sensu /etc/sensu \
    && sensu-install -p cpu-checks \
    && sensu-install -p disk-checks \
    && sensu-install -p network-checks \
    && sensu-install -p memory-checks

COPY /config/ /etc/sensu/conf.d/
COPY config.json /etc/sensu/
COPY uchiwa.json /etc/sensu/dashboard.d/

EXPOSE 4567
EXPOSE 3000

CMD ["/opt/sensu/bin/sensu-server"]

