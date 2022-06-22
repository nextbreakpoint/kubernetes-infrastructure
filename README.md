# kubernetes-infrastructure

This repository contains the Helm charts for installing Kafka, Zookeeper, and Flink.

The charts depend on custom Docker images which must be created before installing the charts.


## Create Docker images

Create the image for Zookeeper:

    docker build -t nextbreakpoint/zookeeper:3.8.0 --build-arg zookeeper_version=3.8.0 docker/zookeeper

Create the image for Kafka:

    docker build -t nextbreakpoint/cp-kafka:6.0.7 --build-arg cp_kafka_version=6.0.7 docker/kafka

Create the image for Flink:

    docker build -t nextbreakpoint/flink:1.15.0 --build-arg flink_version=1.15.0 --build-arg scala_version=2.12 docker/flink


## Test images

Create a bridge network:

    docker network create test

Run Zookeeper as container:

    docker run -it --rm --net test --name zookeeper -p 2181:2181 nextbreakpoint/zookeeper:3.8.0

Run Kafka as container:

    docker run -it --rm --net test --name kafka -p 9093:9093 -e KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181 -e KAFKA_ADVERTISED_LISTENERS="plaintext://kafka:9092,external://localhost:9093" -e KAFKA_LISTENER_SECURITY_PROTOCOL_MAP="PLAINTEXT:PLAINTEXT,EXTERNAL:PLAINTEXT" nextbreakpoint/cp-kafka:6.0.7     

Run Flink Jobmanager as container:

    docker run -it --rm --net test --name flink-jobmanager -p 6123:6123 -p 8081:8081 -e JOB_MANAGER_RPC_ADDRESS=flink-jobmanager nextbreakpoint/flink:1.15.0 jobmanager

Run Flink Taskmanager as container:

    docker run -it --rm --net test --name flink-taskmanager -e JOB_MANAGER_RPC_ADDRESS=flink-jobmanager nextbreakpoint/flink:1.15.0 taskmanager


## Push images

Push images to a registry accessible from Kubernetes:

    docker login ${REGISTRY}

    docker tag nextbreakpoint/zookeeper:3.8.0 ${REGISTRY}/nextbreakpoint/zookeeper:3.8.0
    docker push ${REGISTRY}/nextbreakpoint/zookeeper:3.8.0

    docker tag nextbreakpoint/cp-kafka:6.0.7 ${REGISTRY}/nextbreakpoint/cp-kafka:6.0.7
    docker push ${REGISTRY}/nextbreakpoint/cp-kafka:6.0.7

    docker tag nextbreakpoint/flink:1.15.0 ${REGISTRY}/nextbreakpoint/flink:1.15.0
    docker push ${REGISTRY}/nextbreakpoint/flink:1.15.0


## Install applications

Install Zookeeper:

    helm upgrade --install zookeeper charts/zookeeper --set storage.create=true,image.pullSecrets=regcred,image.registry=${REGISTRY}/     

Install Kafka:

    helm upgrade --install kafka charts/kafka --set storage.create=true,image.pullSecrets=regcred,image.registry=${REGISTRY}/ --values values.yaml

Install Flink:

    helm upgrade --install flink charts/flink --set storage.create=true,image.pullSecrets=regcred,image.registry=${REGISTRY}/


## Uninstall applications

Uninstall Flink:

    helm uninstall flink

Uninstall Kafka:

    helm uninstall kafka

Uninstall Zookeeper:

    helm uninstall zookeeper
