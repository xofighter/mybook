![1559110274690](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1559110274690.png)

![1559110591324](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1559110591324.png)

![1559110656282](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1559110656282.png)

![1559110804637](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1559110804637.png)

![1559110892429](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1559110892429.png)

![1559111067978](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1559111067978.png)

Dockerfile

```dockerfile
FROM anapsix/alpine-java:8_server-jre_unlimited

MAINTAINER qiao.hanping@e-ports.com

# 设置时区为上海
RUN apk add tzdata && cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo "Asia/Shanghai" > /etc/timezone \
    && apk del tzdata

RUN mkdir -p /ad/bin/eports/ad-consumer

WORKDIR /ad/bin/eports/ad-consumer

EXPOSE 8889

ADD ad-consumer.jar ./

CMD java -XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap -Djava.security.egd=file:/dev/./urandom -jar -Dspring.profiles.active=${SPRING_PROFILES_ACTIVE} ad-consumer.jar

```

build.sh

```shell
#!/bin/bash

docker stop ad-consumer_test && docker rm ad-consumer_test && docker rmi ad-consumer

docker build -t ad-consumer .

docker run -d -p 8889:8889 --name ad-consumer_test -m 2048m --memory-swap -1 --restart=always ad-consumer

# rm -rf ad-consumer.jar

```

