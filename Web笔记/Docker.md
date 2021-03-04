# Docker

## 容器生命周期管理

1. run

   创建一个新的容器并运行一个命令

   ```shell
   docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
   ```

   ```
   docker run -d -p 0.0.0.0:80:80 imageid
   ```

   | 参数                  | 说明                                              |
   | --------------------- | ------------------------------------------------- |
   | -d                    | 后台运行容器                                      |
   | -e username="ritchie" | 设置环境变量                                      |
   | -i                    | 以交互模式运行容器                                |
   | -p                    | 指定端口映射，格式为：**主机(宿主)端口:容器端口** |
   | -P                    | 随机端口映射，容器内部端口**随机**映射到主机端口  |
   | -t                    | 为容器重新分配一个伪输入终端                      |
   | --expose port         | 开放一个端口或一组端口                            |
   | --env-file path       | 从指定文件读入环境变量                            |
   | --name="nginx-lb"     | 为容器指定一个名称                                |

   

2. start/stop/restart

3. kill

4. rm

5. pause/unpause

6. create

   同docker run

7. exec

   ```shell
   docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
   ```

   ```shell
   docker exec -it containerid /bin/sh
   ```

   | 参数 | 说明           |
   | ---- | -------------- |
   | -d   | 后台运行       |
   | -i   | STDIN打开      |
   | -t   | 分配一个伪终端 |

   

## 容器操作

1. ps
2. inspect
3. top
4. attach
5. events
6. port

## 容器rootfs命令

1. commit
2. cp
3. diff

## 镜像仓库

1. login
2. pull
3. push
4. search

## 本地镜像管理

1. images
2. rmi
3. tag
4. build
5. history
6. save
7. load
8. import

## info|version

1. info
2. version

## Docker 暂停所有容器

```shell
docker stop $(docker ps -aq)
```

## Docker 删除所有镜像

```shell
docker rmi -f $(docker images -aq)
```

## Docker-compose

安装apt-get install -y compose

常用语法：docker-compose up -d

[Dockerfile]('https://docs.docker.com/engine/reference/builder')


```dockerfile
FROM 远程仓库：镜像
WORKDIR 工作目录
ENV 设置环境变量
RUN 命令
RUN <command> (shell form, the command is run in a shell, which by default is /bin/sh -c on Linux or cmd /S /C on Windows)
RUN ["executable", "param1", "param2"] (exec form)
COPY 复制文件
EXPOSE 端口
CMD 命令
CMD ["executable","param1","param2"] (exec form, this is the preferred form)
CMD ["param1","param2"] (as default parameters to ENTRYPOINT)
CMD command param1 param2 (shell form)
ENTRYPOINT 命令
ENTRYPOINT ["executable", "param1", "param2"]
ENTRYPOINT command param1 param2
```

```dockerfile
FROM python:3.7-alpine
WORKDIR /code
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0
RUN apk add --no-cache gcc musl-dev linux-headers
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
EXPOSE 5000
COPY . .
CMD ["flask", "run"]
```

RUN、CMD、ENTRYPOINT三者区别

1. RUN 一般用于执行命令并创建新的镜像层，通常用于安装软件包。
2. CMD 设置容器启动后默认执行的命令及其参数，作为ENTRYPOINT的参数时，会被run的参数覆盖。
3. ENTRYPOINT 配置容器启动时的执行命令。

[docker-compose.yml]('https://docs.docker.com/compose/gettingstarted/')

```dockerfile
version: "3.9"
services:
  web:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - .:/code
    environment:
      FLASK_ENV: development
  redis:
    image: "redis:alpine"
```

