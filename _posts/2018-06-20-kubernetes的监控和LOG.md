---
layout: post
title:  "kubernetes的监控和LOG"
date:   2018-06-20 13:57:11
categories: kubernetes
tags: kubernetes
excerpt: kubernetes
mathjax: true
---


kubernetes的监控


Heapster + Grafana + InfluxDB


Heapster下载地址：
https://github.com/kubernetes-retired/heapster


目录：
heapster/deploy/kube-config/influxdb
这里面有三个文件：
grafana.yaml
heapster.yaml
influxdb.yaml

我们这里不使用 Cluster add-on
注释掉这一行：
#kubernetes.io/cluster-service: 'true'

什么是add-on,就像Services和Deployments一样，它是一种资源，是kubernetes固有的一部分。
Cluster add-ons are resources like Services and Deployments (with pods) that are shipped with the Kubernetes binaries and are considered an inherent part of the Kubernetes clusters.
# For use as a Cluster add-on (https://github.com/kubernetes/kubernetes/tree/master/cluster/addons)

这三个文件中会涉及到三个镜像：
        image: k8s.gcr.io/heapster-grafana-amd64:v5.0.4
        image: k8s.gcr.io/heapster-amd64:v1.5.4
        image: k8s.gcr.io/heapster-influxdb-amd64:v1.5.2

由于国内访问不到k8s.gcr.io
只能找其他镜像代替
docker pull dockerofwj/heapster-grafana-amd64
docker pull dockerofwj/heapster-amd64
docker pull mycontroldestiny/k8s

然后把三个文件中的image配置的部分也换掉。


kubectl get svc --namespace kube-system

HTTP压测工具，wrk
 
 
minikube addons list
minikube addons enable heapster

minikube stop
minikube start --extra-config=controller-manager.HorizontalPodAutoscalerUseRESTClients=false

 
自动水平扩展
autoScale




k8s监控

Prometheus

通过http，pull的方式拉取数据
项目地址：
https://github.com/coreos/prometheus-operator

kubectl create -f rbac.yml
kubectl create -f prometheus.yml
kubectl create -f prometheus-resource.yml
kubectl create -f kubernetes-monitoring.yml
创建一个示例：
kubectl create -f example-app.yml

注意prometheus.yml中的
type:LoadBalancer

RBAC 是基于角色的访问控制(Role-Based Access Control )


容器的LOG

开源的服务：ELK
收费的服务：hosted log


Fluentd        (log 转发)
ElasticSearch  (log Index) 用于搜索
kibana         (log 可视化) 
LogTrail	   (log UI查看) 是一个插件，可以用过UI看LOG

 

kubectl create -f logging/

   



 













































