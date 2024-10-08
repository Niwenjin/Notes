# Docker — 从入门到实践

## 目录

[简介](#简介)  
[基本概念](#基本概念)  
[使用 Docker 镜像](#使用-docker-镜像)  
[操作 Docker 容器](#操作-docker-容器)  
[访问 Docker 仓库](#访问-docker-仓库)  
[Docker 数据管理](#docker-数据管理)  
[使用网络](#使用网络)  
[Docker Compose](#docker-compose)

## 简介

Docker 使用 Google 公司推出的 Go 语言 进行开发实现，基于 Linux 内核的 cgroup，namespace，以及 OverlayFS 类的 Union FS 等技术，对进程进行封装隔离，属于操作系统层面的虚拟化技术。由于隔离的进程独立于宿主和其它的隔离的进程，因此也称其为容器。

Docker 在容器的基础上，进行了进一步的封装，从文件系统、网络互联到进程隔离等等，极大的简化了容器的创建和维护。使得 Docker 技术比虚拟机技术更为轻便、快捷。

传统虚拟机技术是虚拟出一套硬件后，在其上运行一个完整操作系统，在该系统上再运行所需应用进程；而容器内的应用进程直接运行于宿主的内核，容器内没有自己的内核，而且也没有进行硬件虚拟。因此容器要比传统虚拟机更为轻便。

作为一种新兴的虚拟化方式，Docker 跟传统的虚拟化方式相比具有众多的优势：

-   更高效的利用系统资源
-   更快速的启动时间
-   一致的运行环境
-   持续交付和部署
-   更轻松的迁移
-   更轻松的维护和扩展

## 基本概念

Docker 包括三个基本概念：

-   镜像（Image）
-   容器（Container）
-   仓库（Repository）

### Docker 镜像

**Docker 镜像**是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。镜像不包含任何动态数据，其内容在构建之后也不会被改变。

对于 Linux 而言，内核启动后，会挂载 root 文件系统为其提供用户空间支持。而 Docker 镜像（Image），就相当于是一个 root 文件系统。比如官方镜像 `ubuntu:18.04` 就包含了完整的一套 `Ubuntu 18.04` 最小系统的 root 文件系统。

因为镜像包含操作系统完整的 root 文件系统，其体积往往是庞大的，因此在 Docker 设计时，就充分利用 Union FS 的技术，将其设计为**分层存储**的架构。所以严格来说，镜像并非是像一个 ISO 那样的打包文件，镜像只是一个虚拟的概念，其实际体现并非由一个文件组成，而是由一组文件系统组成，或者说，由多层文件系统联合组成。

镜像构建时，会一层层构建，前一层是后一层的基础。每一层构建完就不会再发生改变，后一层上的任何改变只发生在自己这一层。比如，删除前一层文件的操作，实际不是真的删除前一层的文件，而是仅在当前层标记为该文件已删除。在最终容器运行的时候，虽然不会看到这个文件，但是实际上该文件会一直跟随镜像。因此，在构建镜像的时候，需要额外小心，每一层尽量只包含该层需要添加的东西，任何额外的东西应该在该层构建结束前清理掉。

分层存储的特征还使得镜像的复用、定制变的更为容易。甚至可以用之前构建好的镜像作为基础层，然后进一步添加新的层，以定制自己所需的内容，构建新的镜像。

### Docker 容器

镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的类和实例一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。

容器的实质是进程，但与直接在宿主执行的进程不同，容器进程运行于属于自己的独立的命名空间。因此容器可以拥有自己的 root 文件系统、自己的网络配置、自己的进程空间，甚至自己的用户 ID 空间。容器内的进程是运行在一个隔离的环境里，使用起来，就好像是在一个独立于宿主的系统下操作一样。这种特性使得容器封装的应用比直接在宿主运行更加安全。

镜像使用的是分层存储，容器也是如此。每一个容器运行时，是以镜像为基础层，在其上创建一个当前容器的存储层，这个为容器运行时读写而准备的存储层被称为**容器存储层**。

**数据卷**的生存周期独立于容器，容器消亡，数据卷不会消亡。因此，使用数据卷后，容器删除或者重新运行之后，数据却不会丢失。

_注意：容器存储层的生存周期和容器一样，容器消亡时，容器存储层也随之消亡。因此，任何保存于容器存储层的信息都会随容器删除而丢失。_

_最佳实践：容器不应该向其存储层内写入任何数据，容器存储层要保持无状态化。所有的文件写入操作，都应该使用数据卷（Volume）、或者绑定宿主目录，在这些位置的读写会跳过容器存储层，直接对宿主（或网络存储）发生读写，其性能和稳定性更高。_

### Docker Registry

Docker Registry 提供了集中的存储、分发镜像的服务。一个 Docker Registry 中可以包含多个**仓库**（Repository）；每个仓库可以包含多个**标签**（Tag）；每个标签对应一个**镜像**。

通常，一个仓库会包含同一个软件不同版本的镜像，而标签就常用于对应该软件的各个版本。我们可以通过 <仓库名>:<标签> 的格式来指定具体是这个软件哪个版本的镜像。如果不给出标签，将以 latest 作为默认标签。

以 Ubuntu 镜像 为例，ubuntu 是仓库的名字，其内包含有不同的版本标签，如，16.04, 18.04。我们可以通过 ubuntu:16.04，或者 ubuntu:18.04 来具体指定所需哪个版本的镜像。如果忽略了标签，比如 ubuntu，那将视为 ubuntu:latest。

Docker Registry 公开服务是开放给用户使用、允许用户管理镜像的 Registry 服务，最常使用的 Registry 公开服务是官方的 Docker Hub。除此以外，还有 Red Hat 的 Quay.io；Google 的 Google Container Registry，Kubernetes 的镜像使用的就是这个服务；代码托管平台 GitHub 推出的 ghcr.io。

## 使用 Docker 镜像

本章将介绍更多关于镜像的内容，包括：

-   从仓库获取镜像；
-   管理本地主机上的镜像；
-   介绍镜像实现的基本原理。

### 获取镜像

从 Docker 镜像仓库获取镜像的命令是 docker pull。其命令格式为：

```sh
$ docker pull [OPTIONS] NAME[:TAG|@DIGEST]
```

仓库名：如之前所说，这里的仓库名是两段式名称，即 <用户名>/<软件名>。对于 Docker Hub，如果不给出用户名，则默认为 library，也就是官方镜像。

Docker 镜像仓库地址：地址的格式一般是 <域名/IP>[:端口号]。默认地址是 Docker Hub(docker.io)。

镜像是由多层存储所构成，下载也是一层层的去下载，而并非单一文件。下载过程中给出了每一层的 ID 的前 12 位。并且下载结束后，给出该镜像完整的 sha256 的摘要，以确保下载一致性。

### 列出镜像

可以使用`docker images`或`docker image ls`列出已下载的镜像，列表包含了仓库名、标签、镜像 ID、创建时间以及所占用的空间。**镜像 ID** 是镜像的唯一标识，一个镜像可以对应多个**标签**。通过添加命令行参数，可以筛选列出的镜像。

```sh
# 根据仓库名列出镜像
$ docker image ls ubuntu
# 指定仓库名和标签
$ docker image ls ubuntu:22.04
# 指定过滤器
$ docker image ls -f since=mongo:3.2
```

列表中的**镜像体积**总和并非是所有镜像实际硬盘消耗。由于 Docker 镜像是多层存储结构，并且可以继承、复用，因此不同镜像可能会因为使用相同的基础镜像，从而拥有共同的层。由于 Docker 使用 Union FS，相同的层只需要保存一份即可，因此实际镜像硬盘占用空间很可能要比这个列表镜像大小的总和要小的多。可以通过`docker system df`命令来便捷的查看镜像、容器、数据卷所占用的空间。

有时可能会存在一种特殊的镜像，既没有仓库名，也没有标签，均为`<none>`，这种镜像被称为**虚悬镜像**，这通常是由于某个镜像的新版本发布，导致新旧镜像同名，旧镜像名称和标签被取消。可以用`docker image ls -f dangling=true`命令展示虚悬镜像。一般来说，虚悬镜像已经失去了存在的价值，是可以随意删除的，可以用`docker image prune`命令删除。

为了加速镜像构建、重复利用资源，Docker 会利用**中间层镜像**。所以在使用一段时间后，可能会看到一些依赖的中间层镜像。默认的 `docker image ls` 列表中只会显示顶层镜像，如果希望显示包括中间层镜像在内的所有镜像的话，需要加 -a 参数。与之前的虚悬镜像不同，这些无标签的镜像很多都是中间层镜像，是其它镜像所依赖的镜像。这些无标签镜像不应该删除，否则会导致上层镜像因为依赖丢失而出错。

### 删除本地镜像

要删除本地的镜像，可以使用 `docker image rm` 命令，其格式为：

```sh
$ docker image rm [选项] <镜像1> [<镜像2> ...]
# 其中，<镜像> 可以是 镜像短 ID、镜像长 ID、镜像名 或者 镜像摘要。
```

### 使用 Dockerfile 定制镜像

镜像的定制实际上就是定制每一层所添加的配置、文件。如果我们可以把每一层修改、安装、构建、操作的命令都写入一个脚本，用这个脚本来构建、定制镜像，那么之前提及的无法重复的问题、镜像构建透明性的问题、体积的问题就都会解决。这个脚本就是 Dockerfile。

#### FROM 指定基础镜像

`FROM` 用于指定**基础镜像**。一个 Dockerfile 中 `FROM` 是必备的指令，并且必须是第一条指令。有可以直接拿来使用的服务类的镜像，如 `nginx`、`redis`、`mongo`、`mysql`、`httpd`、`php`、`tomcat` 等；也有一些方便开发、构建、运行各种语言应用的镜像，如 `node`、`openjdk`、`python`、`ruby`、`golang` 等。可以在其中寻找一个最符合我们最终目标的镜像为基础镜像进行定制。如果没有找到对应服务的镜像，官方镜像中还提供了一些更为基础的操作系统镜像，如 `ubuntu`、`debian`、`centos`、`fedora`、`alpine` 等。

除了选择现有镜像为基础镜像外，Docker 还存在一个特殊的镜像，名为 `scratch`。这个镜像是虚拟的概念，并不实际存在，它表示一个空白的镜像。对于 Linux 下静态编译的程序来说，并不需要有操作系统提供运行时支持，所需的一切库都已经在可执行文件里了，因此直接 `FROM scratch` 会让镜像体积更加小巧。

#### RUN 执行命令

`RUN` 指令用于执行命令行命令。其格式有两种：

-   shell 格式：`RUN <命令>`，就像直接在命令行中输入的命令一样。
-   exec 格式：`RUN ["可执行文件", "参数1", "参数2"]`，这更像是函数调用中的格式。

Dockerfile 中每一个指令都会建立一层，每一个 RUN 的行为，都会新建立一层，在其上执行这些命令。执行结束后，commit 这一层的修改，构成新的镜像。应该只使用一个 RUN 指令，并使用 && 将各个所需命令串联起来。每一组命令的最后通常需要执行清理工作，一定要确保每一层只添加真正需要添加的东西，任何无关的东西都应该清理掉。

#### 构建镜像

`docker build`用于构建镜像，用法：

```sh
docker build [选项] <上下文路径/URL/->
```

#### 镜像构建上下文

当构建的时候，用户会指定构建镜像**上下文(context)**的路径，docker build 命令得知这个路径后，会将路径下的所有内容打包，然后上传给 Docker 引擎。这样 Docker 引擎收到这个上下文包后，展开就会获得构建镜像所需的一切文件。

如果在 Dockerfile 中执行如下命令：

```sh
COPY ./package.json /app/
```

实际上是将**上下文**路径下相对路径的文件或目录拷贝到容器的/app 目录下。

一般来说，应该将镜像所需文件或目录置于 Dockerfile 同级目录下。如果目录下有不需要传给 Docker 引擎的文件，可以用`.dockerignore`文件指定不需要作为上下文传递的文件或目录。

#### Dockerfile 指令详解

我们已经介绍了 FROM/RUN，还提及了 COPY/ADD，其实 Dockerfile 功能很强大，它提供了十多个指令。

**COPY 复制文件**

-   shell 格式：`COPY [--chown=<user>:<group>] <源路径>... <目标路径>`
-   exec 格式：`COPY [--chown=<user>:<group>] ["<源路径1>",... "<目标路径>"]`

COPY 指令将从构建上下文目录中 <源路径> 的文件/目录复制到新的一层的镜像内的 <目标路径> 位置。

_如果源路径为文件夹，复制的时候不是直接复制该文件夹，而是将文件夹中的内容复制到目标路径。_

**CMD 容器启动命令**

Docker 不是虚拟机，容器就是进程。既然是进程，那么在启动容器的时候，需要指定所运行的程序及参数。CMD 指令就是用于指定默认的容器主进程的启动命令的。

-   shell 格式：`CMD <命令>`
-   exec 格式：`CMD ["可执行文件", "参数1", "参数2"...]`

_在指令格式上，一般推荐使用 exec 格式，这类格式在解析时会被解析为 JSON 数组，因此一定要使用双引号 "，而不要使用单引号。_

**ENV 设置环境变量**

ENV 指令设置环境变量，无论是其他指令，如`RUN`，还是运行时的应用，都可以直接使用这里定义的环境变量。

-   shell 格式：`ENV <key> <value>`
-   exec 格式：`ENV <key1>=<value1> <key2>=<value2>...`

**ARG 构建参数**

构建参数和 ENV 的效果一样，都是设置环境变量。所不同的是，ARG 所设置的构建环境的环境变量，在将来容器运行时是不会存在这些环境变量的。

-   `ARG <参数名>[=<默认值>]`

**VOLUME 定义匿名卷**

我们可以事先指定某些目录挂载为匿名卷，任何向匿名卷中写入的信息都不会记录进容器存储层，从而保证了容器存储层的无状态化。

-   shell 格式：`VOLUME <路径>`
-   exec 格式：`VOLUME ["<路径1>", "<路径2>"...]`

**EXPOSE 声明端口**

EXPOSE 指令是声明运行时容器提供服务端口，这只是一个声明，在运行时并不会因为这个声明应用就会开启这个端口的服务。在 Dockerfile 中写入这样的声明有两个好处，一个是帮助镜像使用者理解这个镜像服务的守护端口，以方便配置映射；另一个用处则是在运行时使用随机端口映射时，也就是 docker run -P 时，会自动随机映射 EXPOSE 的端口。

-   `EXPOSE <端口1> [<端口2>...]`

**WORKDIR 指定工作目录**

使用 WORKDIR 指令可以来指定工作目录（或者称为当前目录），以后各层的当前目录就被改为指定的目录。

-   `WORKDIR <工作目录路径>`

**USER 指定当前用户**

USER 指令和 WORKDIR 相似，都是改变环境状态并影响以后的层。WORKDIR 是改变工作目录，USER 则是改变之后层的执行 RUN/CMD/ENTRYPOINT 这类命令的身份。

-   `USER <用户名>[:<用户组>]`

## 操作 Docker 容器

容器是独立运行的一个或一组应用，以及它们的运行态环境。本章将具体介绍如何来管理一个容器，包括创建、启动和停止等。

### 启动容器

启动容器有两种方式，一种是基于镜像新建一个容器并启动，另外一个是将在终止状态（stopped）的容器重新启动。

因为 Docker 的容器实在太轻量级了，很多时候用户都是随时删除和新创建容器。

#### 新建并启动

所需要的命令主要为 `docker run`。

docker run 常用的参数有：

```sh
-d: 以 detached 模式运行容器，即在后台运行。

-it: 这是两个参数的组合，-i 表示交互式，-t 分配一个伪终端。通常一起使用，用于创建一个交互式 shell。

--name: 为容器指定一个名称。

--rm: 容器退出时自动清理容器文件系统。

-v 或 --volume: 挂载一个卷到容器，用于数据持久化或共享。

-p 或 --publish: 将容器的端口映射到宿主机。

-e 或 --env: 设置环境变量。

--link: 链接到另一个容器。

--dns: 设置容器的 DNS 服务器。

--add-host: 添加自定义的 host 条目。

--network: 指定容器的网络连接。

--detach-keys: 指定容器退出的快捷键。

--cpus: 限制容器使用的 CPU 数量。

-m 或 --memory: 设置容器可以使用的最大内存。

-u 或 --user: 设置容器的用户。

-w 或 --workdir: 设置容器的工作目录。

--restart: 设置容器的重启策略。

--cap-add: 给容器添加 Linux 内核能力。

--entrypoint: 设置容器的入口点。

--privileged: 给容器提供扩展权限。
```

当利用 docker run 来创建容器时，Docker 在后台运行的标准操作包括：

-   检查本地是否存在指定的镜像，不存在就从公有仓库下载
-   利用镜像创建并启动一个容器
-   分配一个文件系统，并在只读的镜像层外面挂载一层可读写层
-   从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去
-   从地址池配置一个 ip 地址给容器
-   执行用户指定的应用程序
-   执行完毕后容器被终止

#### 启动已终止容器

可以利用 `docker container start` 命令，直接将一个已经终止的容器启动运行。

```sh
$ docker ps -a
...
$ docker container start xxx
```

### 后台运行

很多的时候，需要让 Docker 在后台运行而不是直接把执行命令的结果输出在当前宿主机下。此时，可以通过添加 -d 参数来实现。此时容器会在后台运行并不会把输出的结果 (STDOUT) 打印到宿主机，要查看标准输出的结果，需要使用`docker logs`命令查看。用法：

```sh
# docker logs 和 docker container logs 命令是等价的
$ docker container logs [container ID or NAMES]
```

_注意：容器是否会长久运行，是和 docker run 指定的命令有关，和 -d 参数无关。_

### 终止

当 Docker 容器中指定的应用终结时，容器会自动终止。想要手动终止运行中的容器，可以使用 `docker container stop` 命令。

通过 docker image ls -a 可以看到，终止状态的容器 STATUS 栏为 Exited。

处于终止状态的容器，可以通过 docker container start 命令来重新启动。

此外，docker container restart 命令会将一个运行态的容器终止，然后再重新启动它。

### 进入容器

使用 -d 参数时，容器启动后会进入后台。

某些时候需要进入容器进行操作，此时可以使用 docker attach 命令或 docker exec 命令。推荐使用 docker exec 命令，原因如下。

docker attach 命令：

```sh
$ docker run -dit ubuntu
243c32535da7d142fb0e6df616a3c3ada0b8ab417937c853a9e1c251f499f550

$ docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
243c32535da7        ubuntu:latest       "/bin/bash"         18 seconds ago      Up 17 seconds                           nostalgic_hypatia

$ docker attach 243c
root@243c32535da7:/#
```

_注意： 如果从这个 stdin 中 exit，会导致容器的停止。_

docker exec 命令:

```sh
$ docker run -dit ubuntu
69d137adef7a8a689cbcb059e94da5489d3cddd240ff675c640c8d96e84fe1f6

$ docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
69d137adef7a        ubuntu:latest       "/bin/bash"         18 seconds ago      Up 17 seconds                           zealous_swirles

$ docker exec -i 69d1 bash
ls
bin
boot
dev
...

$ docker exec -it 69d1 bash
root@69d137adef7a:/#
```

docker exec 命令是在已经启动的容器中运行一个新的 Bash，因此从该 Bash 中 exit 不会导致容器的停止。

### 导出和导入

如果要导出本地某个容器，可以使用 docker export 命令。

```sh
$ docker container ls -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                    PORTS               NAMES
7691a814370e        ubuntu:18.04        "/bin/bash"         36 hours ago        Exited (0) 21 hours ago                       test
$ docker export 7691a814370e > ubuntu.tar
```

可以使用 docker import 从容器快照文件中再导入为镜像，例如：

```sh
$ cat ubuntu.tar | docker import - test/ubuntu:v1.0
$ docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED              VIRTUAL SIZE
test/ubuntu         v1.0                9d37a6082e97        About a minute ago   171.3 MB
```

此外，也可以通过指定 URL 或者某个目录来导入，例如：

```sh
$ docker import http://example.com/exampleimage.tgz example/imagerepo
```

### 删除

可以使用 docker rm 或 docker container rm 来删除一个处于终止状态的容器。

如果要删除一个运行中的容器，可以添加 -f 参数。Docker 会发送 SIGKILL 信号给容器。

如果要清理所有处于终止状态的容器，可以执行 `docker container prune` 命令。

## 访问 Docker 仓库

仓库（Repository）是集中存放镜像的地方。

### Docker Hub

Docker 官方维护了一个公共仓库 Docker Hub，大部分需求都可以通过在 Docker Hub 中直接下载镜像来实现。可以通过执行 docker login 命令交互式的输入用户名及密码来完成在命令行界面登录 Docker Hub。通过 docker logout 退出登录。

通过 docker search 命令可以查找官方仓库中的镜像，并利用 docker pull 命令来将它下载到本地。

用户也可以在登录后通过 docker push 命令来将自己的镜像推送到 Docker Hub。

## Docker 数据管理

在容器中管理数据主要有两种方式：

-   数据卷（Volumes）
-   挂载主机目录（Bind mounts）

### 数据卷

**数据卷**是一个可供一个或多个容器使用的特殊目录，它绕过 UFS，可以提供很多有用的特性：

-   数据卷可以在容器之间共享和重用；
-   对数据卷的修改会立马生效；
-   对数据卷的更新不会影响镜像；
-   数据卷会一直存在，即使容器被删除。

_注意：数据卷的使用，类似于 Linux 下对目录或文件进行 mount，镜像中的被指定为挂载点的目录中的文件会复制到数据卷中（仅数据卷为空时会复制）。_

创建一个数据卷：

```sh
# 创建一个数据卷
$ docker volume create my-vol
# 查看所有的数据卷
$ docker volume ls
# 查看指定的数据卷
$ docker volume inspect my-vol
# 等价于 docker inspect my-vol
```

在用 docker run 命令的时候，使用 --mount 标记来将*数据卷*挂载到容器里。在一次 docker run 中可以挂载多个数据卷。

```sh
# 创建一个名为 web 的容器，并加载一个数据卷到容器的 /usr/share/nginx/html 目录。
$ docker run -d -P \
    --name web \
    # -v my-vol:/usr/share/nginx/html \
    --mount source=my-vol,target=/usr/share/nginx/html \
    nginx:alpine
```

删除数据卷：

```sh
$ docker volume rm my-vol
```

数据卷是被设计用来持久化数据的，它的生命周期独立于容器，Docker 不会在容器被删除后自动删除数据卷，并且也不存在垃圾回收这样的机制来处理没有任何容器引用的数据卷。如果需要在删除容器的同时移除数据卷。可以在删除容器的时候使用 `docker rm -v` 这个命令。

无主的数据卷可能会占据很多空间，要清理请使用以下命令：

```sh
$ docker volume prune
```

### 挂载主机目录

使用 --mount 标记可以指定挂载一个本地主机的目录到容器中去。

```sh
# 加载主机的 /src/webapp 目录到容器的 /usr/share/nginx/html目录
$ docker run -d -P \
    --name web \
    # -v /src/webapp:/usr/share/nginx/html \
    --mount type=bind,source=/src/webapp,target=/usr/share/nginx/html \
    nginx:alpine
```

_注意：使用 --mount 参数挂载目录时如果目标目录不存在，Docker 会报错。_

在主机里使用以下命令可以查看 web 容器的信息：

```sh
$ docker inspect web
# Mounts 下包含了挂载目录的配置信息
"Mounts": [
    {
        "Type": "bind",
        "Source": "/src/webapp",
        "Destination": "/usr/share/nginx/html",
        "Mode": "",
        "RW": true,
        "Propagation": "rprivate"
    }
],
```

--mount 标记也可以从主机挂载单个文件到容器中：

```sh
# 用于记录在容器输入过的命令
$ docker run --rm -it \
   # -v $HOME/.bash_history:/root/.bash_history \
   --mount type=bind,source=$HOME/.bash_history,target=/root/.bash_history \
   ubuntu:18.04 \
   bash
```

## 使用网络

Docker 允许通过外部访问容器或容器互联的方式来提供网络服务。

### 外部访问容器

容器中可以运行一些网络应用，要让外部也可以访问这些应用，可以通过 -P 或 -p 参数来指定端口映射。

当使用 -P 标记时，Docker 会随机映射一个端口到内部容器开放的网络端口。

-p 则可以指定要映射的端口，并且，在一个指定端口上只可以绑定一个容器。支持的格式有 ip:hostPort:containerPort | ip::containerPort | hostPort:containerPort。

-   映射所有接口地址

```sh
$ docker run -d -p 80:80 nginx:alpine
```

-   映射到指定地址的指定端口

```sh
$ docker run -d -p 127.0.0.1:80:80 nginx:alpine
# 指定 udp 端口
# $ docker run -d -p 127.0.0.1:80:80/udp nginx:alpine
```

-   映射到指定地址的任意端口

```sh
$ docker run -d -p 127.0.0.1::80 nginx:alpine
```

使用 docker port 来查看当前映射的端口配置，也可以查看到绑定的地址。

```sh
$ docker port fa 80
0.0.0.0:32768
```

_注意：-p 标记可以多次使用来绑定多个端口。_

### 容器互联

可以将容器加入自定义的 Docker 网络来连接多个容器。

```sh
# 创建一个新的 Docker 网络
$ docker network create -d bridge my-net
# -d 参数指定网络类型
```

运行一个容器并连接到新建的 my-net 网络。

```sh
$ docker run -it --rm --name busybox1 --network my-net busybox sh
# --network 参数用于指定连接到的容器网络
```

加入同一个容器网络的容器建立了互联关系，可以通过容器名互相访问。

```sh
$ docker container ls

CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
b47060aca56b        busybox             "sh"                11 minutes ago      Up 11 minutes                           busybox2
8720575823ec        busybox             "sh"                16 minutes ago      Up 16 minutes                           busybox1

# 进入 busybox1 容器
/ # ping busybox2
PING busybox2 (172.19.0.3): 56 data bytes
64 bytes from 172.19.0.3: seq=0 ttl=64 time=0.072 ms
64 bytes from 172.19.0.3: seq=1 ttl=64 time=0.118 ms
```

_提示：如果有多个容器之间需要互相连接，推荐使用 Docker Compose。_

## Docker Compose

Docker Compose 是 Docker 官方编排（Orchestration）项目之一，负责快速的部署分布式应用。

使用一个 Dockerfile 模板文件，可以让用户很方便的定义一个单独的应用容器。然而，在日常工作中，经常会碰到需要多个容器相互配合来完成某项任务的情况。例如要实现一个 Web 项目，除了 Web 服务容器本身，往往还需要再加上后端的数据库服务容器，甚至还包括负载均衡容器等。

Compose 恰好满足了这样的需求。它允许用户通过一个单独的 docker-compose.yml 模板文件（YAML 格式）来定义一组相关联的应用容器为一个项目（project）。

### 使用 Docker Compose

Compose 中有两个重要的概念：

-   服务 (service)：一个应用的容器，实际上可以包括若干运行**相同镜像**的容器实例。
-   项目 (project)：由一组关联的应用容器组成的一个完整业务单元，在 docker-compose.yml 文件中定义。

_提示：Compose 项目由 Python 编写，实现上调用了 Docker 服务提供的 API 来对容器进行管理。因此，只要所操作的平台支持 Docker API，就可以在其上利用 Compose 来进行编排管理。_

模板文件是使用 Compose 的核心，涉及到的指令关键字也比较多。默认的模板文件名称为 docker-compose.yml，格式为 YAML 格式。

```yaml
version: "3"

services:
    webapp:
        image: examples/web
        ports:
            - "80:80"
        volumes:
            - "/data"
```

很多指令与 docker run 对应参数功能一致：

```yaml
# 指定服务容器启动后执行的入口文件。
entrypoint: /code/entrypoint.sh
# 指定容器中运行应用的用户名。
user: nginx
# 指定容器中工作目录。
working_dir: /code
# 指定容器中搜索域名、主机名、mac 地址等。
domainname: your_website.com
hostname: test
mac_address: 08-00-27-00-0C-0A
```

下面分别介绍各个指令的用法。

**build**

定 Dockerfile 所在文件夹的路径（可以是绝对路径，或者相对 docker-compose.yml 文件的路径）。 Compose 将会利用它自动构建这个镜像，然后使用这个镜像。

```yaml
version: "3"
services:
    webapp:
        build: ./dir
```

也可以使用 context 指令指定 Dockerfile 所在文件夹的路径；使用 dockerfile 指令指定 Dockerfile 文件名；使用 arg 指令指定构建镜像时的变量。

```yaml
version: "3"
services:
    webapp:
        build:
            context: ./dir
            dockerfile: Dockerfile-alternate
            args:
                buildno: 1
```

使用 cache_from 指定构建镜像的缓存。

```yaml
build:
    context: .
    cache_from:
        - alpine:latest
        - corp/web_app:3.14
```

**command**

覆盖容器启动后默认执行的命令。

```yaml
command: echo "hello world"
```

**container_name**

指定容器名称。默认将会使用 `项目名称_服务名称_序号` 这样的格式。

```yaml
container_name: docker-web-container
```

_注意: 指定容器名称后，该服务将无法进行扩展（scale），因为 Docker 不允许多个容器具有相同的名称。_

**image**

指定为镜像名称或镜像 ID。如果镜像在本地不存在，Compose 将会尝试拉取这个镜像。

```yaml
image: ubuntu
image: orchardup/postgresql
image: a4bc65fd
```

**devices**

指定设备映射关系。

```yaml
devices:
    - "/dev/ttyUSB1:/dev/ttyUSB0"
```

**depends_on**

解决容器的依赖、启动先后的问题。

```yaml
version: "3"

services:
    web:
        build: .
        depends_on:
            - db
            - redis

    redis:
        image: redis

    db:
        image: postgres
```

_注意：web 服务不会等待 redisdb 「完全启动」之后才启动。_

**dns**

自定义 DNS 服务器。可以是一个值，也可以是一个列表。

```yaml
dns: 8.8.8.8

dns:
  - 8.8.8.8
  - 114.114.114.114
```

**volumes**

数据卷所挂载路径设置。该指令中路径支持相对路径。

```yaml
volumes:
    - /var/lib/mysql
    - cache/:/tmp/cache
    - ~/configs:/etc/configs/:ro
```

如果路径为数据卷名称，必须在文件中配置数据卷。

```yaml
version: "3"

services:
    my_src:
        image: mysql:8.0
        volumes:
            - mysql_data:/var/lib/mysql

volumes:
    mysql_data:
```

**env_file**

文件中获取环境变量，可以为单独的文件路径或列表。

```yaml
env_file: .env

env_file:
  - ./common.env
  - ./apps/web.env
  - /opt/secrets.env
```

_注意：如果有变量名称与 environment 指令冲突，则按照惯例，以后者为准。_

**environment**

设置环境变量。你可以使用数组或字典两种格式。

```yaml
environment:
  RACK_ENV: development
  SESSION_SECRET:

environment:
  - RACK_ENV=development
  - SESSION_SECRET
```

**expose**

暴露端口，但不映射到宿主机，只被连接的服务访问。

```yaml
# 仅可以指定内部端口为参数
expose:
    - "3000"
    - "8000"
```

**logging**

配置日志选项。

```yaml
logging:
    driver: syslog
    options:
        syslog-address: "tcp://192.168.0.42:123"
```

**networks**

配置容器连接的网络。

```yaml
version: "3"
services:
    some-service:
        networks:
            - some-network
            - other-network

networks:
    some-network:
    other-network:
```

**ports**

暴露端口信息。使用宿主端口：容器端口 (HOST:CONTAINER) 格式，或者仅仅指定容器的端口（宿主将会随机选择端口）都可以。

```yaml
ports:
    - "3000"
    - "8000:8000"
    - "49100:22"
    - "127.0.0.1:8001:8001"
```
