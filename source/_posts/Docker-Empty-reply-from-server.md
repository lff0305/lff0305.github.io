---
title: 'Docker: Empty reply from server'
date: 2025-04-10 17:32:16
tags: docker
---

启动一个Docker容器后，从宿主机访问服务发现错误

```shell
$ curl http://localhost:8080

curl: Empty reply from server

$
```

容器内部压根就没有访问日志。检查容器启动参数`-p 8080:8080` 也没有问题。

本想是容器build出问题了。试了一些也没有用。后来使用`docker exec -it ID bash` 进入容器后`curl localhost:8080` 是OK的。

最后才发现因为配置文件的问题，容器的主进程监听的地址是`localhost:8080`!

修改配置文件改成监听`0.0.0.0:8080` 然后重启容器，这次就可以从宿主机访问了。
