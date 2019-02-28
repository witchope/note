# Dockerfile 最佳实践

## FROM

用于设置容器基础镜像

```Dockerfile
FROM scrath # 基于 Base image
FROM centos
```

PS： 尽量使用官方的 image 作为 Base image

## LABEL

用于添加容器 `metadata`

```Dockerfile
LABEL version="1.0"
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

## WORKDIR

用于目录跳转

```Dockerfile
WORKDIR /root # 相当于cd /root
WORKDIR demo
RUN pwd # /root/demo
```

PS：用 `WORDDIR` ， 不要使用 `RUN cd` ，尽量使用绝对目录！

## ADD & COPY

添加或者拷贝本地目录或文件 (new layer)

```Dockerfile
ADD test.tar.gz # 添加到根目录并解压
COPY hello /root # 添加到 /root 目录
```

PS：大部分时候 `COPY` 要优于 `ADD`，不过 `ADD` 额外拥有解压的功能，添加远程文件/目录请使用 `curl` 或者 `wget` 。
