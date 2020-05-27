---
layout: post
title:  "Prometheus监控"
date:   2020-05-12 16:16:31
categories:  Prometheus
tags: Prometheus
excerpt: 
mathjax: true
---


Prometheus的前身是Google Borgmon

go语言编写，性能高。

时序数据库

Prometheus支持的监控类型：
https://prometheus.io/docs/instrumenting/exporters/

通常用Grafana作为Prometheus的UI

Prometheus的环境搭建

Centos7 的环境

下载最新版本的安装包
wget https://github.com/prometheus/prometheus/releases/download/v2.18.1/prometheus-2.18.1.linux-amd64.tar.gz
解压
tar zxvf prometheus-2.18.1.linux-amd64.tar.gz 
启动
 ./prometheus --config.file=prometheus.yml 

 ./prometheus --config.file=prometheus.yml --web.enable-lifecycle

 动态更新prometheus的配置
 curl -X POST http://localhost:9090/-/reload

localhost:9090/graph
localhost:9090/metrics

lsof -i tcp:9090

存储原理参考：
http://coreos.com/blog/prometheus-2.0-storage-layer-optimization


Grafana

Grafana官网下载地址：
https://grafana.com/grafana/download
centos7安装：
wget https://dl.grafana.com/oss/release/grafana-6.7.3-1.x86_64.rpm
sudo yum install grafana-6.7.3-1.x86_64.rpm
service grafana-server start

web默认权限
用户名密码都是admin

在web界面中
1、先添加数据源
add data sources -> Prometheus  -> 修改Name和URL
2、创建dashboard
保存，起个名字
Add Query
Query选择 Prometheus-localhost
metrics： prometheus_http_request_duration_seconds_bucket{job="prometheus"}



Prometheus 报警
AlertManager下载
centos7
wget https://github.com/prometheus/alertmanager/releases/download/v0.20.0/alertmanager-0.20.0.linux-amd64.tar.gz
端口 9093




docker pull prom/prometheus:latest
docker pull prom/node-exporter:latest
docker pull prom/prometheus:latest


kubectl create -f namespace.yaml
kubectl get namespaces
kubectl get pods -n ns-monitor

kubectl create -f node-exporter.yaml
kubectl get pods -n ns-monitor

访问Node的IP加31672端口
http://192.168.99.101:31672/metrics




kubectl create -f prometheus.yaml
kubectl get pod -n ns-monitor

kubectl get svc -n ns-monitor
NAME                    TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
node-exporter-service   NodePort   10.100.85.120    <none>        9100:31672/TCP   12m
prometheus-service      NodePort   10.110.169.200   <none>        9090:30824/TCP   6s

验证
http://192.168.99.101:30824/graph




kubectl apply -f grafana.yaml
kubectl get svc -n ns-monitor
NAME                    TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
grafana-service         NodePort   10.110.89.59     <none>        3000:31987/TCP   21s
node-exporter-service   NodePort   10.100.85.120    <none>        9100:31672/TCP   13m
prometheus-service      NodePort   10.110.169.200   <none>        9090:30824/TCP   70s

验证
http://192.168.99.101:31987/graph
默认用户名和密码：admin/admin

kubectl get pods -n ns-monitor -o wide
NAME                         READY   STATUS    RESTARTS   AGE   IP               NODE       NOMINATED NODE   READINESS GATES
grafana-576db894c6-sj4st     1/1     Running   0          12m   172.17.0.13      minikube   <none>           <none>
node-exporter-8m982          1/1     Running   0          25m   192.168.99.101   minikube   <none>           <none>
prometheus-dd69c4889-w65nc   1/1     Running   0          13m   172.17.0.12      minikube   <none>           <none>



http://172.17.0.12:9090




----------------------------


完整安装prometheus+grafana

在需要安装prometheus的node上执行，我这里是192.168.11.12:
wget https://github.com/prometheus/prometheus/releases/download/v2.18.1/prometheus-2.18.1.linux-amd64.tar.gz

tar zxvf prometheus-2.18.1.linux-amd64.tar.gz

mkdir -p /etc/prometheus
cp prometheus-2.18.1.linux-amd64/* /etc/prometheus/
mv prometheus-2.18.1.linux-amd64/prometheus /bin/

docker pull grafana/grafana:latest

装nfs：
yum install nfs-utils rpcbind -y
mkdir -p /data/k8s
systemctl start rpcbind
systemctl enable rpcbind
systemctl enable nfs-server.service
echo "/data/k8s  192.168.11.0/24(rw,no_root_squash,sync)" >>/etc/exports
systemctl start nfs-server.service
systemctl start rpcbind.service

检查
rpcinfo -p
exportfs -r

其他node上执行：
systemctl start rpcbind.service

测试：
showmount -e 192.168.11.12

挂载磁盘
mount -t nfs 192.168.11.12:/data/k8s /data/k8s



安装yaml文件
kubectl create -f prometheus.configmap.yaml
kubectl get configmaps -n kube-system |grep prometheus

注： configmap用于给容器内应用程序传递参数

kubectl create -f prometheus-volume.yaml
kubectl get pvc --all-namespaces
kubectl get pv prometheus

kubectl create -f prometheus-rbac.yaml

kubectl create -f prometheus.deploy.yaml
kubectl get pod -n kube-system |grep prometheus

kubectl create -f prometeheus-svc.yaml

kubectl get svc -n kube-system |grep prometheus
prometheus        NodePort    10.100.8.125     <none>        9090:30378/TCP           59m

访问prometheus
http://192.168.11.12:30378

kubectl create -f grafana.yaml

kubectl get svc -n kube-system |grep grafana
grafana-service   NodePort    10.100.106.146   <none>        3000:32583/TCP           20m

访问grafana
http://192.168.11.12:32583





-----------------------------



prometheus 监控 redis

kubectl create namespace abcdocker

kubectl create -f prometheus-redis-exporter.yaml

kubectl get pod -n abcdocker

kubectl get svc -n abcdocker
NAME    TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
redis   ClusterIP   10.102.102.242   <none>        6379/TCP,9121/TCP   21m

curl 10.102.102.242:9121/metrics


kubectl delete -f prometheus.configmap.yaml

kubectl create -f prometheus.configmap.yaml

kubectl get svc -n kube-system |grep prometheus
prometheus        NodePort    10.99.137.193   <none>        9090:31935/TCP           4m3s

curl -X POST http://10.99.137.193:9090/-/reload



https://i4t.com/4134.html



-------------------


node-exporter

kubectl create -f prometheus-node-exporter.yaml

kubectl get pod -n kube-system -o wide|grep node

任意节点测试：
curl 127.0.0.1:9100/metrics

kubectl delete -f prometheus.configmap.yaml

kubectl create -f prometheus.configmap.yaml

kubectl get svc -n kube-system |grep prometheus
prometheus        NodePort    10.99.137.193   <none>        9090:31935/TCP           31m

curl -X POST http://10.99.137.193:9090/-/reload

页面上会报错

修改配置
vi prometheus.configmap.yaml
末尾添加：
    - job_name: 'kubernetes-node'
      kubernetes_sd_configs:
      - role: node
      relabel_configs:
      - source_labels: [__address__]
        regex: '(.*):10250'
        replacement: '${1}:9100'
        target_label: __address__
        action: replace

kubectl delete -f prometheus.configmap.yaml

kubectl create -f prometheus.configmap.yaml

curl -X POST http://10.99.137.193:9090/-/reload



https://i4t.com/4136.html




------------------

grafana 配置

https://i4t.com/4146.html
































