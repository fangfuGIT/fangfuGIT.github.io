---
layout: post
title:  "kubernetes相关笔记"
date:   2018-06-19 15:21:49
categories: kubernetes
tags: kubernetes
excerpt: kubernetes
mathjax: true
---



# kubernetes
master - node   

kubernetes master   
api server     scheduler    controller   
etcd   

node中的pod ，是指的具有相同的namespace的container组合，是node中的最小单位。  
Docker   kubelet   kube-proxy   Fluentd

相关的k8s工具，可以在github里查：如 Minikube   kubeadm   kops   

minikube是单节点的k8s集群。  
安装Minikube：   
参考https://github.com/kubernetes/minikube  
需要先安装virtualbox，   
再安装 kubectl， 参考https://kubernetes.io/docs/tasks/tools/install-kubectl/   
然后再装minikube，注意windows下的kubectl.exe,minikube.exe这两个执行文件，需要rename it and add it to your path.    

创建一个单节点的k8s集群：  
 
我是使用windows10下的powershell安装成功的：   
minikube get-k8s-versions   
minikube start --kubernetes-version="v1.10.0" --vm-driver="virtualbox" --v=3 --alsologtostderr   
这里有几个坑，我弄了很久，反复多次安装都没有成功。  
注意几点：一个是要翻墙，powershell都要翻墙。用proxifier+shadowsocks,二是要在proxifier中屏蔽掉内网网段，让其不通过代理，如192.168.0.0-192.168.255.255，三就是注意kubernetes-version要用最新版本。  
安装完成后：  
minikube status  
kubectl cluster-info  

minikube dashboard  
kubectl get nodes  

kubectl config view   
kubectl config get-contexts   
kubectl cluster-info  

minikube ssh  

pod是k8s里调度的最小的单位，1个pod可以包含一个或多个container，1个pod共享一个namespace，namesapce里包含用户、网络、存储等。  
kubectl create -f pod_nginx.yml    （注： 这个yml配置文件内容见github）  
kubectl get pods -o wide  （返回正在运行的节点列表）   
kubectl get rs  (查看replicaset)  
minikube ssh  
kubectl exec -it nginx sh  （直接进入pod，nginx是pod的名称，如果有多个容器，默认是进入第一个容器,通过-c参数可以指定特定的容器）  
kubectl describe pods nginx (打印出nginx这个pod的详细信息)  
kubectl delete -f pod_nginx.yml (删除pod)  
    
做端口映射：   
kubectl port-forward nginx 8080:80   

做容器的扩展：  
kubectl scale rc nginx --replicas=2  
kubectl scale rs nginx --replicas=2   

##Deployments   
kubectl create -f deployment_nginx.yml （创建deployment）         
kubectl get deployment  -o wide  （获取deployment）   
kubectl set image deployment nginx-deployment nginx=nginx:1.13  （nginx版本升级）  
kubectl rollout undo deployment nginx-deployment  （回滚）  
kubectl rollout history deployment nginx-deployment （历史记录）  
kubectl expose deployment nginx-deployment --type=NodePort （deployment下的nginx端口映射）   
kubectl get svc (查看端口状态)   

多个cluster切换  
kubectl config get-contexts   （当前有哪些contexts）   
kubectl config use-context minikube  （切换context）  
kubectl get node   
  
kubectl命令自动补全：  
这个k8s官网上写的比较清楚了   
官网链接：https://kubernetes.io/docs/tasks/tools/install-kubectl/#enabling-shell-autocompletion  
搜一下completion，应该就能找到。不过官网上貌似没有在windows下的配置方法，难道要在windows下安装一个Oh-My-Zsh？

C1、W1，这个概念相当于swarm中的master和workers，直观的意思为controller and workers，  
 
flannel是CoreOS提供用于解决Dokcer集群跨主机通讯的覆盖网络工具。它的主要思路是：预先留出一个网段，每个主机使用其中一部分，
然后每个容器被分配不同的ip；让所有的容器认为大家在同一个直连的网络，底层通过UDP/VxLAN等进行报文的封装和转发。   

不要直接使用和管理pods：   
当我们使用ReplicaSet或ReplicationController做水平扩展scale的时候，pods可能会被terminated  
当我们使用Deployment的时候，我们去更新Docker Image Version,旧的Pods会被terminated，然后新的pods创建   
Services   
kubectl exposee命令，会给我们的pod创建一个service，供外部访问；   
service主要有三种类型：一种叫ClusterIP，一种叫NodePort，一种叫外部的LoadBalancer；  
另外也可以使用DNS，但是需要DNS的add-on；   
ClusterIP就是对Cluster定义的IP，CluseterIP只能内部访问，内部任何一个地方都可以访问这个地址，ClusterIP不会变的，
是services所对应的IP地址，pod和ClusterIP是有一种一一对应的关系；  
NodePort是在node下面的port；  
LoadBalancer则是需要阿里云或AWS提供LoadBalancer服务；   

kubectl edit deployment service-test  (编辑名为service-test的deployment)   

kops 生产环境用的k8s工具   







  







  

 
 


   



  

   



 













































