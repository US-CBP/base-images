FROM ppc64le/openjdk:8-jdk

CMD ["/bin/bash"]

ENV JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-ppc64el
ENV PATH=$JAVA_HOME/bin:$PATH
ENV RUBY_PLATFORM=java
ENV ANT_HOME=/opt/logstash/apache-ant-1.9.4
ENV PATH=$ANT_HOME/bin:$PATH

ENV ELASTIC_CONTAINER true

COPY elasticsearch-7.2.1-SNAPSHOT-linux-ppc64le.tar.gz /elasticsearch.tar.gz
RUN tar -C /usr/share -xf /elasticsearch.tar.gz
RUN mv /usr/share/elasticsearch-7.2.1-SNAPSHOT /usr/share/elasticsearch

RUN wget https://repo1.maven.org/maven2/net/java/dev/jna/jna/4.5.1/jna-4.5.1.jar
RUN mv jna-4.5.1.jar /usr/share/elasticsearch/lib/

RUN groupadd -g 1000 elasticsearch && useradd -u 1000 -g 1000 -G 0 -d /usr/share/elasticsearch elasticsearch && chmod 0775 /usr/share/elasticsearch && chgrp 0 /usr/share/elasticsearch

WORKDIR /usr/share/elasticsearch


RUN ln -sf /etc/pki/ca-trust/extracted/java/cacerts /usr/share/elasticsearch/jdk/lib/security/cacerts

ENV PATH /usr/share/elasticsearch/bin:$PATH

COPY --chown=1000:0 bin/docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh


RUN chgrp 0 /usr/local/bin/docker-entrypoint.sh && chmod g=u /etc/passwd && chmod 0775 /usr/local/bin/docker-entrypoint.sh

EXPOSE 9200 9300



CMD ["/usr/local/bin/docker-entrypoint.sh"]
