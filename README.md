# openshift


### 笔记目录

- **相关内容列表**
  - 
    |ID | 文件列表                              |     描述内容      |
    |:--|:--------------------------------------|:----------------- |
    | 0 |S2I|关于镜像构建的相关内容|
    | 1 |/docker/docker.md|docker学习内容|
    | 2 |/docker/Skopeo.md|跨仓库镜像复制工具使用方法|
    | 3 |/CMDCLI.md|Openshift的学习笔记|




### 系统诊断
 oadm diagnostics

 
## 服务命名规则
 
 docker-registry.default.svc.cluster.local
 [servicename]-[namespace].router.default.svc.cluster.local
 

### 测试registry
 172.30.254.72 是docker-registry的service的ip
 ```batch
[root@node1 ~]# docker login -u admin -p $(oc whoami -t) 172.30.254.72:5000
[root@node1 ~]# docker login -u admin -p $(oc whoami -t)  docker-registry.default.svc:5000
Login Succeeded
[root@node1 ~]# docker pull docker.io/busybox
Using default tag: latest
Trying to pull repository docker.io/library/busybox ...
latest: Pulling from docker.io/library/busybox
f70adabe43c0: Pull complete
Digest: sha256:186694df7e479d2b8bf075d9e1b1d7a884c6de60470006d572350573bfa6dcd2
Status: Downloaded newer image for docker.io/busybox:latest
[root@node1 ~]# docker tag docker.io/busybox 172.30.254.72:5000/openshift/busybox
[root@node1 ~]# docker tag docker.io/busybox docker-registry.default.svc:5000/openshift/busybox
[root@node1 ~]# docker push 172.30.254.72:5000/openshift/busybox
The push refers to a repository [172.30.254.72:5000/openshift/busybox]
0314be9edf00: Pushed
latest: digest: sha256:186694df7e479d2b8bf075d9e1b1d7a884c6de60470006d572350573bfa6dcd2 size: 527
[root@node1 ~]# ls /etc/docker/certs.d/
```

### 测试 redhat registry 

```batch
docker pull registry.access.redhat.com/redhat-openjdk-18/openjdk18-openshift
docker login -u minikiller@qq.com -p hanling registry.access.redhat.com
# 错误现象
Error response from daemon: open /etc/docker/certs.d/registry.access.redhat.com/redhat-ca.crt: 
no such file or directory
#解决办法
yum install -y *rhsm*
```

## openjdk image stream 注册
```batch

https://github.com/jboss-openshift/application-templates/blob/master/openjdk/openjdk18-image-stream.json

oc create -f https://raw.githubusercontent.com/jboss-openshift/application-templates/master/openjdk/openjdk18-image-stream.json

```

## 重要资源例子
```batch
https://blog.openshift.com/part-1-from-app-to-openshift-runtimes-and-templates/
https://blog.openshift.com/building-declarative-pipelines-openshift-dsl-plugin/
https://github.com/siamaksade/coolstore-microservice
https://github.com/OpenShiftDemos/nexus-openshift-docker
https://github.com/OpenShiftDemos/openshift-cd-demo
https://ipaas.com.cn/blog/post/seanzhau/3ada17a7b2b8 
https://blog.openshift.com/openshift-pipelines-jenkins-blue-ocean/
https://blog.openshift.com/part-2-creating-a-template-a-technical-walkthrough/

```

## 导出模板 
 oc export all -o yaml --as-template=kong-template > ./kong-template.yaml
## 调整linux磁盘空间
https://themacwrangler.wordpress.com/2015/01/16/re-sizing-partitions-in-centos7/

## 无法连接docker的问题
```
vim /etc/docker/daemon.json
{"registry-mirrors": [
        "https://registry.docker-cn.com",
        "https://kuamavit.mirror.aliyuncs.com",
        "https://docker.mirrors.ustc.edu.cn"
    ]}
```

## jenkins
使用jenkins调用maven操作的方法文件：https://github.com/siamaksade/cart-service/blob/jenkinsfiles/Jenkinsfile
CICDdemo:https://github.com/siamaksade/openshift-cd-demo
https://blog.openshift.com/improving-build-time-java-builds-openshift/
https://blog.openshift.com/decrease-maven-build-times-openshift-pipelines-using-persistent-volume-claim/
### example of jenkins
https://github.com/openshift/origin/tree/master/examples/jenkins/pipeline https://github.com/OpenShiftDemos/openshift-cd-demo
### 官方给出的配置pipeline的方法
https://docs.openshift.org/latest/install_config/configuring_pipeline_execution.html
## jenkins pipeline example  快速拉起一个jenkins实例
```batch
 oc new-app https://github.com/minikiller/mapit-spring.git --strategy=pipeline
```




## 安装dnsmasq

```
如果直接配置了公网域名解析请忽略。（建议单独使用一台低配置服务器部署，不在openshift-ansible安装范围内）
[root@node1 ~]# yum install dnsmasq -y
[root@node1 ~]# vim /etc/dnsmasq.d/node-dnsmasq.conf
server=/in-addr.arpa/127.0.0.1
server=/cluster.local/127.0.0.1
address=/.ipaas.seanzhau.com/192.168.100.101
address=/www.ipaas.seanzhau.com/192.168.100.101
```




