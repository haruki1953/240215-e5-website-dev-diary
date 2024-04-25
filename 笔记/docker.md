https://pan.baidu.com/s/1inZog6YV0f3_Qqx2WDNJag&pwd=9988
https://www.bilibili.com/video/BV1HP4118797/
https://b11et3un53m.feishu.cn/wiki/MWQIw4Zvhil0I5ktPHwcoqZdnec

[docker笔记](assets/day02-Docker%20-%20飞书云文档%201.pdf)
[Linux环境搭建](assets/Linux环境搭建%20-%20飞书云文档.pdf)
[安装Docker](assets/安装Docker%20-%20飞书云文档.pdf)

又给吃灰好长时间的ubuntu开机了，开不开，弄了半天发现是硬盘电源的问题，换个接头就好了，暂时是通过宝塔面板装了docker

hub.docker.com

# 一、快速入门
## 1.1 部署MySQL
```PowerShell
docker run -d \
  --name mysql \
  -p 3306:3306 \
  -e TZ=Asia/Shanghai \
  -e MYSQL_ROOT_PASSWORD=123 \
  mysql
```
![](assets/Pasted%20image%2020240417103306.png)

## 1.2 命令解读
解读：
- `docker run -d` ：创建并运行一个容器，`-d`则是让容器以后台进程运行
- `--name mysql` : 给容器起个名字叫`mysql`，你可以叫别的
- `-p 3306:3306` : 设置端口映射。
    - **容器是隔离环境**，外界不可访问。但是可以**将****宿主机****端口****映射容器内到端口**，当访问宿主机指定端口时，就是在访问容器内的端口了。
    - 容器内端口往往是由容器内的进程决定，例如MySQL进程默认端口是3306，因此容器内端口一定是3306；而宿主机端口则可以任意指定，一般与容器内保持一致。
    - 格式： `-p 宿主机端口:容器内端口`，示例中就是将宿主机的3306映射到容器内的3306端口
- `-e TZ=Asia/Shanghai` : 配置容器内进程运行时的一些参数
    - 格式：`-e KEY=VALUE`，KEY和VALUE都由容器内进程决定
    - 案例中，`TZ=Asia/Shanghai`是设置时区；`MYSQL_ROOT_PASSWORD=123`是设置MySQL默认密码
- `mysql` : 设置**镜像**名称，Docker会根据这个名字搜索并下载镜像
    - 格式：`REPOSITORY:TAG`，例如`mysql:8.0`，其中`REPOSITORY`可以理解为镜像名，`TAG`是版本号
    - 在未指定`TAG`的情况下，默认是最新版本，也就是`mysql:latest`


# 二、Docker基础
## 2.1 常见命令

|**命令**|**说明**|**文档地址**|
|---|---|---|
|docker pull|拉取镜像|[docker pull](https://docs.docker.com/engine/reference/commandline/pull/)|
|docker push|推送镜像到DockerRegistry|[docker push](https://docs.docker.com/engine/reference/commandline/push/)|
|docker images|查看本地镜像|[docker images](https://docs.docker.com/engine/reference/commandline/images/)|
|docker rmi|删除本地镜像|[docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/)|
|docker run|创建并运行容器（不能重复创建）|[docker run](https://docs.docker.com/engine/reference/commandline/run/)|
|docker stop|停止指定容器|[docker stop](https://docs.docker.com/engine/reference/commandline/stop/)|
|docker start|启动指定容器|[docker start](https://docs.docker.com/engine/reference/commandline/start/)|
|docker restart|重新启动容器|[docker restart](https://docs.docker.com/engine/reference/commandline/restart/)|
|docker rm|删除指定容器|[docs.docker.com](https://docs.docker.com/engine/reference/commandline/rm/)|
|docker ps|查看容器|[docker ps](https://docs.docker.com/engine/reference/commandline/ps/)|
|docker logs|查看容器运行日志|[docker logs](https://docs.docker.com/engine/reference/commandline/logs/)|
|docker exec|进入容器|[docker exec](https://docs.docker.com/engine/reference/commandline/exec/)|
|docker save|保存镜像到本地压缩文件|[docker save](https://docs.docker.com/engine/reference/commandline/save/)|
|docker load|加载本地压缩文件到镜像|[docker load](https://docs.docker.com/engine/reference/commandline/load/)|
|docker inspect|查看容器详细信息|[docker inspect](https://docs.docker.com/engine/reference/commandline/inspect/)|

![](assets/Pasted%20image%2020240417104053.png) 

补充：
默认情况下，每次重启虚拟机我们都需要手动启动Docker和Docker中的容器。通过命令可以实现开机自启：
```PowerShell
# Docker开机自启
systemctl enable docker

# Docker容器开机自启
docker update --restart=always [容器名/容器id]
```

### 演示：
我们以Nginx为例给大家演示上述命令。
```PowerShell
# 第1步，去DockerHub查看nginx镜像仓库及相关信息

# 第2步，拉取Nginx镜像
docker pull nginx

# 第3步，查看镜像
docker images
# 结果如下：
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
nginx        latest    605c77e624dd   16 months ago   141MB
mysql        latest    3218b38490ce   17 months ago   516MB

# 保存镜像到本地压缩文件
docker save -o nginx.tar nginx:latest
# 删除镜像
docker rmi nginx:latest
# 加载本地压缩文件到镜像
docker load -i nginx.tar

# 第4步，创建并允许Nginx容器
docker run -d --name nginx -p 80:80 nginx

# 第5步，查看运行中容器
docker ps
# 也可以加格式化方式访问，格式会更加清爽
docker ps --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}"

# 第6步，访问网页，地址：http://虚拟机地址

# 第7步，停止容器
docker stop nginx

# 第8步，查看所有容器（包括已停止）
docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}"

# 第9步，再次启动nginx容器
docker start nginx

# 第10步，再次查看容器
docker ps --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}"

# 第11步，查看容器详细信息
docker inspect nginx

# 查看日志
docker logs nginx
# 持续跟踪日志
docker logs -f nginx

# 第12步，进入容器,控制台
docker exec -it nginx bash
# 或者，可以进入MySQL
docker exec -it mysql mysql -uroot -p

# 第13步，删除容器
docker rm nginx
# 发现无法删除，因为容器运行中，强制删除容器
docker rm -f nginx
```

### 命令别名
给常用Docker命令起别名，方便我们访问：
```PowerShell
# 修改/root/.bashrc文件
vi /root/.bashrc
内容如下：
# .bashrc

# User specific aliases and functions

alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'
alias dps='docker ps --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}"'
alias dis='docker images'

# Source global definitions
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi
```

然后，执行命令使别名生效
```PowerShell
source /root/.bashrc
```

## 2.2 数据卷
`/var/lib/docker/volumes`这个目录就是默认的存放所有容器数据卷的目录，其下再根据数据卷名称创建新目录，格式为`/数据卷名/_data`。

### 数据卷命令

|**命令**|**说明**|**文档地址**|
|---|---|---|
|docker volume create|创建数据卷|[docker volume create](https://docs.docker.com/engine/reference/commandline/volume_create/)|
|docker volume ls|查看所有数据卷|[docs.docker.com](https://docs.docker.com/engine/reference/commandline/volume_ls/)|
|docker volume rm|删除指定数据卷|[docs.docker.com](https://docs.docker.com/engine/reference/commandline/volume_prune/)|
|docker volume inspect|查看某个数据卷的详情|[docs.docker.com](https://docs.docker.com/engine/reference/commandline/volume_inspect/)|
|docker volume prune|清除数据卷|[docker volume prune](https://docs.docker.com/engine/reference/commandline/volume_prune/)|

注意：容器与数据卷的挂载要在创建容器时配置，对于创建好的容器，是不能设置数据卷的。而且**创建容器的过程中，数据卷会自动创建**。

教学**演示环节**：演示一下nginx的html目录挂载
```PowerShell
# 1.首先创建容器并指定数据卷，注意通过 -v 参数来指定数据卷
docker run -d --name nginx -p 80:80 -v html:/usr/share/nginx/html nginx

# 2.然后查看数据卷
docker volume ls
# 结果
DRIVER    VOLUME NAME
local     29524ff09715d3688eae3f99803a2796558dbd00ca584a25a4bbc193ca82459f
local     html

# 3.查看数据卷详情
docker volume inspect html
# 结果
[
    {
        "CreatedAt": "2024-05-17T19:57:08+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/html/_data",
        "Name": "html",
        "Options": null,
        "Scope": "local"
    }
]

# 4.查看/var/lib/docker/volumes/html/_data目录
ll /var/lib/docker/volumes/html/_data
# 可以看到与nginx的html目录内容一样，结果如下：
总用量 8
-rw-r--r--. 1 root root 497 12月 28 2021 50x.html
-rw-r--r--. 1 root root 615 12月 28 2021 index.html

# 5.进入该目录，并随意修改index.html内容
cd /var/lib/docker/volumes/html/_data
vi index.html

# 6.打开页面，查看效果

# 7.进入容器内部，查看/usr/share/nginx/html目录内的文件是否变化
docker exec -it nginx bash
```

教学**演示环节**：演示一下MySQL的匿名数据卷
```PowerShell
# 1.查看MySQL容器详细信息
docker inspect mysql
# 关注其中.Config.Volumes部分和.Mounts部分
```

我们关注两部分内容，第一是`.Config.Volumes`部分：
```JSON
{
  "Config": {
    // ... 略
    "Volumes": {
      "/var/lib/mysql": {}
    }
    // ... 略
  }
}
```

可以发现这个容器声明了一个本地目录，需要挂载数据卷，但是**数据卷未定义**。这就是匿名卷。
然后，我们再看结果中的`.Mounts`部分：
```JSON
{
  "Mounts": [
    {
      "Type": "volume",
      "Name": "29524ff09715d3688eae3f99803a2796558dbd00ca584a25a4bbc193ca82459f",
      "Source": "/var/lib/docker/volumes/29524ff09715d3688eae3f99803a2796558dbd00ca584a25a4bbc193ca82459f/_data",
      "Destination": "/var/lib/mysql",
      "Driver": "local",
    }
  ]
}
```

可以发现，其中有几个关键属性：
- Name：数据卷名称。由于定义容器未设置容器名，这里的就是匿名卷自动生成的名字，一串hash值。
- Source：宿主机目录
- Destination : 容器内的目录

上述配置是将容器内的`/var/lib/mysql`这个目录，与数据卷`29524ff09715d3688eae3f99803a2796558dbd00ca584a25a4bbc193ca82459f`挂载。于是在宿主机中就有了`/var/lib/docker/volumes/29524ff09715d3688eae3f99803a2796558dbd00ca584a25a4bbc193ca82459f/_data`这个目录。这就是匿名数据卷对应的目录，其使用方式与普通数据卷没有差别。

接下来，可以查看该目录下的MySQL的data文件：
```Bash
ls -l /var/lib/docker/volumes/29524ff09715d3688eae3f99803a2796558dbd00ca584a25a4bbc193ca82459f/_data
```

注意：每一个不同的镜像，将来创建容器后内部有哪些目录可以挂载，可以参考DockerHub对应的页面

### 挂载本地目录或文件
```Bash
# 挂载本地目录
-v 本地目录:容器内目录
# 挂载本地文件
-v 本地文件:容器内文件
```

**注意**：本地目录或文件必须以 `/` 或 `./`开头，如果直接以名字开头，会被识别为数据卷名而非本地目录名。

例如：
```Bash
-v mysql:/var/lib/mysql # 会被识别为一个数据卷叫mysql，运行时会自动创建这个数据卷
-v ./mysql:/var/lib/mysql # 会被识别为当前目录下的mysql目录，运行时如果不存在会创建目录
```


## 2.3 镜像
如何自定义镜像
### 镜像结构
![](assets/Pasted%20image%2020240417132108.png)

### Dockerfile
[https://docs.docker.com/engine/reference/builder/](https://docs.docker.com/engine/reference/builder/)

|**指令**|**说明**|**示例**|
|---|---|---|
|**FROM**|指定基础镜像|`FROM centos:6`|
|**ENV**|设置环境变量，可在后面指令使用|`ENV key value`|
|**COPY**|拷贝本地文件到镜像的指定目录|`COPY ./xx.jar /tmp/app.jar`|
|**RUN**|执行Linux的shell命令，一般是安装过程的命令|`RUN yum install gcc`|
|**EXPOSE**|指定容器运行时监听的端口，是给镜像使用者看的|EXPOSE 8080|
|**ENTRYPOINT**|镜像中应用的启动命令，容器运行时调用|ENTRYPOINT java -jar xx.jar|

例如，要基于Ubuntu镜像来构建一个Java应用，其Dockerfile内容如下：
```Dockerfile
# 指定基础镜像
FROM ubuntu:16.04
# 配置环境变量，JDK的安装目录、容器内时区
ENV JAVA_DIR=/usr/local
ENV TZ=Asia/Shanghai
# 拷贝jdk和java项目的包
COPY ./jdk8.tar.gz $JAVA_DIR/
COPY ./docker-demo.jar /tmp/app.jar
# 设定时区
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
# 安装JDK
RUN cd $JAVA_DIR \
 && tar -xf ./jdk8.tar.gz \
 && mv ./jdk1.8.0_144 ./java8
# 配置环境变量
ENV JAVA_HOME=$JAVA_DIR/java8
ENV PATH=$PATH:$JAVA_HOME/bin
# 指定项目监听的端口
EXPOSE 8080
# 入口，java项目的启动命令
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

```Dockerfile
# 基础镜像
FROM openjdk:11.0-jre-buster
# 设定时区
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
# 拷贝jar包
COPY docker-demo.jar /app.jar
# 入口
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

### 构建镜像
```Bash
# 进入镜像目录
cd /root/demo
# 开始构建
docker build -t docker-demo:1.0 .
```

命令说明：
- `docker build` : 就是构建一个docker镜像
- `-t docker-demo:1.0` ：`-t`参数是指定镜像的名称（`repository`和`tag`）
- `.` : 最后的点是指构建时Dockerfile所在路径，由于我们进入了demo目录，所以指定的是`.`代表当前目录，也可以直接指定Dockerfile目录：
    ```Bash
    # 直接指定Dockerfile目录
    docker build -t docker-demo:1.0 /root/demo
    ```


## 2.4 网络
[https://docs.docker.com/engine/reference/commandline/network/](https://docs.docker.com/engine/reference/commandline/network/)

|**命令**|**说明**|**文档地址**|
|---|---|---|
|docker network create|创建一个网络|[docker network create](https://docs.docker.com/engine/reference/commandline/network_create/)|
|docker network ls|查看所有网络|[docs.docker.com](https://docs.docker.com/engine/reference/commandline/network_ls/)|
|docker network rm|删除指定网络|[docs.docker.com](https://docs.docker.com/engine/reference/commandline/network_rm/)|
|docker network prune|清除未使用的网络|[docs.docker.com](https://docs.docker.com/engine/reference/commandline/network_prune/)|
|docker network connect|使指定容器连接加入某网络|[docs.docker.com](https://docs.docker.com/engine/reference/commandline/network_connect/)|
|docker network disconnect|使指定容器连接离开某网络|[docker network disconnect](https://docs.docker.com/engine/reference/commandline/network_disconnect/)|
|docker network inspect|查看网络详细信息|[docker network inspect](https://docs.docker.com/engine/reference/commandline/network_inspect/)|

```Bash
# 1.首先通过命令创建一个网络
docker network create hmall

# 2.然后查看网络
docker network ls
# 结果：
NETWORK ID     NAME      DRIVER    SCOPE
639bc44d0a87   bridge    bridge    local
403f16ec62a2   hmall     bridge    local
0dc0f72a0fbb   host      host      local
cd8d3e8df47b   none      null      local
# 其中，除了hmall以外，其它都是默认的网络

# 3.让dd和mysql都加入该网络，注意，在加入网络时可以通过--alias给容器起别名
# 这样该网络内的其它容器可以用别名互相访问！
# 3.1.mysql容器，指定别名为db，另外每一个容器都有一个别名是容器名
docker network connect hmall mysql --alias db
# 3.2.db容器，也就是我们的java项目
docker network connect hmall dd

# 4.进入dd容器，尝试利用别名访问db
# 4.1.进入容器
docker exec -it dd bash
# 4.2.用db别名访问
ping db
# 结果
PING db (172.18.0.2) 56(84) bytes of data.
64 bytes from mysql.hmall (172.18.0.2): icmp_seq=1 ttl=64 time=0.070 ms
64 bytes from mysql.hmall (172.18.0.2): icmp_seq=2 ttl=64 time=0.056 ms
# 4.3.用容器名访问
ping mysql
# 结果：
PING mysql (172.18.0.2) 56(84) bytes of data.
64 bytes from mysql.hmall (172.18.0.2): icmp_seq=1 ttl=64 time=0.044 ms
64 bytes from mysql.hmall (172.18.0.2): icmp_seq=2 ttl=64 time=0.054 ms
```

在自行创建的网络中，可以通过容器名访问

# 三、项目部署
示例项目说明：
- hmall：商城的后端代码
- hmall-portal：商城用户端的前端代码
- hmall-admin：商城管理端的前端代码

部署的容器及端口说明：

|**项目**|**容器名**|**端口**|**备注**|
|---|---|---|---|
|hmall|hmall|8080|黑马商城后端API入口|
|hmall-portal|nginx|18080|黑马商城用户端入口|
|hmall-admin|18081|黑马商城管理端入口|
|mysql|mysql|3306|数据库|

## DockerCompose
Docker Compose可以帮助我们实现 **多个相互关联的Docker容器的快速部署**。它允许用户通过一个单独的 docker-compose.yml 模板文件（YAML 格式）来定义一组相关联的应用容器。

### 基本语法
https://docs.docker.com/compose/compose-file/compose-file-v3/

docker-compose文件中可以定义多个相互关联的应用容器，每一个应用容器被称为一个服务（service）。由于service就是在定义某个应用的运行时参数，因此与`docker run`参数非常相似。

举例来说，用docker run部署MySQL的命令如下：
```Bash
docker run -d \
  --name mysql \
  -p 3306:3306 \
  -e TZ=Asia/Shanghai \
  -e MYSQL_ROOT_PASSWORD=123 \
  -v ./mysql/data:/var/lib/mysql \
  -v ./mysql/conf:/etc/mysql/conf.d \
  -v ./mysql/init:/docker-entrypoint-initdb.d \
  --network hmall
  mysql
```

如果用`docker-compose.yml`文件来定义，就是这样：
```YAML
version: "3.8"

services:
  mysql:
    image: mysql
    container_name: mysql
    ports:
      - "3306:3306"
    environment:
      TZ: Asia/Shanghai
      MYSQL_ROOT_PASSWORD: 123
    volumes:
      - "./mysql/conf:/etc/mysql/conf.d"
      - "./mysql/data:/var/lib/mysql"
    networks:
      - new
networks:
  new:
    name: hmall
```

对比如下：

|**docker run 参数**|**docker compose 指令**|**说明**|
|---|---|---|
|--name|container_name|容器名称|
|-p|ports|端口映射|
|-e|environment|环境变量|
|-v|volumes|数据卷配置|
|--network|networks|网络|

黑马商城部署文件：
```YAML
version: "3.8"

services:
  mysql:
    image: mysql
    container_name: mysql
    ports:
      - "3306:3306"
    environment:
      TZ: Asia/Shanghai
      MYSQL_ROOT_PASSWORD: 123
    volumes:
      - "./mysql/conf:/etc/mysql/conf.d"
      - "./mysql/data:/var/lib/mysql"
      - "./mysql/init:/docker-entrypoint-initdb.d"
    networks:
      - hm-net
  hmall:
    build: 
      context: .
      dockerfile: Dockerfile
    container_name: hmall
    ports:
      - "8080:8080"
    networks:
      - hm-net
    depends_on:
      - mysql
  nginx:
    image: nginx
    container_name: nginx
    ports:
      - "18080:18080"
      - "18081:18081"
    volumes:
      - "./nginx/nginx.conf:/etc/nginx/nginx.conf"
      - "./nginx/html:/usr/share/nginx/html"
    depends_on:
      - hmall
    networks:
      - hm-net
networks:
  hm-net:
    name: hmall
```

可以在项目部署的同时构建镜像、创建网络

depends_on，依赖，使当前容器在列出的容器之后创建

### 基础命令
编写好docker-compose.yml文件，就可以部署项目了。常见的命令：
https://docs.docker.com/compose/reference/

基本语法如下：
```Bash
docker compose [OPTIONS] [COMMAND]
```

其中，OPTIONS和COMMAND都是可选参数，比较常见的有：

|**类型**|**参数或指令**|**说明**|
|---|---|---|
|Options|-f|指定compose文件的路径和名称|
|-p|指定project名称。project就是当前compose文件中设置的多个service的集合，是逻辑概念|
|Commands|up|创建并启动所有service容器|
|down|停止并移除所有容器、网络|
|ps|列出所有启动的容器|
|logs|查看指定容器的日志|
|stop|停止容器|
|start|启动容器|
|restart|重启容器|
|top|查看运行的进程|
|exec|在指定的运行中容器中执行命令|

教学演示：
```Bash
# 1.进入root目录
cd /root

# 2.删除旧容器
docker rm -f $(docker ps -qa)

# 3.删除hmall镜像
docker rmi hmall

# 4.清空MySQL数据
rm -rf mysql/data

# 5.启动所有, -d 参数是后台启动
docker compose up -d
# 结果：
[+] Building 15.5s (8/8) FINISHED
 => [internal] load build definition from Dockerfile                                    0.0s
 => => transferring dockerfile: 358B                                                    0.0s
 => [internal] load .dockerignore                                                       0.0s
 => => transferring context: 2B                                                         0.0s
 => [internal] load metadata for docker.io/library/openjdk:11.0-jre-buster             15.4s
 => [1/3] FROM docker.io/library/openjdk:11.0-jre-buster@sha256:3546a17e6fb4ff4fa681c3  0.0s
 => [internal] load build context                                                       0.0s
 => => transferring context: 98B                                                        0.0s
 => CACHED [2/3] RUN ln -snf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && echo   0.0s
 => CACHED [3/3] COPY hm-service.jar /app.jar                                           0.0s
 => exporting to image                                                                  0.0s
 => => exporting layers                                                                 0.0s
 => => writing image sha256:32eebee16acde22550232f2eb80c69d2ce813ed099640e4cfed2193f71  0.0s
 => => naming to docker.io/library/root-hmall                                           0.0s
[+] Running 4/4
 ✔ Network hmall    Created                                                             0.2s
 ✔ Container mysql  Started                                                             0.5s
 ✔ Container hmall  Started                                                             0.9s
 ✔ Container nginx  Started                                                             1.5s

# 6.查看镜像
docker compose images
# 结果
CONTAINER           REPOSITORY          TAG                 IMAGE ID            SIZE
hmall               root-hmall          latest              32eebee16acd        362MB
mysql               mysql               latest              3218b38490ce        516MB
nginx               nginx               latest              605c77e624dd        141MB

# 7.查看容器
docker compose ps
# 结果
NAME                IMAGE               COMMAND                  SERVICE             CREATED             STATUS              PORTS
hmall               root-hmall          "java -jar /app.jar"     hmall               54 seconds ago      Up 52 seconds       0.0.0.0:8080->8080/tcp, :::8080->8080/tcp
mysql               mysql               "docker-entrypoint.s…"   mysql               54 seconds ago      Up 53 seconds       0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp
nginx               nginx               "/docker-entrypoint.…"   nginx               54 seconds ago      Up 52 seconds       80/tcp, 0.0.0.0:18080-18081->18080-18081/tcp, :::18080-18081->18080-18081/tcp
```

打开浏览器，访问：http://yourIp:8080


# node项目部署
## 如何挑选node_docker镜像
https://www.cnblogs.com/woshimrf/p/node-docker-image.html
https://blog.csdn.net/weixin_53312997/article/details/129046231

https://hub.docker.com

比较小的镜像有：
- `slim (bullseye-slim)`，node:20.12.2-bullseye-slim
- `alpine`，node:20.12.2-alpine3.19

搜索镜像tag，找到lts的如 `lts-bullseye-slim`
然后根据其 Digest 选择与之相同的具体版本号的tag（确定性标记）
如：`20.12.2-bullseye-slim`

alpine是最小的，但可能有兼容性问题

## docker打包
Dockerfile
```Dockerfile
# FROM node:20.12.2-bullseye-slim
# 使用更小的 node:20.12.2-alpine3.19
FROM node:20.12.2-alpine3.19

# 配置环境变量
ENV TZ=Asia/Shanghai

# 设置工作目录
WORKDIR /app

# 复制 package.json 和 pnpm-lock.yaml 文件到工作目录
COPY package.json .
COPY pnpm-lock.yaml .

# 设置代理
ENV http_proxy=http://192.168.2.110:10811/
ENV https_proxy=http://192.168.2.110:10811/

# 安装 pnpm
RUN npm install -g pnpm

# 安装项目依赖
RUN pnpm install

# 取消代理
ENV http_proxy=
ENV https_proxy=

# 复制应用程序代码到工作目录
COPY . .

# 暴露默认端口
EXPOSE 23769

# 定义容器启动时运行的命令
ENTRYPOINT ["node", "app.js"]
```

构建
```
docker build -t project:tag .
```

运行
```sh
docker run -d \
	--name project_name \
	-p 端口:容器内端口 \
	-v /数据卷/data:/app/data \
	--restart unless-stopped \
	project:tag
```

导出与加载镜像
```
docker save -o project_tag.tar project:tag
docker load -i project_tag.tar
```

