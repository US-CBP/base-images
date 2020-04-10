FROM centos:7


RUN yum update -y && yum install -y java-1.8.0-openjdk-devel java-1.8.0-openjdk which && \
    yum clean all


RUN groupadd --gid 1000 logstash && \
    adduser --uid 1000 --gid 1000 \
      --home-dir /usr/share/logstash --no-create-home \
      logstash


RUN curl -Lo - https://artifacts.elastic.co/downloads/logstash/logstash-7.2.0.tar.gz | \
    tar zxf - -C /usr/share && \
    mv /usr/share/logstash-7.2.0 /usr/share/logstash && \
    chown --recursive logstash:logstash /usr/share/logstash/ && \
    chown -R logstash:root /usr/share/logstash && \
    chmod -R g=u /usr/share/logstash && \
    find /usr/share/logstash -type d -exec chmod g+s {} \; && \
    ln -s /usr/share/logstash /opt/logstash

WORKDIR /usr/share/logstash

ENV ELASTIC_CONTAINER true
ENV PATH=/usr/share/logstash/bin:$PATH


ADD config/pipelines.yml config/pipelines.yml
ADD config/logstash-full.yml config/logstash.yml
ADD config/log4j2.properties config/
ADD pipeline/default.conf pipeline/logstash.conf
RUN chown --recursive logstash:root config/ pipeline/


ENV LANG='en_US.UTF-8' LC_ALL='en_US.UTF-8'


ADD bin/docker-entrypoint /usr/local/bin/
RUN chmod 0755 /usr/local/bin/docker-entrypoint

ADD env2yaml/env2yaml /usr/local/bin/env2yaml

RUN chmod 777 /usr/local/bin/env2yaml

RUN rm -f /usr/share/logstash/logstash-core/lib/jars/jruby-complete-9.2.7.0.jar
COPY jruby-complete-9.2.7.0.jar /usr/share/logstash/logstash-core/lib/jars/jruby-complete-9.2.7.0.jar
USER 1000

EXPOSE 9600 5044
ENV JAVA_HOME /etc/alternatives/jre




CMD ["/usr/local/bin/docker-entrypoint"]
