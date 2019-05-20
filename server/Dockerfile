FROM ubuntu:18.04

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
  && apt-get install -y wget apt-utils apt-transport-https gnupg2 \
  && wget -q https://sensu.global.ssl.fastly.net/apt/pubkey.gpg -O- | apt-key add - \
  && echo "deb     https://sensu.global.ssl.fastly.net/apt sensu main" | tee /etc/apt/sources.list.d/sensu.list \
  && apt-get update \
  && apt-get install -y sensu wget supervisor \
  && mkdir -p /var/run/sshd /var/log/supervisor


COPY ./conf.d/ /etc/sensu/conf.d/
COPY config.json /etc/sensu/
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

CMD ["/usr/bin/supervisord"]
