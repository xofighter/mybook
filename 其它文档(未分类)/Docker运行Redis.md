　自从接触docker之后,很多软件都想着用docker运行,毕竟手动安装的话老是要配一些环境变量啊,找配置文件修改配置什么的,docker却有更简便的办法.

　　正题:

　　建一个docker应用容器可以通过build dockerfile文件,但是这个文件手写的话又很麻烦,而且容易出错.

　　找到官方仓库:

[https://hub.docker.com](https://hub.docker.com/),搜索要的软件,比如redis,可以看到

![img](https://images2015.cnblogs.com/blog/919970/201703/919970-20170328102811967-1654714062.png)

 

选择星比较多的,进入detail,这里介绍了多种情况下的启动方式:(启动前先运行 docker pull ***)

 

```
docker run --name some-redis -d redis
```

　　用docker inspect 镜像id 可看到镜像的相关信息,直接用上述命令启动默认暴露6379端口,正常情况下都是不需要修改的

　　如果需要添加持久化仓库

```
docker run --name some-redis -d redis redis-server --appendonly yes
```

　　数据默认存储在VOLUME /data目录下,使用`--volumes-from some-volume-container` 或者 `-v /docker/host/dir:/data 可实现挂载`

　　如果应用需要连接redis

```
docker run --name some-app --link some-redis:redis -d application-that-uses-redis
```

　　或者

```
docker run -it --link some-redis:redis --rm redis redis-cli -h redis -p 6379
```

　　如果想使用自己的配置文件启动redis,则在其基础上写一个dockerfile

```
FROM redis
COPY redis.conf /usr/local/etc/redis/redis.conf
CMD [ "redis-server", "/usr/local/etc/redis/redis.conf" ]
```

　　或者在启动命令中修改配置

```
docker run -v /myredis/conf/redis.conf:/usr/local/etc/redis/redis.conf --name myredis redis redis-server /usr/local/etc/redis/redis.conf
```