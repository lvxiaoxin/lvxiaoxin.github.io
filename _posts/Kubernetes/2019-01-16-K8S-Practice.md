---
layout: post
title: 基于minikube在单节点搭建kubernetes集群
categories: Docker Kubernetes
description: 本文描述如何在本地，通过minikube搭建一套kubernetes集群
keywords: Docker Dockerfile Kubernetes
---

# 基于minikube在单节点搭建kubernetes集群

## 预备知识

* 基础的Docker知识，包括但不限于 image、container 等
* 基础的 K8S 概念，包括但不限于 node、deployment、service、pod 等

推荐两篇文档博客：

* [Kubernetes 概述](https://k8smeetup.github.io/docs/tutorials/kubernetes-basics/)

* [Docker 概述](https://yeasy.gitbooks.io/docker_practice/image/dockerfile/user.html)
* [我所理解的K8S](https://juejin.im/entry/5ba0ae026fb9a05cfb3da88f)



## 构建Docker镜像

### Docker环境准备

Docker for Mac - Daemon

登录镜像仓库

```bash
docker login [xxxx(host)]
```

默认为docker的官方镜像仓库，后面加参数可替换为私有仓库。



### 构建Docker镜像

**开发Dockerfile**



基础版

```dockerfile
FROM xxx.xxx/model/python:3.6

ARG HOME=/root
WORKDIR /app
COPY . /app

RUN pip install -r requirements.txt && rm -rf /root/.cache

USER root

CMD ["python", "app.py"]
```



切换用户版

```dockerfile
# Base image
FROM xxx.xxx/model/python:3.6


# Label info
LABEL maintainer="lvxiaoxin <lvxin@wecash.net>"

# Create user
RUN mkdir -p /data/app
RUN mkdir -p /data/logs && chmod -R 777 /data/logs

RUN groupadd -r app && \
    useradd -r -g app -m -d /home/app -s /sbin/nologin/ -c "Docker Image User" app

# Create workdir
ENV APP_HOME=/data/app/code

RUN mkdir $APP_HOME

WORKDIR $APP_HOME

COPY . $APP_HOME

# Install all packages
RUN pip install -r requirements.txt

# Install Config
USER root
RUN mkdir -p config/real && chmod -R 777 config

# Talk with outside
EXPOSE 8000

# Don't set up the project by root
USER app

ENTRYPOINT ["gunicorn"]
CMD ["-c", "etc/gunicorn.py", "server:app"]

```

**构建镜像**

e.g.

```bash
docker build -t "model/tester:v1" .
```

**查看镜像**

e.g.

```bash
docker images
```

**运行镜像**

e.g. 

```bash
docker run -it -p [port:port] [image_id]
```



### 推送到镜像仓库

**打标签**

e.g.

```bash
docker tag model/tester:v1 xxx.xxx/model/tester:v1
```

**推送到仓库**

e.g.

```bash
docker push xxx.xxx/model/tester
```





## 构建Kubernetes服务

### Kubernetes 环境准备

**启动minikube**

```bash
minikube status

minikube start 

# 若需要代理，则执行下面的命令来启动
(minikube start --docker-env HTTP_PROXY=http://127.0.0.1:1087  --docker-env HTTPS_PROXY=http:127.0.0.1:1087)

minikube status
```



**查看当前kubernetes各个资源状态**

```bash
kubectl get nodes

kubectl get pods

kubectl get deployments

kubectl get services

kubectl get secrets

minikube ip
```



### 私有仓库secret配置

**查看已有秘钥**

```bash
kubectl get secrets
```

**增加新的秘钥**

e.g.

```bash
kubectl create secret docker-registry regsecret --docker-server=xxx.xxx --docker-username=lvxin --docker-password=??????? --docker-email=lvxin@xxx.xxx
```



### Create a Deployment

**部署一个镜像服务**

**通过 kubectl** （传入secret的方式比较hack的实现）

e.g.

```bash
kubectl run nano-tester --image=wcr.wecash.net/model/nano_tester:v1 --port=8081 --overrides='{"spec": { "imagePullSecrets": [{"name": "wecash-docker-secret"}] } }'

```

**如果在yaml配置文件中使用**，

则应该添加imagePullSecrets字段 的 name: wecash-docker-secret

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: model-tester
  labels:
    app: model-tester
spec:
  replicas: 3
  selector:
    matchLabels:
      app: model-tester
  template:
    metadata:
      labels:
        app: model-tester
    spec:
      containers:
      - name: model-tester
        image: xxx.xxx/model/tester:v1
        ports:
        - containerPort: 8081
      imagePullSecrets:
      - name: wecash-docker-secret
```



### Expose to External as Service

**暴露该服务，指定端口**

```bash
kubectl expose deployment nano-tester --type="NodePort" --port 8081
```

**或者通过写文件**

```yaml
kind: Service
apiVersion: v1
metadata:
  name: nano-tester
spec:
  selector:
    app: nano-tester
  ports:
  - protocol: TCP
    port: 8081
    nodePort: 32321 (这种方式可以指定NodePort)
  type: NodePort
```



