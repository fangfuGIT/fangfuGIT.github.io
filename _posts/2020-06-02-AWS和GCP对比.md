---
layout: post
title:  "k8s报错记录"
date:   2020-05-15 10:01:31
categories:  k8s
tags: k8s
excerpt: 
mathjax: true
---


error execution phase preflight: couldn't validate the identity of the API Server: abort connecting to API servers after timeout of 5m0s

master节点的token过期了

在master上重新创建token

kubeadm token create

然后再去节点执行
kubeadm join 192.168.11.11:6443 --token ........
注意修改token的值

----------

error: unable to recognize "prometheus.deploy.yaml": no matches for kind "Deployment" in version "extensions/v1beta1"

kubernetes的版本升级的问题
"extensions/v1beta1" 换成 "apps/v1"


---------

error: error validating "prometheus.deploy.yaml": error validating data: ValidationError(Deployment.spec): missing required field "selector" in io.k8s.api.apps.v1.DeploymentSpec; if you choose to ignore these errors, turn validation off with --validate=false
换成 "apps/v1" 出现的问题，新版本在定义模板的时候必须定义labels,因为Deployment.spec.selector是必须字段,而他又必须和template.labels对应,
spec下面添加
spec:
  selector:
    matchLabels:
      app: prometheus

----------

pvc报错
no persistent volumes available for this claim and no storage class is set
看下pvc设置的大小是不是超过了pv设置的大小


----------

pod无法解析域名

修改pod的yaml文件，添加
在spec下添加
    spec:
      dnsPolicy: "None"
      dnsConfig:
        nameservers:
        - 10.0.2.3

然后重建pod
进入pod测试：
kubectl exec -it pod/jenkins-56c8fbbb9-dhxtt sh


-----------

1 node(s) had taints that the pod didn't tolerate

允许master节点部署pod
kubectl taint nodes --all node-role.kubernetes.io/master-
禁止master部署pod
kubectl taint nodes c1 node-role.kubernetes.io/master=true:NoSchedule









