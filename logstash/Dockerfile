FROM docker.elastic.co/logstash/logstash:5.3.2

WORKDIR /usr/share/logstash/
COPY config/ config/
COPY pipeline/ pipeline/

EXPOSE 12201/udp 51415
