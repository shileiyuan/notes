## 命令

### image

```bash
# 列出本机的所有 image 文件
docker image ls

# 删除 image 文件
docker image rm [imageName]

# 将image文件从仓库抓取到本地
docker image pull library/hello-world

# 运行image文件，该image运行之后，容器会自动终止
docker image run hello-world
```



### 容器

```bash
# 运行 ubuntu
docker run --name=ubuntu -it ubuntu /bin/bash

# 停止容器运行
docker stop ubuntu

# 启动容器
docker start -i ubuntu

# 查看正在运行的容器
docker ps

# 查看所有容器
docker ps -a

# 删除容器
docker rm ubuntu

# 如果启动ubuntu的时候，没有加上-i参数，可以手动附着到容器上
docker attach ubuntu

```



### 守护式容器

```bash
# -d 参数会让docker将容器放到后台运行
# while循环会一直打印hello world
doker run --name daemon -d ubuntu /bin/sh -c "while true; do echo Hello World; sleep 1; done"

# 查看容器的日志
docker logs daemon

# -f 参数会动态输出日志
docker logs -f daemon

# 只输出最后10条日志
docker logs --tail 10 daemon

# 动态输出容器的最新日志而不必读取整个日志文件
docker logs --tail 0 -f daemon

# -t 参数为每条日志加上时间戳
docker logs -ft daemon


# 查看容器内部运行的进程
docker top daemon

# 查看一个或多个容器的统计信息: CPU、内存、网络IO及存储IO
docker stats daemon ubuntu

# 在容器内部启动新进程

# 启动后台任务
docker exec -d daemon touch /root/abc.txt

# 启动交互任务
docker exec -it daemon /bin/bash
```













