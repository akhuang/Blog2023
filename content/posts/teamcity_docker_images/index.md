---
title: "优化 CI 流程：在TeamCity镜像中切换Java版本的方法" 
date: 2024-05-20T01:24:48+08:00
description: ""
tags: [docker,teamcity]
featured_image: ""
# images is optional, but needed for showing Twitter Card
images: []
categories:
comment: true
draft: false
---

## 为什么要修改TeamCity镜像中的Java版本
Teamcity 官方镜像中默认的 Java 版本是 11，但是我们的项目需要使用 Java 8。因此，我们需要修改 TeamCity 镜像中的 Java 版本。


## 如何修改TeamCity镜像中的Java版本
以下是一个验证过的 Dockerfile，可以在 TeamCity 镜像中切换 Java 版本。

```dockerfile
# 基于原始镜像，这里假设原始镜像名为 your-original-image

FROM jetbrains/teamcity-agent

# 避免在构建过程中出现询问

# ARG DEBIAN_FRONTEND=noninteractive

USER root

# 更新包列表，修复可能损坏的包，清理无用的包

RUN apt-get update && \

apt-get install -y wget unzip openjdk-8-jdk

  

# 移除 JDK 17（如果存在）

RUN apt-get remove -y openjdk-17-jdk || true

  

# 设置环境变量，确保使用的是 JDK 1.8

ENV JAVA_HOME /usr/lib/jvm/java-8-openjdk-amd64

ENV PATH $JAVA_HOME/bin:$PATH

  

# 下载并解压 Gradle 5.2.1

RUN wget https://services.gradle.org/distributions/gradle-5.2.1-bin.zip -P /tmp && \

unzip -d /opt/gradle /tmp/gradle-5.2.1-bin.zip && \

rm /tmp/gradle-5.2.1-bin.zip

  

# 设置环境变量，以便可以直接运行 gradle 命令

ENV GRADLE_HOME /opt/gradle/gradle-5.2.1

ENV PATH $PATH:$GRADLE_HOME/bin

```

## 构建镜像
在 Dockerfile 所在目录执行以下命令构建镜像：

```bash
sudo docker build -t teamcity-java-gradle-image .

# -rm: 构建完成后删除中间容器
docker run -it --rm teamcity-java-gradle-image /bin/bash

sudo docker tag teamcity-java-gradle-image phoenixhf/teamcity-java-gradle-image:1.1

sudo docker push phoenixhf/teamcity-java-gradle-image:1.1
```