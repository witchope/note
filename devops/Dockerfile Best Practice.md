# Dockerfile 最佳实践

## FROM

用于设置容器基础镜像

```Dockerfile
FROM scrath # 基于 Base image
FROM alpine:latest
```

PS： 尽量使用官方的 `image` 作为 `Base image`，尤其推荐 [Alpine][alpine] 镜像，因为其体积严格控制（仅为 5 MB），且是一个完整的 Linux 分发版本。

## LABEL

用于添加容器 `metadata`，用于描述容器相关特性

```Dockerfile
LABEL version="1.0"
LABEL com.example.release-date="2018-09-16"
```

PS： `metadata` 不能少！

## RUN

用于构建期间，执行命令，并基于现有镜像层，生成一层新的镜像层 (image layer)。

```Dockerfile
RUN ["/bin/bash", "-c", "source $HOME/.bashrc"]
RUN /bin/bash -c 'source $HOME/.bashrc; \
echo $HOME'
```

PS： 为了美观，复杂的命令请使用反斜线 `"\"` 进行换行，避免无用分层，合并多条命令成一行！

## CMD

用于容器应用启动时运行，多条 `CMD` 只有最后一条生效。

```Dockerfile
CMD ["apache2","-DFOREGROUND"]
```

PS： 尽量使用 `CMD ["executable", "param1", "param2"…]` 这种方式编写命令，而且须指定 `shell` , 例如：`bash` 、`python` 、 `perl` 。

## ADD or COPY

添加或者拷贝本地目录或文件 (new layer)

```Dockerfile
ADD test.tar.gz # 添加到根目录并解压
COPY hello /root # 添加到 /root 目录
```

PS：大部分时候 `COPY` 要优于 `ADD`，不过 `ADD` 额外拥有解压的功能，添加远程文件/目录请使用 `curl` 或者 `wget` 。

## EXPOSE

暴露需要监听的接口

```Dockerfile
EXPOSE 80 # Apache
EXPOSE 27017 # MongoDB
```

## ENV

设置环境变量，例如：`ENV PATH /usr/local/nginx/bin:$PATH`, 能确保 `CMD ["nginx"]` 的运行。

## ENTRYPOINT

用于设置容器的主运行命令

```Dockerfile
COPY ./docker-entrypoint.sh /
ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["postgres"]
```

docker-entrypoint.sh

```sh
#!/bin/bash
set -e

if [ "$1" = 'postgres' ]; then
    chown -R postgres "$PGDATA"

    if [ -z "$(ls -A "$PGDATA")" ]; then
        gosu postgres initdb
    fi

    exec gosu postgres "$@"
fi

exec "$@"

```


## WORKDIR

用于目录跳转

```Dockerfile
WORKDIR /root # 相当于cd /root
WORKDIR demo
RUN pwd # /root/demo
```

PS：用 `WORDDIR` ， 不要使用 `RUN cd` ，尽量使用绝对目录！


[alpine]: https://hub.docker.com/_/alpine/