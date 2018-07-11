# docker的用途越来越广泛，现在介绍一下docker的基础用法

## 1.prepare

连接VPN下载docker

<https://docs.docker.com/install/linux/docker-ce/ubuntu/>

查看是否安装成功(查看docker版本)：
`docker version`

`出现类似Version:      18.05.0-ce就是安装成功了`

接下来启动docker服务：

`sudo service docker start`

## image文件

docker把应用程序及依赖打包在image文件中，
通过这个文件才能生成docker容器，image文件可以看成是容器的模板，
docker根据image文件生成容器的实例，同一个image文件可以生成多个可以同时运行的容器实例

列出本机所有的image文件

`docker image ls`

删除image文件

`docker image rm imagename 或者 docker rmi imageID`

为了能更快的下载image文件可以修改image仓库的镜像网址

打开/etc/default/docker文件，在文件的底部加上一行

`DOCKER_OPTS="--registry-mirror=https://registry.docker-cn.com"`

然后重启docker服务

`service docker rrestart`

## demo----hello world

首先，将image文件从仓库中抓取到本地
`docker image pull libray/hello-world`

docker image pull 是抓取image文件的命令，libray/hello-world 是image在仓库里面的位置，
其中libray是image文件所在的组，hello-world是image文件的名字

由于docker官方提供的image文件都放在library组里面，所以它是默认组，可以省略，也可以写成这样

`docker image pull hello-world`

运行image文件

`docker container run helloworld`

docker container run命令会根据image文件生成一个正在运行的容器实例

#### 注意：docker container run命令具有自动抓取 image 文件的功能。如果发现本地没有指定的 image 文件，就会从仓库自动抓取。因此，前面的docker image pull命令并不是必需的步骤

有的容器实例运行完会自动停止，有的容器实例则不会，需要手动停止

`container kill containerID`

关于容器ID的查看可以用如下命令：

`docker container ls 或者 docker ps  或者 docker container ls -all `

终止运行的容器文件依然会占据硬盘空间，可以用如下命令来删除容器实例：

`dokcer container rm containerID`

## 制作自己的docker image

如何生成image文件，如果要推广自己的软件，势必要自己制作image文件
这就要用到dockerfile文件，它是一个文本文件，用来配置image，docker根据该文件生成二进制的image文件

以bookstore为例介绍

先下载源码：

`git clone https://github.com/QaQwmy/bookstore.git`

进入项目根目录

`cd bookstore`

首先在项目的根目录下新建一个文本文件 .dockerignore,写入如下内容：

```
.git
*.pyc
```
上面代码表示，这两个路径要排除，不要打包进入image文件，如果你没有路径可安排，这个文件可以不新建

然后在项目的根目录下，新建一个文本文件Dockerfile,写入以下内容：
```
FROM node:8.4
COPY . /app
WORKDIR /app
RUN pip install requirements.txt
EXPOSE 3000
```
- FROM node:8.4：该 image 文件继承官方的 node image，冒号表示标签，这里标签是8.4，即8.4版本的 node。
- COPY . /app：将当前目录下的所有文件（除了.dockerignore排除的路径），都拷贝进入 image 文件的/app目录。
- WORKDIR /app：指定接下来的工作路径为/app。
- RUN npm install：在/app目录下，运行pip install命令安装依赖。注意，安装后所有的依赖，都将打包进入 image 文件。
- EXPOSE 3000：将容器 3000 端口暴露出来， 允许外部连接这个端口。

进入docker容器

`docker exec -it containerID /bin/bash`

查看docker容器日志

`docker logs -f -t containerID`

然后在创建image文件

`docker image build -t bookstore 或者 docker image build -t bookstore:0.01`

上面代码中 -t 指定image文件的名字，后面还可以用冒号指定标签，如果不指定，默认标签是latest。最后那个点表示dockerfile文件所在的路径，上例是当前路径，所以是一个点

如果运行成功，就可以看到新生成的image文件bookstore了

生成容器：

`docker container run -p 8000:3000 -it bookstore:0.0.1 /bin/bash`

- -p 参数 ： 容器的3000端口映射到本机的8000端口
- -it参数： 容器shekk映射到当前的shell，然后再你的本机窗口输入的命令，就会传入到容器
- bookstore:0.0.1 image文件的名字（如果有标签还需要添加标签，默认是latest标签） /bin/bash容器启动后，内部第一个执行的命令这里是启动bash保证用户可以使用shell
如果一切正常会返回一个命令提示符： app#(不一定是这个)

现在执行python manager.py runserver就可执行此项目，现在即位于容器内部，按下Ctrl + c停止容器进程，然后按下 Ctrl + d （或者输入 exit）退出容器。
此外，也可以用docker container kill终止容器运行。

## cmd命令

容器启动后需要手动输入python manager.py runserver，我们可以将这个命令写到dockerfile中，这样容器启动后这个命令就直接执行了

Dckerfile文件：

```
FROM node:8.4
COPY . /app
WORKDIR /app
RUN pip install -r requirements.txt
EXPOSE 3000
CMD ["python","manager.py runserver"]
```
### 注意：cmd命令和run命令的区别
run命令在image文件的构建阶段执行，执行结果都会打包进入image文件，
cmd命令则是在容器启动后执行，另外，一个dockerfile文件中可以包含多个run命令，但是只能有一个cmd命令，
在指定了cmd命令后，docker container run命令就不能附加命令了（比如前面的/bin/bash）否则它会覆盖cmd命令

现在启动容器可以使用以下命令

`docker container run -rm -p 8000:3000 -it bookstore:0.0.1`


## 发布image

首先，去[hub.docker.com](hub.docker.com)或者[cloud.docker.com](cloud.docker.com)注册一个账户（可能需要连接VPN），然后用以下命令登录：

`docker login`

之后就是需要输入用户名和密码

重新构建一下image文件，加上组名和tag(版本号)

`docker image build -t mengyuwu/bookstore:1.0.0`

最后就是发布image文件啦：

  `docker image push username/imagename:tag`

发布成功后登录[]()就可以看到你发布的image文件了

  
### 补充一下：拷贝容器到 ...

`docker container cp containerID:/path/to/file`

