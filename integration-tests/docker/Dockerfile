# Base image must come from qubership-docker-integration-tests-demo (adapter-S3 + docker-entrypoint.sh).
# ghcr.io/<owner>/qubership-docker-integration-tests is built by that repo's push.yml; tag follows the branch (slashes → underscores).
# Pin was "fix-atp-integration-tags" and never picked up demo adapter updates.
ARG ATP_BASE_IMAGE=ghcr.io/elbe0116/qubership-docker-integration-tests:feat_atp-integration
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
