---
title: docker部署jar包

categories: 

- 工作日常记录

tags: 

- 工作
date: 2020-03-11

---

首先先docker search openjdk8，寻找到要使用的image。我使用的是adoptopenjdk/openjdk8这个。

然后基于该镜像构建容器。

```dockerfile
 docker run -dit --name XXX -w /root  -v /XXX:/root  -p 8888:8080 adoptopenjdk/openjdk8 sh -c "java -jar api.jar"
```