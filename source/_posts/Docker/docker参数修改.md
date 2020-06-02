---
title: docker一些参数修改

categories: 

- Docker

tags: 

- docker

date: 2020-06-02 15:32:18

---

有时候需要修改docker的一些参数，如restart，或者command命令，但是不想重新构建容器，这时候可以这么做：

```shell
/var/lib/docker/containers/ID/
```

进入到ID对应目录，里面有两个文件config.v2.json  hostconfig.json，config.v2.json里可以修改command的相关命令，hostconfig.json可以修改端口绑定，地址映射等配置。

修改时需要stop掉，完事之后才start，才生效。