# kubernetes-infrastructure

This repository contains the Helm charts for installing Kafka, Zookeeper, and Flink.

The charts depend on custom Docker images which must be created before installing the charts.


## Create Docker images

Create the image for Zookeeper:

    docker build -t nextbreakpoint/zookeeper:3.4.14 --build-arg zookeeper_version=3.4.14 docker/zookeeper

Create the image for Kafka:

    docker build -t nextbreakpoint/cp-kafka:5.3.1 --build-arg cp_kafka_version=5.3.1 docker/kafka

Create the image for Flink:

    docker build -t nextbreakpoint/flink:1.9.0 --build-arg flink_version=1.9.0 --build-arg scala_version=2.11 docker/flink


## Install applications

Install Zookeeper:

    helm install zookeeper charts/zookeeper

Install Kafka:

    helm install kafka charts/kafka

Install Flink:

    helm install flink charts/flink


## Uninstall applications

Uninstall Flink:

    helm uninstall flink

Uninstall Kafka:

    helm uninstall kafka

Uninstall Zookeeper:

    helm uninstall zookeeper
