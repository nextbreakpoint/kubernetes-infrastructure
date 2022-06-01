ARG zookeeper_version

FROM zookeeper:${zookeeper_version}

RUN apt update -y && apt-get install -y wget

RUN mkdir -p /opt/jmx-exporter && \
    mkdir -p /etc/jmx-exporter && \
    wget -O /opt/jmx-exporter/jmx-exporter.jar https://repo1.maven.org/maven2/io/prometheus/jmx/jmx_prometheus_javaagent/0.12.0/jmx_prometheus_javaagent-0.12.0.jar

COPY jmx-exporter.yml /etc/jmx-exporter/zookeeper.yml

COPY docker-entrypoint.sh /docker-entrypoint.sh
RUN chmod a+x /docker-entrypoint.sh

ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["zkServer.sh", "start-foreground"]
