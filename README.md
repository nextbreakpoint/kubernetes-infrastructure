# kubernetes-infrastructure

This repository contains the Helm charts for deploying some infrastructure on Kubernetes.


## Install infrastructure

Add charts repositories:

    helm repo add jetstack https://charts.jetstack.io
    helm repo add bitnami https://charts.bitnami.com/bitnami
    helm repo add hashicorp https://helm.releases.hashicorp.com
    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
    helm repo add jaegertracing https://jaegertracing.github.io/helm-charts
    helm repo add fluent https://fluent.github.io/helm-charts
    helm repo update

Install Cert Manager:

    helm upgrade --install cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace --version v1.8.2 --set installCRDs=true

Install Hostpath Provisioner Operator:

    kubectl create -f https://raw.githubusercontent.com/kubevirt/hostpath-provisioner-operator/main/deploy/namespace.yaml
    kubectl create -f https://raw.githubusercontent.com/kubevirt/hostpath-provisioner-operator/main/deploy/operator.yaml -n hostpath-provisioner
    kubectl create -f https://raw.githubusercontent.com/kubevirt/hostpath-provisioner-operator/main/deploy/webhook.yaml -n hostpath-provisioner
    kubectl create -f https://raw.githubusercontent.com/kubevirt/hostpath-provisioner-operator/main/deploy/storageclass-wffc-csi.yaml
    kubectl create -f https://raw.githubusercontent.com/kubevirt/hostpath-provisioner-operator/main/deploy/hostpathprovisioner_cr.yaml

Install Elasticsearch:

    helm dependency build charts/elasticsearch                      
    kubectl create ns elasticsearch
    helm upgrade --install elasticsearch charts/elasticsearch --render-subchart-notes --namespace elasticsearch

Install Cassandra:

    helm dependency build charts/cassandra                      
    kubectl create ns cassandra
    helm upgrade --install cassandra-rack1 charts/cassandra --render-subchart-notes --namespace cassandra --set cassandra.cluster.rack=rack1
    helm upgrade --install cassandra-rack2 charts/cassandra --render-subchart-notes --namespace cassandra --set cassandra.cluster.rack=rack2,cassandra.cluster.extraSeeds\[0\]=cassandra-rack1-headless.default.svc.cluster.local
    helm upgrade --install cassandra-rack3 charts/cassandra --render-subchart-notes --namespace cassandra --set cassandra.cluster.rack=rack3,cassandra.cluster.extraSeeds\[0\]=cassandra-rack1-headless.default.svc.cluster.local

Install Zookeeper:

    helm dependency build charts/zookeeper                      
    kubectl create ns zookeeper
    helm upgrade --install zookeeper charts/zookeeper --render-subchart-notes --namespace zookeeper

Install Kafka:

    helm dependency build charts/kafka
    kubectl create ns kafka
    helm upgrade --install kafka charts/kafka --render-subchart-notes --namespace kafka

Install Minio:

    helm dependency build charts/minio                      
    kubectl create ns minio
    helm upgrade --install minio charts/minio --render-subchart-notes --namespace minio

Install MySQL:

    helm dependency build charts/mysql                      
    kubectl create ns mysql
    helm upgrade --install mysql charts/mysql --render-subchart-notes --namespace mysql

Install PostgreSQL:

    helm dependency build charts/postgresql                   
    kubectl create ns postgresql
    helm upgrade --install postgresql charts/postgresql --render-subchart-notes --namespace postgresql

Install Consul:

    helm dependency build charts/consul
    kubectl create ns consul
    helm upgrade --install consul charts/consul --render-subchart-notes --namespace consul

Install Vault:

    helm dependency build charts/vault
    kubectl create ns vault
    helm upgrade --install vault charts/vault --render-subchart-notes --namespace vault
    kubectl -n vault exec -it vault-0 -- vault operator init  
    kubectl -n vault exec -it vault-0 -- vault operator unseal
    kubectl -n vault exec -it vault-0 -- vault operator unseal
    kubectl -n vault exec -it vault-0 -- vault operator unseal

Install Jaeger:

    helm dependency build charts/jaeger-operator
    kubectl create ns observability
    helm upgrade --install jaeger-operator charts/jaeger-operator --render-subchart-notes --namespace observability

Install FluentBit:

    helm dependency build charts/fluent-bit
    kubectl create ns fluent-bit
    helm upgrade --install fluent-bit charts/fluent-bit --render-subchart-notes --namespace fluent-bit

Install Prometheus:

    helm dependency build charts/kube-prometheus-stack                      
    kubectl create ns kube-prometheus-stack
    helm upgrade --install kube-prometheus-stack charts/kube-prometheus-stack --render-subchart-notes --namespace kube-prometheus-stack

Install Flink:

    kubectl create ns flink
    helm upgrade --install flink charts/flink --render-subchart-notes --namespace flink


## Documentation

https://cert-manager.io/docs/installation/helm/#output-yaml    
https://github.com/kubevirt/hostpath-provisioner
https://github.com/kubevirt/hostpath-provisioner-operator
https://github.com/bitnami/charts
https://github.com/hashicorp/consul-k8s
https://github.com/hashicorp/vault-k8s
https://github.com/fluent/helm-charts
https://github.com/prometheus-community/helm-charts
https://github.com/jaegertracing/helm-charts
https://github.com/jaegertracing/jaeger-operator
