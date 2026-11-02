ARG LOGSTASH_VERSION=9.5.5
FROM docker.elastic.co/logstash/logstash-oss:${LOGSTASH_VERSION}

# Outputs used by conf/logstash/99-*.conf.example:
#   - logstash-output-opensearch : not bundled with Logstash; 2.x supports
#     logstash-core-plugin-api <= 2.99 (Logstash 8/9)
#   - logstash-integration-kafka : bundled with Logstash
#   - logstash-output-elasticsearch, logstash-output-udp : bundled with Logstash
ARG OPENSEARCH_OUTPUT_VERSION=2.1.1
RUN bin/logstash-plugin install --version "${OPENSEARCH_OUTPUT_VERSION}" logstash-output-opensearch

# Fail the build if a required output plugin is missing.
RUN bin/logstash-plugin list --verbose > /tmp/plugins.txt \
 && for p in logstash-output-opensearch logstash-integration-kafka logstash-output-elasticsearch logstash-output-udp; do \
      grep -q "^${p} " /tmp/plugins.txt || { echo "missing plugin: ${p}" >&2; exit 1; }; \
    done \
 && grep -E '^logstash-(output-opensearch|integration-kafka|output-elasticsearch|output-udp) ' /tmp/plugins.txt \
 && rm -f /tmp/plugins.txt

# Logstash 8+ defaults to ECS field names (v8). The pipeline relies on the legacy layout
# (e.g. geoip writing [meta][cric][src][city_name]) and logstash-output-opensearch fails
# to start on Logstash 9 with ECS enabled, so keep the 7.x behaviour pipeline-wide.
RUN echo 'pipeline.ecs_compatibility: disabled' >> config/logstash.yml
