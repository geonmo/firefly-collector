ARG LOGSTASH_VERSION=9.5.5
FROM docker.elastic.co/logstash/logstash-oss:${LOGSTASH_VERSION}

# OpenSearch output is not bundled with Logstash
RUN bin/logstash-plugin install logstash-output-opensearch

# Logstash 8+ defaults to ECS field names (v8). The pipeline relies on the legacy layout
# (e.g. geoip writing [meta][cric][src][city_name]) and logstash-output-opensearch fails
# to start on Logstash 9 with ECS enabled, so keep the 7.x behaviour pipeline-wide.
RUN echo 'pipeline.ecs_compatibility: disabled' >> config/logstash.yml
