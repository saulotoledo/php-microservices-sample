FROM php:7-fpm

RUN apt-get update \
    && apt-get -y install git g++ libcurl4-gnutls-dev libicu-dev libmcrypt-dev libpq-dev libxml2-dev unzip zlib1g-dev \
    && docker-php-ext-install curl intl json mbstring pdo pdo_pgsql xml \
    && pecl install mcrypt-1.0.2 redis \
    && docker-php-ext-enable redis mcrypt \
    && git clone -b php7-ipv6 https://github.com/phpredis/phpredis.git /usr/src/php/ext/redis \
    && apt-get autoremove \
    && apt-get autoclean \
    && rm -rf /var/lib/apt/lists/*

RUN echo 'date.timezone="Europe/Oslo"' >> /usr/local/etc/php/conf.d/date.ini
RUN echo 'session.save_path = "/tmp"' >> /usr/local/etc/php/conf.d/session.ini

ENV CONSUL_TEMPLATE_VERSION 0.20.0
ENV CONSUL_TEMPLATE_SHA256 500fe023c89517f959175eb79e21c33df0acf7733d3f3681ec8c5238863caf86
ENV CONSUL_LOCAL_FILE /tmp/consul-template.zip

RUN curl --retry 7 -Lso ${CONSUL_LOCAL_FILE} \
    "https://releases.hashicorp.com/consul-template/${CONSUL_TEMPLATE_VERSION}/consul-template_${CONSUL_TEMPLATE_VERSION}_linux_amd64.zip" \
    && echo "${CONSUL_TEMPLATE_SHA256} ${CONSUL_LOCAL_FILE}"  \
    && echo "${CONSUL_TEMPLATE_SHA256} ${CONSUL_LOCAL_FILE}" | sha256sum -c \
    && unzip ${CONSUL_LOCAL_FILE} -d /usr/local/bin \
    && rm ${CONSUL_LOCAL_FILE}

ENV CONTAINERPILOT_VERSION 3.8.0
ENV CONTAINERPILOT_SHA256 baa016f9c617c7e230a4b4fb5e75802a60512b6e799b022a5f5696ec29b87ac5
ENV CONTAINERPILOT file:///etc/containerpilot.json
ENV CONTAINERPILOT_LOCAL_FILE /tmp/containerpilot.tar.gz

RUN curl --retry 7 -Lso ${CONTAINERPILOT_LOCAL_FILE} \
    https://github.com/joyent/containerpilot/releases/download/${CONTAINERPILOT_VERSION}/containerpilot-${CONTAINERPILOT_VERSION}.tar.gz \
    && echo "${CONTAINERPILOT_SHA256} ${CONTAINERPILOT_LOCAL_FILE}" \
    && echo "${CONTAINERPILOT_SHA256} ${CONTAINERPILOT_LOCAL_FILE}" | sha256sum -c \
    && tar xzf ${CONTAINERPILOT_LOCAL_FILE} -C /usr/local/bin \
    && rm ${CONTAINERPILOT_LOCAL_FILE}

COPY config/ /etc
COPY scripts/ /usr/local/bin

RUN chmod +x /usr/local/bin/reload.sh

CMD [ "/usr/local/bin/containerpilot", "php-fpm", "--nodaemonize" ]
