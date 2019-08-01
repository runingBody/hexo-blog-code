---
title: Dockerfile入门到实例的详细讲解
tags:
  - 前端
  - 架构
originContent: >
  一、Docker简介

      1.1 什么是容器

  -     一种虚拟化的方法

  -     操作系统级别的虚拟化

  -     只能运行相同的或相似的内核的操作系统

  -     依赖于Linuxb内核特性：Namespace和Cgroups(控制组)

      1.2 什么是Docker
    
      docker是一个把开发的应用程序自动部署到容器的开源引擎。docker在虚拟化的容器处理环境中增加了一个应用程序部署引擎，该引擎的目标是提供一个轻量、快速的环境，能够运行开发者的程序，并方便高效的将程序从开发者环境部署到测试环境然后在部署到生产环境。

      Docker的目标：（高内聚、低耦合、单一任务）

  -     提供简单轻量的建模方式

  -     职责的逻辑分离，开发、运维

  -     快速高效的开发生命周期，编码、编译、测试、打包、部署、监控

  -     鼓励使用面向服务的架构

      1.3 Docker的使用场景
     
  -     使用Docker容器开发、测试、部署服务

  -     创建隔离的运行环境

  -     搭建测试环境

  -     构建多用户的平台即服务基础设施（Pass）

  -     提供软件即服务的应用程序（sass）

  -     高性能、超大规模的宿主机部署

      1.4 Docker跟容器的关系
   
       由于容器技术的复杂性，它依赖于Linux内核的特性，而且不易安装和不易管理和实现自动化，docker的出现正是为了解决这些问题。
      
  二、Docker的基本组成


  -     Docker Client 客户端

  -     Docker Daemon 守护进程

  -     Docker Image 镜像

  -     Docker Container 容器

  -     Docker Register 仓库

      PS：在线练习docker常用命令，包含search、pull、push、run、inspect、history...等

      [Docker在线演示地址：](https://www.docker.com/tryit/)    

  三、Docker容器相关技术简介

      Docker依赖的Linux内核特性
  -     Namespaces 命名空间

  -     Control groups（Cgroups） 控制组

      系统资源环境的隔离或者资源限制，网络、进程、文件

      Docker容器的能力：
    
  -     文件系统的隔离：每个容器都有自己的Root文件系统

  -     进程隔离： 每个容器都运行在自己的进程环境中

  -     网络隔离： 容器间的虚拟网络接口和Ip地址都是分开的

  -     资源隔离和分组： 使用cgroups将CPU和内存之类的资源独立分配给每个Docker容器



  四、DockerFile文件的编写、理解

      4.1 常用命令
    
      1. 启动容器：

          ```language
  docker run image [command] [arg]

  image: 指定容器启动时操作的镜像

  command: 容器启动时执行的命令

  arg: 命令参数

  ```
      2. 启动交互式容器

      ```language
  docker run -i -t image /bin/bash

  -i: --interactive=true | false 默认 false (为容器始终打开标准输入)

  -t: --tty=true | false 默认false（为创建的容器提供终端，可进入交互模式）

  ```
      3. 查看容器

      ```language
  docker ps

  -a: 查看所有容器，包含未运行的容器

  -l: 列表模式

  ```

      4. 查看容器参数、详情

      ```language
  docker inspect imageId| imageName

  ```
      5. 容器重命名(可在运行阶段直接重命名)

      ```language
  docker run --name= container01 -t -i ubantu /bin/bash

  ```

      6. 重新启动停止的容器：

      ```language
  docker start -i container01


  ```

      7. 删除容器（停止状态）：

      ```language
  docker rm container


  ```

      8. 删除镜像：

      ```language
  docker rmi image


  ```

      9. 查看容器日志(查看容器内部运行情况)

      ```language
  docker logs -f -t --tail container

  -f: --follows=true | false 默认为false 一直跟踪返回日志

  -t: --timestamps=true | false 默认为false 时间戳

  --tail= "all" 返回日志范围

  ```



      4.2 守护式容器

      启动守护式容器：docker run --name dc1 ubuntu -d /bin/bash -C "

      4.3 Dockerfile配置文件的编写

      4.3.1 基本结构
    
      Dockerfile 由一行行命令语句组成，并且支持以 # 开头的注释行。

  一般的，Dockerfile 分为四部分：基础镜像信息、维护者信息、镜像操作指令和容器启动时执行指令。


  例如：

      ```language
  # This dockerfile uses the ubuntu image

  # VERSION 2 - EDITION 1

  # Author: docker_user

  # Command format: Instruction [arguments / command] ..


  # Base image to use, this must be set as the first line

  FROM ubuntu


  # Maintainer: docker_user <docker_user at email.com> (@docker_user)

  MAINTAINER docker_user docker_user@email.com


  # Commands to update the image

  RUN echo "deb http://archive.ubuntu.com/ubuntu/ raring main universe" >>
  /etc/apt/sources.list

  RUN apt-get update && apt-get install -y nginx

  RUN echo "\ndaemon off;" >> /etc/nginx/nginx.conf


  # Commands when creating a new container

  CMD /usr/sbin/nginx

  ```


  其中，一开始必须指明所基于的镜像名称，接下来推荐说明维护者信息。


  后面则是镜像操作指令，例如 RUN 指令，RUN 指令将对镜像执行跟随的命令。每运行一条 RUN 指令，镜像添加新的一层，并提交。


  最后是 CMD 指令，来指定运行容器时的操作命令。


  下面是一个更复杂的例子:

      ```language
  # Nginx

  #

  # VERSION               0.0.1


  FROM      ubuntu

  MAINTAINER Victor Vieux <victor@docker.com>


  RUN apt-get update && apt-get install -y inotify-tools nginx apache2
  openssh-server


  # Firefox over VNC

  #

  # VERSION               0.3


  FROM ubuntu


  # Install vnc, xvfb in order to create a 'fake' display and firefox

  RUN apt-get update && apt-get install -y x11vnc xvfb firefox

  RUN mkdir /.vnc

  # Setup a password

  RUN x11vnc -storepasswd 1234 ~/.vnc/passwd

  # Autostart firefox (might not be the best way, but it does the trick)

  RUN bash -c 'echo "firefox" >> /.bashrc'


  EXPOSE 5900

  CMD    ["x11vnc", "-forever", "-usepw", "-create"]


  # Multiple images example

  #

  # VERSION               0.1


  FROM ubuntu

  RUN echo foo > bar

  # Will output something like ===> 907ad6c2736f


  FROM ubuntu

  RUN echo moo > oink

  # Will output something like ===> 695d7793cbe4


  # You᾿ll now have two images, 907ad6c2736f with /bar, and 695d7793cbe4 with

  # /oink.

  ```

      4.3.2 指令
   
      指令的一般格式为 INSTRUCTION arguments，指令包括 FROM、MAINTAINER、RUN 等。

      FROM
   
      格式为 FROM <image>或FROM <image>:<tag>。

  第一条指令必须为 FROM 指令。并且，如果在同一个Dockerfile中创建多个镜像时，可以使用多个 FROM 指令（每个镜像一次）。

      MAINTANER

      格式为 MAINTAINER <name>，指定维护者信息。

      RUN

      格式为 RUN <command> 或 RUN ["executable", "param1", "param2"]。

  前者将在 shell 终端中运行命令，即 /bin/sh -c；后者则使用 exec 执行。指定使用其它终端可以通过第二种方式实现，例如 RUN
  ["/bin/bash", "-c", "echo hello"]。


  每条 RUN 指令将在当前镜像基础上执行指定命令，并提交为新的镜像。当命令较长时可以使用 \ 来换行。

      CMD

      支持三种格式:

  -     CMD ["executable","param1","param2"] 使用 exec 执行，推荐方式；

  -     CMD command param1 param2 在 /bin/sh 中执行，提供给需要交互的应用；

  -     CMD ["param1","param2"] 提供给 ENTRYPOINT 的默认参数；

      指定启动容器时执行的命令，每个 Dockerfile 只能有一条 CMD 命令。如果指定了多条命令，只有最后一条会被执行。

  如果用户启动容器时候指定了运行的命令，则会覆盖掉 CMD 指定的命令。

      EXPOSE 

      格式为 EXPOSE <port> [<port>...]。

  告诉 Docker 服务端容器暴露的端口号，供互联系统使用。在启动容器时需要通过 -P，Docker 主机会自动分配一个端口转发到指定的端口。

      ENV

      格式为 ENV <key> <value>。 指定一个环境变量，会被后续 RUN 指令使用，并在容器运行时保持。

      例如:

      ```language
  ENV PG_MAJOR 9.3

  ENV PG_VERSION 9.3.4

  RUN curl -SL http://example.com/postgres-$PG_VERSION.tar.xz | tar -xJC
  /usr/src/postgress && …

  ENV PATH /usr/local/postgres-$PG_MAJOR/bin:$PATH

  ```

      ADD 

      格式为 ADD <src> <dest>。

  该命令将复制指定的 <src> 到容器中的 <dest>。 其中 <src> 可以是Dockerfile所在目录的一个相对路径；也可以是一个
  URL；还可以是一个 tar 文件（自动解压为目录）。

      COPY 

      格式为 COPY <src> <dest>。

  复制本地主机的 <src>（为 Dockerfile 所在目录的相对路径）到容器中的 <dest>。


  当使用本地目录为源目录时，推荐使用 COPY

      ENTRYPOINT

      两种格式：

  -     ENTRYPOINT ["executable", "param1", "param2"]

  -     ENTRYPOINT command param1 param2（shell中执行）。

      配置容器启动后执行的命令，并且不可被 docker run 提供的参数覆盖。

  每个 Dockerfile 中只能有一个 ENTRYPOINT，当指定多个时，只有最后一个起效

      VOLUME 

      格式为 VOLUME ["/data"]。

  创建一个可以从本地主机或其他容器挂载的挂载点，一般用来存放数据库和需要保持的数据等。

      USER

      格式为 USER daemon。

      指定运行容器时的用户名或 UID，后续的 RUN 也会使用指定用户。

      当服务不需要管理员权限时，可以通过该命令指定运行用户。并且可以在之前创建所需要的用户，例如：RUN groupadd -r postgres && useradd -r -g postgres postgres。要临时获取管理员权限可以使用 gosu，而不推荐 sudo。

       WORKDIR
   
      格式为 WORKDIR /path/to/workdir。

      为后续的 RUN、CMD、ENTRYPOINT 指令配置工作目录。

      可以使用多个 WORKDIR 指令，后续命令如果参数是相对路径，则会基于之前命令指定的路径。例如

  ```language

  WORKDIR /a

  WORKDIR b

  WORKDIR c

  RUN pwd

  则最终路径为 /a/b/c。


  ```

      ONBUILD

      格式为 ONBUILD [INSTRUCTION]。

      配置当所创建的镜像作为其它新创建镜像的基础镜像时，所执行的操作指令。

      例如，Dockerfile 使用如下的内容创建了镜像 image-A。

  ```language

  [...]

  ONBUILD ADD . /app/src

  ONBUILD RUN /usr/local/bin/python-build --dir /app/src

  [...]


  ```

      如果基于 image-A 创建新的镜像时，新的Dockerfile中使用 FROM image-A指定基础镜像时，会自动执行 ONBUILD 指令内容，等价于在后面添加了两条指令。

    ```language
      FROM image-A

      #Automatically run the following
      ADD . /app/src
      RUN /usr/local/bin/python-build --dir /app/src

  ```
      使用 ONBUILD 指令的镜像，推荐在标签中注明，例如 ruby:1.9-onbuild。

      创建镜像

      编写完成 Dockerfile 之后，可以通过 docker build 命令来创建镜像。

      基本的格式为 docker build [选项] 路径，该命令将读取指定路径下（包括子目录）的 Dockerfile，并将该路径下所有内容发送给 Docker 服务端，由服务端来创建镜像。因此一般建议放置 Dockerfile 的目录为空目录。也可以通过 .dockerignore 文件（每一行添加一条匹配模式）来让 Docker 忽略路径下的目录和文件。

      要指定镜像的标签信息，可以通过 -t 选项，例如

      ```language
  $ sudo docker build -t myrepo/myapp /tmp/test1/


  ```



      


   
categories:
  - 前端
toc: true
date: 2019-07-27 18:51:23
---

一、Docker简介

    1.1 什么是容器

-     一种虚拟化的方法
-     操作系统级别的虚拟化
-     只能运行相同的或相似的内核的操作系统
-     依赖于Linuxb内核特性：Namespace和Cgroups(控制组)

    1.2 什么是Docker
  
    docker是一个把开发的应用程序自动部署到容器的开源引擎。docker在虚拟化的容器处理环境中增加了一个应用程序部署引擎，该引擎的目标是提供一个轻量、快速的环境，能够运行开发者的程序，并方便高效的将程序从开发者环境部署到测试环境然后在部署到生产环境。

    Docker的目标：（高内聚、低耦合、单一任务）

-     提供简单轻量的建模方式
-     职责的逻辑分离，开发、运维
-     快速高效的开发生命周期，编码、编译、测试、打包、部署、监控
-     鼓励使用面向服务的架构

    1.3 Docker的使用场景
   
-     使用Docker容器开发、测试、部署服务
-     创建隔离的运行环境
-     搭建测试环境
-     构建多用户的平台即服务基础设施（Pass）
-     提供软件即服务的应用程序（sass）
-     高性能、超大规模的宿主机部署

    1.4 Docker跟容器的关系
 
     由于容器技术的复杂性，它依赖于Linux内核的特性，而且不易安装和不易管理和实现自动化，docker的出现正是为了解决这些问题。
    
二、Docker的基本组成

-     Docker Client 客户端
-     Docker Daemon 守护进程
-     Docker Image 镜像
-     Docker Container 容器
-     Docker Register 仓库

    PS：在线练习docker常用命令，包含search、pull、push、run、inspect、history...等

    [Docker在线演示地址：](https://www.docker.com/tryit/)    

三、Docker容器相关技术简介

    Docker依赖的Linux内核特性
-     Namespaces 命名空间
-     Control groups（Cgroups） 控制组

    系统资源环境的隔离或者资源限制，网络、进程、文件

    Docker容器的能力：
  
-     文件系统的隔离：每个容器都有自己的Root文件系统
-     进程隔离： 每个容器都运行在自己的进程环境中
-     网络隔离： 容器间的虚拟网络接口和Ip地址都是分开的
-     资源隔离和分组： 使用cgroups将CPU和内存之类的资源独立分配给每个Docker容器


四、DockerFile文件的编写、理解

    4.1 常用命令
  
    1. 启动容器：

        ```language
docker run image [command] [arg]
image: 指定容器启动时操作的镜像
command: 容器启动时执行的命令
arg: 命令参数
```
    2. 启动交互式容器

    ```language
docker run -i -t image /bin/bash
-i: --interactive=true | false 默认 false (为容器始终打开标准输入)
-t: --tty=true | false 默认false（为创建的容器提供终端，可进入交互模式）
```
    3. 查看容器

    ```language
docker ps
-a: 查看所有容器，包含未运行的容器
-l: 列表模式
```

    4. 查看容器参数、详情

    ```language
docker inspect imageId| imageName
```
    5. 容器重命名(可在运行阶段直接重命名)

    ```language
docker run --name= container01 -t -i ubantu /bin/bash
```

    6. 重新启动停止的容器：

    ```language
docker start -i container01

```

    7. 删除容器（停止状态）：

    ```language
docker rm container

```

    8. 删除镜像：

    ```language
docker rmi image

```

    9. 查看容器日志(查看容器内部运行情况)

    ```language
docker logs -f -t --tail container
-f: --follows=true | false 默认为false 一直跟踪返回日志
-t: --timestamps=true | false 默认为false 时间戳
--tail= "all" 返回日志范围
```

```language

```

    4.2 守护式容器

    启动守护式容器：docker run --name dc1 ubuntu -d /bin/bash -C "

    4.3 Dockerfile配置文件的编写

    4.3.1 基本结构
  
    Dockerfile 由一行行命令语句组成，并且支持以 # 开头的注释行。

    一般的，Dockerfile 分为四部分：基础镜像信息、维护者信息、镜像操作指令和容器启动时执行指令。

    例如：

    ```language
# This dockerfile uses the ubuntu image
# VERSION 2 - EDITION 1
# Author: docker_user
# Command format: Instruction [arguments / command] ..

# Base image to use, this must be set as the first line
FROM ubuntu

# Maintainer: docker_user <docker_user at email.com> (@docker_user)
MAINTAINER docker_user docker_user@email.com

# Commands to update the image
RUN echo "deb http://archive.ubuntu.com/ubuntu/ raring main universe" >> /etc/apt/sources.list
RUN apt-get update && apt-get install -y nginx
RUN echo "\ndaemon off;" >> /etc/nginx/nginx.conf

# Commands when creating a new container
CMD /usr/sbin/nginx
```

其中，一开始必须指明所基于的镜像名称，接下来推荐说明维护者信息。

后面则是镜像操作指令，例如 RUN 指令，RUN 指令将对镜像执行跟随的命令。每运行一条 RUN 指令，镜像添加新的一层，并提交。

最后是 CMD 指令，来指定运行容器时的操作命令。

下面是一个更复杂的例子:

    ```language
# Nginx
#
# VERSION               0.0.1

FROM      ubuntu
MAINTAINER Victor Vieux <victor@docker.com>

RUN apt-get update && apt-get install -y inotify-tools nginx apache2 openssh-server

# Firefox over VNC
#
# VERSION               0.3

FROM ubuntu

# Install vnc, xvfb in order to create a 'fake' display and firefox
RUN apt-get update && apt-get install -y x11vnc xvfb firefox
RUN mkdir /.vnc
# Setup a password
RUN x11vnc -storepasswd 1234 ~/.vnc/passwd
# Autostart firefox (might not be the best way, but it does the trick)
RUN bash -c 'echo "firefox" >> /.bashrc'

EXPOSE 5900
CMD    ["x11vnc", "-forever", "-usepw", "-create"]

# Multiple images example
#
# VERSION               0.1

FROM ubuntu
RUN echo foo > bar
# Will output something like ===> 907ad6c2736f

FROM ubuntu
RUN echo moo > oink
# Will output something like ===> 695d7793cbe4

# You᾿ll now have two images, 907ad6c2736f with /bar, and 695d7793cbe4 with
# /oink.
```

    4.3.2 指令
 
    指令的一般格式为 INSTRUCTION arguments，指令包括 FROM、MAINTAINER、RUN 等。

    FROM
 
    格式为 FROM <image>或FROM <image>:<tag>。

第一条指令必须为 FROM 指令。并且，如果在同一个Dockerfile中创建多个镜像时，可以使用多个 FROM 指令（每个镜像一次）。

    MAINTANER

    格式为 MAINTAINER <name>，指定维护者信息。

    RUN

    格式为 RUN <command> 或 RUN ["executable", "param1", "param2"]。

前者将在 shell 终端中运行命令，即 /bin/sh -c；后者则使用 exec 执行。指定使用其它终端可以通过第二种方式实现，例如 RUN ["/bin/bash", "-c", "echo hello"]。

每条 RUN 指令将在当前镜像基础上执行指定命令，并提交为新的镜像。当命令较长时可以使用 \ 来换行。

    CMD

    支持三种格式:

-     CMD ["executable","param1","param2"] 使用 exec 执行，推荐方式；
-     CMD command param1 param2 在 /bin/sh 中执行，提供给需要交互的应用；
-     CMD ["param1","param2"] 提供给 ENTRYPOINT 的默认参数；

    指定启动容器时执行的命令，每个 Dockerfile 只能有一条 CMD 命令。如果指定了多条命令，只有最后一条会被执行。

如果用户启动容器时候指定了运行的命令，则会覆盖掉 CMD 指定的命令。

    EXPOSE 

    格式为 EXPOSE <port> [<port>...]。

告诉 Docker 服务端容器暴露的端口号，供互联系统使用。在启动容器时需要通过 -P，Docker 主机会自动分配一个端口转发到指定的端口。

    ENV

    格式为 ENV <key> <value>。 指定一个环境变量，会被后续 RUN 指令使用，并在容器运行时保持。

    例如:

    ```language
ENV PG_MAJOR 9.3
ENV PG_VERSION 9.3.4
RUN curl -SL http://example.com/postgres-$PG_VERSION.tar.xz | tar -xJC /usr/src/postgress && …
ENV PATH /usr/local/postgres-$PG_MAJOR/bin:$PATH
```

    ADD 

    格式为 ADD <src> <dest>。

该命令将复制指定的 <src> 到容器中的 <dest>。 其中 <src> 可以是Dockerfile所在目录的一个相对路径；也可以是一个 URL；还可以是一个 tar 文件（自动解压为目录）。

    COPY 

    格式为 COPY <src> <dest>。

复制本地主机的 <src>（为 Dockerfile 所在目录的相对路径）到容器中的 <dest>。

当使用本地目录为源目录时，推荐使用 COPY

    ENTRYPOINT

    两种格式：

-     ENTRYPOINT ["executable", "param1", "param2"]
-     ENTRYPOINT command param1 param2（shell中执行）。

    配置容器启动后执行的命令，并且不可被 docker run 提供的参数覆盖。

每个 Dockerfile 中只能有一个 ENTRYPOINT，当指定多个时，只有最后一个起效

    VOLUME 

    格式为 VOLUME ["/data"]。

创建一个可以从本地主机或其他容器挂载的挂载点，一般用来存放数据库和需要保持的数据等。

    USER

    格式为 USER daemon。

    指定运行容器时的用户名或 UID，后续的 RUN 也会使用指定用户。

    当服务不需要管理员权限时，可以通过该命令指定运行用户。并且可以在之前创建所需要的用户，例如：RUN groupadd -r postgres && useradd -r -g postgres postgres。要临时获取管理员权限可以使用 gosu，而不推荐 sudo。

     WORKDIR
 
    格式为 WORKDIR /path/to/workdir。

    为后续的 RUN、CMD、ENTRYPOINT 指令配置工作目录。

    可以使用多个 WORKDIR 指令，后续命令如果参数是相对路径，则会基于之前命令指定的路径。例如

```language
WORKDIR /a
WORKDIR b
WORKDIR c
RUN pwd
则最终路径为 /a/b/c。

```

    ONBUILD

    格式为 ONBUILD [INSTRUCTION]。

    配置当所创建的镜像作为其它新创建镜像的基础镜像时，所执行的操作指令。

    例如，Dockerfile 使用如下的内容创建了镜像 image-A。

```language
[...]
ONBUILD ADD . /app/src
ONBUILD RUN /usr/local/bin/python-build --dir /app/src
[...]

```

    如果基于 image-A 创建新的镜像时，新的Dockerfile中使用 FROM image-A指定基础镜像时，会自动执行 ONBUILD 指令内容，等价于在后面添加了两条指令。

  ```language
    FROM image-A

    #Automatically run the following
    ADD . /app/src
    RUN /usr/local/bin/python-build --dir /app/src

```
    使用 ONBUILD 指令的镜像，推荐在标签中注明，例如 ruby:1.9-onbuild。

    创建镜像

    编写完成 Dockerfile 之后，可以通过 docker build 命令来创建镜像。

    基本的格式为 docker build [选项] 路径，该命令将读取指定路径下（包括子目录）的 Dockerfile，并将该路径下所有内容发送给 Docker 服务端，由服务端来创建镜像。因此一般建议放置 Dockerfile 的目录为空目录。也可以通过 .dockerignore 文件（每一行添加一条匹配模式）来让 Docker 忽略路径下的目录和文件。

    要指定镜像的标签信息，可以通过 -t 选项，例如

    ```language
$ sudo docker build -t myrepo/myapp /tmp/test1/

```



    


 
