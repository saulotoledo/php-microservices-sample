FROM nginx:stable

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        bc \
        curl \
        unzip \
    && rm -rf /var/lib/apt/lists/*

ENV CONSUL_TEMPLATE_VERSION 0.16.0
ENV CONSUL_TEMPLATE_SHA1 064b0b492bb7ca3663811d297436a4bbf3226de706d2b76adade7021cd22e156
ENV CONSUL_LOCAL_FILE /tmp/consul-template.zip

RUN curl --retry 7 -Lso ${CONSUL_LOCAL_FILE} \
        "https://releases.hashicorp.com/consul-template/${CONSUL_TEMPLATE_VERSION}/consul-template_${CONSUL_TEMPLATE_VERSION}_linux_amd64.zip" \
    && echo "${CONSUL_TEMPLATE_SHA1} ${CONSUL_LOCAL_FILE}" | sha256sum -c \
    && unzip ${CONSUL_LOCAL_FILE} -d /usr/local/bin \
    && rm ${CONSUL_LOCAL_FILE}

ENV CONTAINERPILOT_VERSION 2.4.3
ENV CONTAINERPILOT_SHA1 2c469a0e79a7ac801f1c032c2515dd0278134790
ENV CONTAINERPILOT file:///etc/containerpilot.json
ENV CONTAINERPILOT_LOCAL_FILE /tmp/containerpilot.tar.gz

RUN curl --retry 7 -Lso ${CONTAINERPILOT_LOCAL_FILE} \
         "https://github.com/joyent/containerpilot/releases/download/${CONTAINERPILOT_VERSION}/containerpilot-${CONTAINERPILOT_VERSION}.tar.gz" \
    && echo "${CONTAINERPILOT_SHA1} ${CONTAINERPILOT_LOCAL_FILE}" | sha1sum -c \
    && tar zxf ${CONTAINERPILOT_LOCAL_FILE} -C /usr/local/bin \
    && rm ${CONTAINERPILOT_LOCAL_FILE}

COPY config/ /etc
COPY scripts/ /usr/local/bin

RUN chmod +x /usr/local/bin/reload.sh
RUN chmod +x /usr/local/bin/sensor.sh

CMD ["/usr/local/bin/containerpilot", "nginx", "-g", "daemon off;"]
