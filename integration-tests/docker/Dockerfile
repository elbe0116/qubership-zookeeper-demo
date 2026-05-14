# Base layer for adapter-S3 + entrypoint. Default tag is mutable; pin with digest when debugging:
#   ghcr.io/elbe0116/qubership-docker-integration-tests@sha256:<digest> (GHCR → package → version digest).
# CI: run "CI: Dev Build" via workflow_dispatch and set "integration_tests_base_image" to pass build-arg ATP_BASE_IMAGE.
# Local: docker build --build-arg ATP_BASE_IMAGE='...@sha256:...' -f integration-tests/docker/Dockerfile integration-tests
ARG ATP_BASE_IMAGE=ghcr.io/elbe0116/qubership-docker-integration-tests:fix-atp-integration-tags
FROM ${ATP_BASE_IMAGE}
# Switch to root for installation
USER root

ENV ROBOT_OUTPUT=${ROBOT_HOME}/output \
    DISTR_DIR=/tmp/deps \
    SERVICE_CHECKER_SCRIPT=${ROBOT_HOME}/zookeeper_pods_checker.py \
    SERVICE_CHECKER_SCRIPT_TIMEOUT=500

RUN mkdir -p ${ROBOT_HOME} \
    && mkdir -p ${ROBOT_OUTPUT}

RUN echo "https://dl-cdn.alpinelinux.org/alpine/v3.23/main" > /etc/apk/repositories && \
    echo "https://dl-cdn.alpinelinux.org/alpine/v3.23/community" >> /etc/apk/repositories

COPY docker/requirements.txt ${ROBOT_HOME}/requirements.txt
COPY docker/zookeeper_pods_checker.py ${ROBOT_HOME}/zookeeper_pods_checker.py
COPY robot ${ROBOT_HOME}

# Upgrade all tools to avoid vulnerabilities
RUN set -x && apk upgrade --available

RUN set -x \
    && pip3 install -r ${ROBOT_HOME}/requirements.txt \
    && rm -rf /var/cache/apk/*

USER 1000:0

# Expose the port
EXPOSE 8080
VOLUME ["${ROBOT_OUTPUT}"]
