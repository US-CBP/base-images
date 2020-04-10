FROM ubuntu:16.04

ARG TARGETPLATFORM
ARG BUILDPLATFORM

ARG KIBANA_VERSION=7.2.1


RUN set -e \
 && apt update -y \
 && apt install -y --no-install-recommends ca-certificates \ 
 && apt install -y --no-install-recommends curl \
 && apt install -y --no-install-recommends gosu \
 && apt install -y --no-install-recommends wget \
 && apt install -y --no-install-recommends libkrb5-dev \
 && apt install -y --no-install-recommends git \
 && apt install -y --no-install-recommends libfontconfig
 
 RUN set -e \
 && apt install -y --no-install-recommends python \
 && DEBIAN_FRONTEND=noninteractive apt install -y --no-install-recommends krb5-config \
 && apt install -y --no-install-recommends libssl-dev

 RUN set -e \
 && apt install -y --no-install-recommends libsasl2-dev \
 && apt install -y --no-install-recommends libsasl2-modules-gssapi-mit \
 && apt install -y --no-install-recommends gcc

 RUN set -e \
 && apt install -y --no-install-recommends libc-dev \
 && apt install -y --no-install-recommends make \
 && apt install -y --no-install-recommends g++ \
 && apt install -y --no-install-recommends build-essential

 RUN set -e \
 && apt clean \
 && rm -rf /var/lib/apt/lists/*


ENV \
 KIBANA_HOME=/usr/share/kibana \
 KIBANA_PACKAGE=kibana-${KIBANA_VERSION}-linux-x86_64.tar.gz \
 KIBANA_GID=1000 \
 KIBANA_UID=1000

RUN mkdir /usr/share/kibana \
 && curl -O https://artifacts.elastic.co/downloads/kibana/kibana-7.2.1-linux-x86_64.tar.gz \
 && tar xzf kibana-7.2.1-linux-x86_64.tar.gz -C /usr/share/kibana --strip-components=1 \
 && rm -f kibana-7.2.1-linux-x86_64.tar.gz \
 && groupadd -r kibana -g 1000 \
 && useradd -r -s /usr/sbin/nologin -d /usr/share/kibana -c "Kibana service user" -u 1000 -g kibana kibana \
 && mkdir -p /var/log/kibana \
 && chown -R kibana:kibana /usr/share/kibana /var/log/kibana



RUN set -x \
  && curl -O https://nodejs.org/dist/v10.15.2/node-v10.15.2-linux-ppc64le.tar.gz \
  && mkdir node && tar -xvf node-v10.15.2-linux-ppc64le.tar.gz -C node \
  && rm -rf /usr/share/kibana/node && mkdir /usr/share/kibana/node && mv node/node-v10.15.2-linux-ppc64le/* /usr/share/kibana/node/


RUN set -x \
  && git clone --branch v0.25.0 --depth 1 https://github.com/nodegit/nodegit.git \
  && cd /nodegit \
  && wget https://github.com/fg2it/phantomjs-on-raspberry/releases/download/v2.1.1-wheezy-jessie-armv6/phantomjs \
  && export PATH=$PATH:/nodegit:/usr/share/kibana/node/bin/ \
  && chmod -R 777 /nodegit \
  && /usr/share/kibana/node/bin/npm install --unsafe-perm

RUN set -x \
  && mv /usr/share/kibana/node_modules/@elastic/nodegit/build/Release /usr/share/kibana/node_modules/@elastic/nodegit/build/Release.old \
  && mv /usr/share/kibana/node_modules/@elastic/nodegit/dist/enums.js /usr/share/kibana/node_modules/@elastic/nodegit/dist/enums.js.old \
  && cp -rf /nodegit/build/Release /usr/share/kibana/node_modules/@elastic/nodegit/build \
  && cp /nodegit/dist/enums.js /usr/share/kibana/node_modules/@elastic/nodegit/dist

RUN set -x \
  && cd /nodegit \ 
  && export PATH=$PATH:/nodegit:/usr/share/kibana/node/bin/ \  
  && /usr/share/kibana/node/bin/npm install ctags --unsafe-perm


USER ${KIBANA_UID}


WORKDIR /usr/share/kibana
RUN chmod a+x /usr/share/kibana/bin/kibana
CMD ["/usr/share/kibana/bin/kibana"]

