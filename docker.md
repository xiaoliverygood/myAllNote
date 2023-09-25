# docker

- ## 容器入门：

- 中间件比较多，很麻烦，环境这些，所以有了docker技术

- 将 mysql 等一些环境打包成镜像，然后安装部署就ok

- ```txt
  您理解得正确。如果您在Docker中安装了MySQL和Redis，那么通常情况下，您将会使用两个不同的Docker镜像。一个镜像用于运行MySQL，另一个镜像用于运行Redis。
  
  每个Docker镜像代表一个独立的容器化应用程序或服务，并包含了该应用程序或服务所需的所有文件和依赖项。在您的情况下，一个镜像包含MySQL数据库服务器，另一个包含Redis缓存服务器。
  
  这种分开的方式使得您可以分别管理和配置MySQL和Redis，而不会互相干扰，也更容易进行升级或维护。每个容器将运行一个实例，您可以根据需要启动、停止、备份和恢复它们，而不会影响其他容器。
  
  所以，您的Docker安装中应该包括两个不同的镜像，分别用于MySQL和Redis，以便支持您的应用程序的数据库和缓存需求。
  
  Docker不是一个大容器里装着多个独立的操作系统，而是一个容器化平台，用于在单个操作系统内部创建多个相互隔离的容器，每个容器都包含一个独立的应用程序或服务及其依赖项。
  
  每个Docker容器共享宿主操作系统的内核，但在用户空间中是相互隔离的。这意味着容器之间不会拥有自己的独立操作系统，它们共享相同的操作系统内核，但每个容器有自己的文件系统、进程空间、网络栈等，使得它们看起来像是独立的操作系统环境。
  
  因此，Docker容器提供了一种轻量级的虚拟化技术，使得多个应用程序可以在同一台物理机上运行，每个应用程序都在自己的独立容器中，这样可以实现资源隔离、便捷的部署和管理，以及高效的资源利用。与传统的虚拟机相比，Docker容器更轻便、启动更快，但它们并不包含独立的操作系统，而是共享宿主操作系统。、
  
  当您在Docker容器中安装MySQL或任何其他应用程序时，数据通常存储在容器内部的文件系统中。这是默认行为，容器内的文件系统是容器的一部分，通常是临时性的，这意味着容器停止或删除后，容器内的数据也将丢失。
  
  然而，为了保护和持久化数据，通常会采用以下两种方法之一：
  
  数据卷（Volumes）: Docker提供了数据卷的机制，允许您将容器内的特定目录映射到宿主主机上的目录。这样，容器内的数据将存储在宿主主机上，即使容器停止或删除，数据仍然保留。您可以在运行容器时使用-v或--volume标志来创建数据卷，或者在Docker Compose配置中定义数据卷。
  
  例如，创建一个MySQL容器并将其数据存储在宿主主机上的/data/mysql目录中：
  
  docker run -d -v /data/mysql:/var/lib/mysql mysql:latest
  网络存储: 另一种方法是将数据存储在网络存储上，例如网络附加存储卷、云存储或网络文件系统（NFS）。这允许多个容器访问相同的数据，即使容器在不同的宿主主机上运行。这对于构建分布式应用程序或在集群中运行容器很有用。
  
  您可以根据您的需求选择合适的方法来管理和持久化容器内的数据。一般来说，对于关键数据，使用数据卷或网络存储是推荐的做法，以确保数据的可靠性和持久性。
  
  
  是的，Docker容器在宿主主机上运行，并与宿主主机共享一些资源，包括内存、网络、CPU和端口。以下是一些关于Docker容器资源管理的重要信息：
  
  1. **内存**: Docker容器共享宿主主机的物理内存。您可以通过配置容器的内存限制来限制容器可以使用的内存量。如果容器使用的内存超过了限制，Docker会进行内存管理，可能会触发内存交换或杀死容器。
  
  2. **CPU**: Docker容器可以访问宿主主机上的CPU资源。您可以使用CPU限制和调度策略来控制容器对CPU的访问。例如，您可以为容器分配特定的CPU核心或设置CPU共享限制。
  
  3. **网络**: Docker容器可以与宿主主机共享网络资源，但它们也可以有自己的独立网络堆栈。默认情况下，Docker容器可以通过宿主主机的网络访问外部网络，但您也可以配置容器之间的网络通信。
  
  4. **端口**: Docker容器可以监听和使用宿主主机上的端口，但需要确保端口不被其他容器或应用程序占用。您可以使用端口映射来将容器内部的端口映射到宿主主机上，以便从外部访问容器内的服务。
  
  需要注意的是，Docker的资源管理和隔离机制允许多个容器在同一宿主主机上运行，每个容器都以独立的方式访问这些资源。这样可以实现资源隔离和多租户的应用程序部署。但同时也需要谨慎管理资源，以确保它们得到充分利用而不会过度占用宿主主机的资源。
  ```

  简单来说就是类似于虚拟机，但是和虚拟机不一样的是少了个系统，每安装个软件可以看作是一个系统里面只安装一个软件（也就是独立的运行环境），但是和虚拟机不一样的是占用的网络端口都是宿主主机的

  

  ### 容器与镜像概念：

  你说得对。在 Docker 中，一个容器通常会运行一个特定的镜像，但一个镜像可以同时用于创建多个容器的实例。这是 Docker 的典型用法之一，它允许你扩展和复制应用程序的运行环境。
  
  以下是一些关于这个概念的更多详细信息：
  
  1. **一个容器一个镜像**：通常情况下，你会为一个容器选择一个适当的镜像来运行你的应用程序。这个容器将会包含该镜像的一个实例，其中包括镜像的文件系统、运行时配置等。
  2. **多个容器一个镜像**：你可以使用相同的镜像创建多个容器实例，每个容器都是独立的、相互隔离的运行环境。这对于在多个环境中运行相同的应用程序副本非常有用，例如负载均衡或横向扩展。
     - 例如，如果你有一个 Web 应用程序，你可以使用相同的镜像创建多个容器，每个容器监听不同的端口。这样，你可以同时运行多个相同的 Web 应用程序实例，以处理更多的请求流量。
  3. **分发和版本控制**：使用相同的镜像来创建多个容器实例还有助于在开发、测试和生产环境之间实现一致性。你可以确保在不同环境中使用相同的镜像版本，从而避免了配置差异和依赖问题。
  4. **容器之间的隔离**：每个容器都是相互隔离的，它们不共享运行时状态。这意味着一个容器的更改不会影响其他容器，这提供了良好的隔离和安全性。
  
  总之，Docker 的灵活性允许你使用相同的镜像来创建多个容器，这有助于实现应用程序的横向扩展、一致性分发和更好的隔离性。这是 Docker 技术的一个强大特性，对于构建可伸缩和可维护的应用程序非常有帮助。
  
  
  
  ### 安装docker
  
- 自己百度

- 最后查看是否安装成功，docker –version，有版本信息则是安装成功的

- 非root用户没有权限使用是因为没有添加到用户组里面去，所以将用户添加到docker用户组里面

- ### 使用：

- 有些镜像（软件）都是在远程仓库有的，类似于maven的依赖那样子

- //下载镜像

- ```dockerfile
  docker pull 镜像名//从镜像仓库拉取镜像名的镜像
  ```

  //查看正在运行的镜像

  ```dockerfile
  docker images //查看正在运行的镜像的容器， **一个容器一个镜像，多个容器一个镜像也是可以的
  docker ps -a（可以省-a，省调是显示正在运行的容器，加上可以看到全部容器）
  ```

-  启动容器中的镜像

- ```
  docker run 镜像名称//运行镜像,一创建就会创建个容器去运行镜像
  
  docker run --name=”新名称（不需要引号）“ 镜像名称//运行镜像，并且为运行镜像的容器起名字（自定义名字）
  
  一些已经停止的容易重新启动
  docker start 名称（容器名称）/容器id
  查看容器名称或者id
  docker ps -a（写上-a是查看全部的包括停止运行的）
  
  
  //删除不处于运行状态的容器，处于运行状态的删除不了的，因为数据是保存在容器中，删除后全部不会有的，类似于删除操作系统后，c盘d盘全部都没有了
  docker rm 容器名称
  //结束容器：
  docker stop 容器名称或者id
  
  docker rmi 镜像名称 //可以删除镜像
  docker image //查看镜像
  docker ps -a//是查看所有的容器
  
  
  ```

  

- docker ps -a//显示正在运行的容器

  ## docker的自身理解：

  ---

  docker其实简单理解就是类似于开虚拟机，创建base 系统镜像就是虚拟机安装了个系统，现在base 镜像跑java项目是需要容器内安装jdk，但是我在外容器外或者第二个容器安装能有端口访问的，都是可以用的，比如nginx的8080端口可以访问容器内部，而网络ip一样，就会导致不同容器或者外界都是可以访问的，但是像jdk那种是环境变量，就不能共享；也可以设置端口不一样，使用映射关系。

  ----

  镜像可以看作开启容器的钥匙，一个容器一个镜像
  
  容器就像主机，复制一份系统就是镜像，镜像用docker run就创建了，在容器中跑上次打包好的系统
  
  一个镜像可以用来创建多个容器实例。多个容器可以基于相同的镜像运行，每个容器都有自己的隔离环境，这使得容器化环境中的应用程序可以轻松扩展和部署。

## 什么是base 镜像，作用是什么

### 镜像结构介绍

前面我们了解了Docker的相关基本操作，实际上容器的基石就是镜像，有了镜像才能创建对应的容器实例，那么我们就先从镜像的基本结构开始说起，我们来看看镜像到底是个什么样的存在。

我们在打包项目时，实际上往往需要一个基本的操作系统环境，这样我们才可以在这个操作系统上安装各种依赖软件，比如数据库、缓存等，像这种基本的系统镜像，我们称为base镜像，我们的项目之后都会基于base镜像进行打包，当然也可以不需要base镜像，仅仅是基于当前操作系统去执行简单的命令，比如我们之前使用的hello-world就是。

一般base镜像就是各个Linux操作系统的发行版，比如我们正在使用的Ubuntu，还有CentOS、Kali等等。这里我们就下载一下CentOS的base镜像：

```sh
docker pull centos
```

![image-20220701132622893](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150045444.png)

可以看到，CentOS的base镜像就已经下载完成，不像我们使用完整系统一样，base镜像的CentOS省去了内核，所以大小只有272M，这里需要解释一下base镜像的机制：

![image-20220701133111829](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150045541.png)

Linux操作体系由内核空间和用户空间组成，其中内核空间就是整个Linux系统的核心，Linux启动后首先会加`bootfs`文件系统，加载完成后会自动卸载掉，之后会加载用户空间的文件系统，这一层是我们自己可以进行操作的部分：

* bootfs包含了BootLoader和Linux内核，用户是不能对这层作任何修改的，在内核启动之后，bootfs会自动卸载。
* rootfs则包含了系统上的常见的目录结构，包括`/dev`、` /proc`、 `/bin`等等以及一些基本的文件和命令，也就是我们进入系统之后能够操作的整个文件系统，包括我们在Ubuntu下使用的apt和CentOS下使用的yum，都是用户空间上的。

base镜像底层会直接使用宿主主机的内核，也就是说你的Ubuntu内核版本是多少，那么base镜像中的CentOS内核版本就是多少，而rootfs则可以在不同的容器中运行多种不同的版本。所以，base镜像实际上只有CentOS的rootfs，因此只有300M大小左右，当然，CentOS里面包含多种基础的软件，还是比较臃肿的，而某些操作系统的base镜像甚至都不到10M。

使用`uname`命令可以查看当前内核版本：

![image-20220701135056123](https://s2.loli.net/2022/07/01/mZjupCUktL7Ab2R.png)

因此，Docker能够同时模拟多种Linux操作系统环境，就不足为奇了，我们可以尝试启动一下刚刚下载的base镜像：

```sh
docker run -it centos
```

注意这里需要添加`-it`参数进行启动，其中`-i`表示在容器上打开一个标准的输入接口，`-t`表示分配一个伪tty设备，可以支持终端登录，一般这两个是一起使用，否则base容器启动后就自动停止了。

![image-20220701135834325](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150045081.png)

可以看到使用ls命令能够查看所有根目录下的文件，不过很多命令都没有，连clear都没有，我们来看看内核版本：

![image-20220701140018095](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150045976.png)

可以看到内核版本是一样的（这也是缺点所在，如果软件对内核版本有要求的话，那么此时使用Docker就直接寄了），我们输入`exit`就可以退出容器终端了，可以看到退出后容器也停止了：

![image-20220701140225415](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150045554.png)

当然我们也可以再次启动，注意启动的时候要加上`-i`才能进入到容器进行交互，否则会在后台运行：

![image-20220701140706977](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150045620.png)

基于base镜像，我们就可以在这基础上安装各种各样的软件的了，几乎所有的镜像都是通过在base镜像的基础上安装和配置需要的软件构建出来的：

![image-20220701143105247](https://s2.loli.net/2022/07/01/SDwEqz2b7lA9nJa.png)

每安装一个软件，就在base镜像上一层层叠加上去，采用的是一种分层的结构，这样多个容器都可以将这些不同的层次自由拼装，比如现在好几个容器都需要使用CentOS的base镜像，而上面运行的软件不同，此时分层结构就很爽了，我们只需要在本地保存一份base镜像，就可以给多个不同的容器拼装使用，是不是感觉很灵活？

我们看到除了这些软件之外，最上层还有一个可写容器层，这个是干嘛的呢，为什么要放在最上面？

我们知道，所有的镜像会叠起来组成一个统一的文件系统，如果不同层中存在相同位置的文件，那么上层的会覆盖掉下层的文件，最终我们看到的是一个叠加之后的文件系统。当我们需要修改容器中的文件时，实际上并不会对镜像进行直接修改，而是在最顶上的容器层（最上面一般称为容器层，下面都是镜像层）进行修改，不会影响到下面的镜像，否则镜像就很难实现多个容器共享了。所以各个操作如下：

* 文件读取：要读取一个文件，Docker会最上层往下依次寻找，找到后则打开文件。
* 文件创建和修改：创建新文件会直接添加到容器层中，修改文件会从上往下依次寻找各个镜像中的文件，如果找到，则将其复制到容器层，再进行修改。
* 删除文件：删除文件也会从上往下依次寻找各个镜像中的文件，一旦找到，并不会直接删除镜像中的文件，而是在容器层标记这个删除操作。

也就是说，我们对整个容器内的文件进行的操作，几乎都是在最上面的容器层进行的，我们是无法干涉到下面所有的镜像层文件的，这样就很好地保护了镜像的完整性，才能实现多个容器共享使用。

---

### 通俗点说就是：

docker其实简单理解就是类似于开虚拟机，然而真正的虚拟机是网络都是不同ip地址的，但是docker是同ip地址的，所以可以通过端口访问镜像的容器内部罢了，所以创建base 系统镜像就是虚拟机安装了个系统，现在base 镜像跑java项目是需要容器内安装jdk，但是我在外容器外或者第二个容器安装能有端口访问的，都是可以用的，比如nginx的8080端口可以访问容器内部，而网络ip一样，就会导致不同容器或者外界都是可以访问的，但是像jdk那种是环境变量，就不能共享

注意，使用base镜像（也就是系统镜像需要打开it（命令窗），不然一打开没多久就会自动关闭容器的，因为没有东西在跑）

---

```
docker run -it centos//这个是打开命令窗口的意思

docker start -i name(是容器的名字)/id //重新进入xxx的容器
```

----

```
基于base镜像构建其他镜像是一种常见的容器化技术，它允许开发人员在一个干净的基础镜像上安装和配置所需的软件和依赖项，以构建特定用途的镜像。这有助于实现镜像的复用和减少不必要的镜像构建步骤，提高了效率。通俗点说就是我本来需要在cneos7系统安装jdk和mysql才能跑的程序，现在我使用docker可以是我安装base镜像，centos7的base镜像，里面又安装了jdk，我宿主主机再加个mysql镜像，又是一个新的容器，到时候哪台电脑服务器需要跑，我将这两个容器给他就行，一个全新的系统机器，只要有了这两个镜像，安装个docker就能跑起来，新机器不需要按jdk和mysql了
```

```xml
您理解得很正确！容器化技术允许您将应用程序及其依赖项打包到容器镜像中，这使得在不同环境中轻松部署应用程序变得更加方便和可重复。使用基于base镜像构建其他镜像的方法，您可以确保应用程序的基础依赖项在不同的服务器上都是一致的，从而提高了可移植性和一致性。

在您的描述中，您可以将不同的应用程序组件打包到不同的容器镜像中，然后在需要部署这些应用程序的服务器上运行这些容器。这使得在新的机器上启动应用程序变得非常容易，只需确保Docker已安装，然后拉取并运行相应的容器镜像即可，而无需在每台服务器上手动安装和配置各种依赖项。

这种容器化方法有助于简化应用程序的交付和维护，提高了开发和运维的效率，并确保了应用程序在不同环境中的一致性和可移植性。这正是Docker和容器化技术的主要优势之一。
```

---

## 构建镜像：

构建自己的环境镜像：

```
作用：比如我要跑一个Java程序，里面需要的是mysql，redis 和jdk，我可以先创建base镜像（精简的系统），然后在精简的系统里面安装好这些东西，然后一起打包成一个镜像，属于自己的镜像文件；换一台新的电脑，我直接运行自己打包的镜像文件，就可以实现一键搬运环境（里面包括mysql，redis等打包前在base镜像安装的东西）
```

### 方式一：使用commit命令

```
docker commit 容器的id或者名字 打包好的镜像文件

然后就多了个镜像出来
```

### 方式二：使用docker 进行文件的构建

首先vim 一个文档，from base系统名称（就是原生容器名称）

然后输入安装环境的多个命令

![image-20230914215937989](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309142159039.png)

然后保存文件

然后使用docker build -t 创建的镜像名 文件目录

![image-20230914215828898](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309142158001.png)—



```
ctrl P+ctrl Q就能退出容器主机，但是容器不会关闭
```

```
docker exec -it 容器名称或id bash//进入容器的终端
```



## 容器网络管理：

Docker在安装后，会在我们的主机上创建三个网络，使用`network ls`命令来查看：

```sh
docker network ls
```

![image-20220702161742741](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150045096.png)

可以看到默认情况下有`bridge`、`host`、`none`这三种网络类型（其实有点像虚拟机的网络配置，也是分桥接、共享网络之类的），我们先来依次介绍一下，在开始之前我们先构建一个镜像，默认的ubuntu镜像由于啥软件都没有，所以我们把一会网络要用到的先提前装好：

```sh
docker run -it ubuntu
```

```sh
apt update
apt install net-tools iputils-ping curl
```

这样就安装好了，我们直接退出然后将其构建为新的镜像：

```sh
docker commit lucid_sammet ubuntu-net
```

![image-20220702170441267](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150045645.png)

OK，一会我们就可以使用了。

* **none网络：**这个网络除了有一个本地环回网络之外，就没有其他的网络了，我们可以在创建容器时指定这个网络。

  这里使用`--network`参数来指定网络：

  ```sh
  docker run -it --network=none ubuntu-net
  ```

  进入之后，我们可以直接查看一下当前的网络：

  ```sh
  ifconfig
  ```

  可以看到只有一个本地环回`lo`网络设备：

  ![image-20220702170000617](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150046659.png)

  所以这个容器是无法连接到互联网的：

  ![image-20220702170531312](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150046629.png)

  “真”单机运行，可以说是绝对的安全，没人能访问进去，存点密码这些还是不错的。

* **bridge网络：**容器默认使用的网络类型，这是桥接网络，也是应用最广泛的网络类型：

  实际上我们在宿主主机上查看网络信息，会发现有一个名为docker0的网络设备：

  ![image-20220702172102410](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150046806.png)

  这个网络设备是Docker安装时自动创建的虚拟设备，它有什么用呢？我们可以来看一下默认创建的容器内部的情况：

  ```sh
  docker run -it ubuntu-net
  ```

  ![image-20220702172532004](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150046665.png)

  可以看到容器的网络接口地址为172.17.0.2，实际上这是Docker创建的虚拟网络，就像容器单独插了一根虚拟的网线，连接到Docker创建的虚拟网络上，而docker0网络实际上作为一个桥接的角色，一头是自己的虚拟子网，另一头是宿主主机的网络。

  网络拓扑类似于下面这样：

  ![image-20220702173005750](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150046078.png)

  通过添加这样的网桥，我们就可以对容器的网络进行管理和控制，我们可以使用`network inspect`命令来查看docker0网桥的配置信息：

  ```sh
  docker network inspect bridge
  ```

  ![image-20220702173431530](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150046689.png)

  这里的配置的子网是172.17.0.0，子网掩码是255.255.0.0，网关是172.17.0.1，也就是docker0这个虚拟网络设备，所以我们上面创建的容器就是这个子网内分配的地址172.17.0.2了。

  之后我们还会讲解如何管理和控制容器网络。

* **host网络：**当容器连接到此网络后，会共享宿主主机的网络，网络配置也是完全一样的：

  ```sh
  docker run -it --network=host ubuntu-net
  ```

  可以看到网络列表和宿主主机的列表是一样的，不知道各位有没有注意到，连hostname都是和外面一模一样的：

  ![image-20220702170754656](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150046269.png)

  只要宿主主机能连接到互联网，容器内部也是可以直接使用的：

  ![image-20220702171041631](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150046395.png)

  这样的话，直接使用宿主的网络，传输性能基本没有什么折损，而且我们可以直接开放端口等，不需要进行任何的桥接：

  ```sh
   apt install -y systemctl nginx
   systemctl start nginx
  ```

  安装Nginx之后直接就可以访问了，不需要开放什么端口：

  ![image-20220702171550979](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150046993.png)

  相比桥接网络就方便得多了。

我们可以根据实际情况，来合理地选择这三种网络使用。

---

```dockerfile
docker run -it --network=host ubuntu-net
```

```
docker attach 容器名字或者容器id//表示附加（回到）到之前退出未结束运行的容器中（就是ctrl+p+q退出容器，不关闭容器命令的）

docker network connect 网络名字（一开始默认三个，可以（手动）添加一个桥接网络） 容器id或容器名字//用于多个容器组成局域网

docker run -it --network=网络名字 镜像//运行镜像时使用的网络
docker network create --drive 网络驱动名称（如bridge） 网络的名字
```

```dock
docker run -it --name=容器名字 --network 镜像
```

- 创建出来的容器，可以说自带dns(域名解析的功能，域名和ip地址进行绑定) 比如www.baidu.com首先是dns解析域名，域名代表ip地址，然后就去访问ip，也就是我两个同一个路由网关（bridge网络）下（也就是network名称相同下），我另一个容器直接ping同网关的容器是通的！
- 

----

### up

### 用户自定义网络

除了前面我们介绍的三种网络之外，我们也可以自定义自己的网络，让容器连接到这个网络。

Docker默认提供三种网络驱动：`bridge`、`overlay`、`macvlan`，不同的驱动对应着不同的网络设备驱动，实现的功能也不一样，比如bridge类型的，其实就和我们前面介绍的桥接网络是一样的。

我们可以使用`network create`来试试看：

```sh
docker network create --driver bridge test
```

这里我们创建了一个桥接网络，名称为test：

![image-20220702180837819](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150114749.png)

可以看到新增了一个网络设备，这个就是一会负责我们容器网络的网关了，和之前的docker0是一样的：

```sh
docker network inspect test
```

![image-20220702181150667](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150114554.png)

这里我们创建一个新的容器，使用此网络：

```sh
 docker run -it --network=test ubuntu-net
```

![image-20220702181252137](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150115876.png)

成功得到分配的IP地址，是在这个网络内的，注意不同的网络之间是隔离的，我们可以再创建一个容器试试看：

![image-20220702181808792](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150115136.png)

可以看到不同的网络是相互隔离的，无法进行通信，当然我们也为此容器连接到另一个容器所属的网络下：

```sh
docker network connect test 容器ID/名称
```

![image-20220702182050204](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150115046.png)

这样就连接了一个新的网络：

![image-20220702182146049](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150115648.png)

可以看到容器中新增了一个网络设备连接到我们自己定义的网络中，现在这两个容器在同一个网络下，就可以相互ping了：
![image-20220702182310008](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150115665.png)

这里就不介绍另外两种类型的网络了，他们是用于多主机通信的，目前我们只学习单机使用。

### 容器间网络

我们首先来看看容器和容器之间的网络通信，实际上我们之前已经演示过ping的情况了，现在我们创建两个ubuntu容器：

```sh
docker run -it ubuntu-net
```

先获取其中一个容器的网络信息：

![image-20220702175353454](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150115397.png)

我们可以直接在另一个容器中ping这个容器：

![image-20230814155603447](https://s2.loli.net/2023/08/14/go7peBGM6nNI38s.png)

可以看到能够直接ping通，因为这两个容器都是使用的bridge网络，在同一个子网中，所以可以互相访问。

我们可以直接通过容器的IP地址在容器间进行通信，只要保证两个容器处于同一个网络下即可，虽然这样比较方便，但是大部分情况下，容器部署之后的IP地址是自动分配的（当然也可以使用`--ip`来手动指定，但是还是不方便），我们无法提前得知IP地址，那么有没有一直方法能够更灵活一些呢？

我们可以借助Docker提供的DNS服务器，它就像是一个真的DNS服务器一样，能够对域名进行解析，使用很简单，我们只需要在容器启动时给个名字就行了，我们可以直接访问这个名称，最后会被解析为对应容器的IP地址，但是注意只会在我们用户自定义的网络下生效，默认的网络是不行的：

```sh
docker run -it --name=test01 --network=test ubuntu-net
docker run -it --name=test02 --network=test ubuntu-net
```

接着直接ping对方的名字就可以了：

![image-20220702192457354](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150115619.png)

可以看到名称会自动解析为对应的IP地址，这样的话就不用担心IP不确定的问题了。

当然我们也可以让两个容器同时共享同一个网络，注意这里的共享是直接共享同一个网络设备，两个容器共同使用一个IP地址，只需要在创建时指定：

```sh
docker run -it --name=test01 --network=container:test02 ubuntu-net
```

这里将网络指定为一个容器的网络，这样两个容器使用的就是同一个网络了：

![image-20220702200711351](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150115703.png)

可以看到两个容器的IP地址和网卡的Mac地址是完全一样的，它们的网络现在是共享状态，此时在容器中访问，localhost，既是自己也是别人。

我们可以在容器1中，安装Nginx，然后再容器2中访问：

```sh
 apt install -y systemctl nginx
 systemctl start nginx
```

![image-20220702201348722](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309150115528.png)

成功访问到另一个容器中的Nginx服务器。

---

```
docker run -d -p 宿主端口：容器端口 容器名字 镜像 //这样子就能做端口转发
```

```
-d -p 宿主主机端口：容器端口（映射过去）//host是占用端口，容器直接用需要的公网端口，桥接可以指定占用的端口进行转发比如我容器80端口可以用公网82端口，就是我访问82就会去到容器的80
```

---

## 容器数据保存持久性，数据挂载



```
docker run -it -v ~/test:/root/test ubuntu-volume //-v表示数据挂载，前面是宿主主机挂载的路径冒号后面是容器文件路径
```

```
我想搞清楚的是容器创建后，原本我不挂载，它自己是按照它的配置文件运行的。但是我挂载了，我文件夹是空的，那它运行失败，是因为我文件夹没有配置文件吗？说白了是容器的文件放到我宿主主机上还是容器去找我的宿主的文件去运行
```

我明白您的疑问了。在Docker中，容器是独立的运行环境，它们包含了自己的文件系统。当您挂载宿主主机上的文件夹到容器内时，容器会使用挂载的文件夹中的文件替换容器内相应路径的文件。如果您挂载了一个空文件夹，容器将在相应路径上找不到配置文件，因此可能会导致运行失败。

具体来说，Nginx容器通常在容器内有默认的配置文件。当您不挂载任何文件夹时，容器会使用这些默认配置文件来运行Nginx。但是，如果您挂载了一个空的文件夹到容器内的Nginx配置文件路径（例如 `/etc/nginx`），容器会将这个空文件夹视为配置文件目录，并在其中找不到必需的配置文件而失败。

要解决这个问题，您可以选择以下两种方法之一：

1. 挂载自定义Nginx配置文件：在宿主主机上创建一个包含您的自定义Nginx配置文件的文件夹，并将它挂载到容器内的 `/etc/nginx` 目录。这样，您可以使用自己的配置文件来配置Nginx。

2. 不挂载任何文件夹：如果您不希望挂载任何文件夹，可以确保容器内已经存在必需的默认配置文件，并且这些默认配置文件符合您的需求。

总之，当您挂载宿主主机上的文件夹到容器内时，容器会使用挂载的文件夹中的文件替换容器内相应路径的文件。如果挂载的文件夹中没有必需的配置文件，容器可能会由于缺少配置文件而失败。

----



### 容器持久化存储

我们知道，容器在创建之后，实际上我们在容器中创建和修改的文件，实际上是被容器的分层机制保存在最顶层的容器层进行操作的，为了保护下面每一层的镜像不被修改，所以才有了这样的CopyOnWrite特性。但是这样也会导致容器在销毁时数据的丢失，当我们销毁容器重新创建一个新的容器时，所有的数据全部丢失，直接回到梦开始的地方。

在某些情况下，我们可能希望对容器内的某些文件进行持久化存储，而不是一次性的，这里就要用到数据卷（Data Volume）了。

在开始之前我们先准备一下实验要用到的镜像：

```sh
docker run -it ubuntu
apt update && apt install -y vim
```

然后打包为我们一会要使用的镜像：

```
docker commit 
```

我们可以让容器将文件保存到宿主主机上，这样就算容器销毁，文件也会在宿主主机上保留，下次创建容器时，依然可以从宿主主机上读取到对应的文件。如何做到呢？只需要在容器启动时指定即可：

```sh
mkdir test
```

我们现在用户目录下创建一个新的`test`目录，然后在里面随便创建一个文件，再写点内容：

```sh
vim test/hello.txt
```

接着我们就可以将宿主主机上的目录或文件挂载到容器的某个目录上：

```sh
docker run -it -v ~/test:/root/test ubuntu-volume
```

这里用到了一个新的参数`-v`，用于指定文件挂载，这里是将我们刚刚创建好的test目录挂在到容器的/root/test路径上。

![image-20220703105256049](https://s2.loli.net/2022/07/03/ztEJDC4PTVAyZF2.png)

这样我们就可以直接在容器中访问宿主主机上的文件了，当然如果我们对挂载目录中的文件进行编辑，那么相当于编辑的是宿主主机的数据：

```sh
vim /root/test/test.txt  
```

![image-20220703105626105](https://s2.loli.net/2022/07/03/YqUHkJiTG3Q9pAM.png)

在宿主主机的对应目录下，可以直接访问到我们刚刚创建好的文件。

接着我们来将容器销毁，看看当容器不复存在时，挂载的数据时候还能保留：

![image-20220703105847329](https://s2.loli.net/2022/07/03/B5M6Wy8AxIoqJtC.png)

可以看到，即使我们销毁了容器，在宿主主机上的文件依然存在，并不会受到影响，这样的话，当我们下次创建新的镜像时，依然可以使用这些保存在外面的文件。

比如我们现在想要部署一个Nginx服务器来代理我们的前端，就可以直接将前端页面保存到宿主主机上，然后通过挂载的形式让容器中的Nginx访问，这样就算之后Nginx镜像有升级，需要重新创建，也不会影响到我们的前端页面。这里我们来测试一下，我们先将前端模板上传到服务器：

```sh
scp Downloads/moban5676.zip 192.168.10.10:~/
```

然后在服务器上解压一下：

```sh
unzip moban5676.zip
```

接着我们就可以启动容器了：

```sh
docker run -it -v ~/moban5676:/usr/share/nginx/html/ -p 80:80 -d nginx
```

这里我们将解压出来的目录，挂载到容器中Nginx的默认站点目录`/usr/share/nginx/html/`（由于挂在后位于顶层，会替代镜像层原有的文件），这样Nginx就直接代理了我们存放在宿主主机上的前端页面，当然别忘了把端口映射到宿主主机上，这里我们使用的镜像是官方的nginx镜像。

现在我们进入容器将Nginx服务启动：

```sh
systemctl start nginx
```

然后通过浏览器访问看看是否代理成功：

![image-20220703111937254](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309151452962.png)

可以看到我们的前端页面直接被代理了，当然如果我们要编写自定义的配置，也是使用同样的方法操作即可。

注意如果我们在使用`-v`参数时不指定宿主主机上的目录进行挂载的话，那么就由Docker来自动创建一个目录，并且会将容器中对应路径下的内容拷贝到这个自动创建的目录中，最后挂在到容器中，这种就是由Docker管理的数据卷了（docker managed volume）我们来试试看：

```sh
docker run -it -v /root/abc ubuntu-volume
```

注意这里我们仅仅指定了挂载路径，没有指定宿主主机的对应目录，继续创建：

![image-20220703112702067](https://s2.loli.net/2022/07/03/fXCl7IRqKBvYwxj.png)

创建后可以看到`root`目录下有一个新的`abc`目录，那么它具体是在宿主主机的哪个位置呢？这里我们依然可以使用`inspect`命令：

```sh
docker inspect bold_banzai 
```

![image-20220703113507320](https://s2.loli.net/2022/07/03/zFotAfeBpcRjKWN.png)

可以看到Sorce指向的是`/var/lib`中的某个目录，我们可以进入这个目录来创建一个新的文件，进入之前记得提升一下权限，权限低了还进不去：

![image-20220703114333446](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309151453080.png)

我们来创一个新的文本文档：

![image-20220703114429831](https://s2.loli.net/2022/07/03/yi1hSPC3bAndMXm.png)

实际上和我们之前是一样的，也是可以在容器中看到的，当然删除容器之后，数据依然是保留的。当我们不需要使用数据卷时，可以进行删除：

![image-20220703145011638](https://s2.loli.net/2022/07/03/f8NPDWmhLtvw3SV.png)

当然有时候为了方便，可能并不需要直接挂载一个目录上去，仅仅是从宿主主机传递一些文件到容器中，这里我们可以使用`cp`命令来完成：

![image-20220703115648195](https://s2.loli.net/2022/07/03/uw7S5PobAUWBtCI.png)

这个命令支持从宿主主机复制文件到容器，或是从容器复制文件到宿主主机，使用方式类似于Linux自带的`cp`命令。

### 容器数据共享

前面我们通过挂载的形式，将宿主主机上的文件直接挂载到容器中，这样容器就可以直接访问到宿主主机上的文件了，并且在容器删除时也不会清理宿主主机上的文件。

我们接着来看看如何实现容器与容器之间的数据共享，实际上按照我们之前的思路，我们可以在宿主主机创建一个公共的目录，让这些需要实现共享的容器，都挂载这个公共目录：

```sh
docker run -it -v ~/test:/root/test ubuntu-volume
```

![image-20220703141840532](https://s2.loli.net/2022/07/03/soxdKyY4MIXBOin.png)

由于挂载的是宿主主机上的同一块区域，所以内容可以直接在两个容器中都能访问。当然我们也可以将另一个容器挂载的目录，直接在启动容器时指定使用此容器挂载的目录：

```sh
docker run -it -v ~/test:/root/test --name=data_test ubuntu-volume
docker run -it --volumes-from data_test ubuntu-volume
```

这里使用`--volumes-from`指定另一个容器（这种用于给其他容器提供数据卷的容器，我们一般称为数据卷容器）

![image-20220703142849845](https://s2.loli.net/2022/07/03/Uu4CjSZifv1Oyr7.png)

可以看到，数据卷容器中挂载的内容，在当前容器中也是存在的，当然就算此时数据卷容器被删除，那么也不会影响到这边，因为这边相当于是继承了数据卷容器提供的数据卷，所以本质上还是让两个容器挂载了同样的目录实现数据共享。

虽然通过上面的方式，可以在容器之间实现数据传递，但是这样并不方便，可能某些时候我们仅仅是希望容器之间共享，而不希望有宿主主机这个角色直接参与到共享之中，此时我们就需要寻找一种更好的办法了。其实我们可以将数据完全放入到容器中，通过构建一个容器，来直接将容器中打包好的数据分享给其他容器，当然本质上依然是一个Docker管理的数据卷，虽然还是没有完全脱离主机，但是移植性就高得多了。

我们来编写一个Dockerfile：

```dockerfile
FROM ubuntu
ADD moban5676.tar.gz /usr/share/nginx/html/
VOLUME /usr/share/nginx/html/
```

这里我们使用了一个新的指令ADD，它跟COPY命令类似，也可以复制文件到容器中，但是它可以自动对压缩文件进行解压，这里只需要将压缩好的文件填入即可，后面的VOLUME指令就像我们使用`-v`参数一样，会创建一个挂载点在容器中：

```sh
cd test
tar -zcvf moban5676.tar.gz *
mv moban5676.tar.gz ..
cd ..
```

接着我们直接构建：

```sh
docker build -t data .
```

![image-20220703153109650](https://s2.loli.net/2022/07/03/M7jxBUsApKtgzku.png)

现在我们运行一个容器看看：

![image-20220703153343461](https://s2.loli.net/2022/07/03/SUg32jlwMcY7Btp.png)

可以看到所有的文件都自动解压出来了（除了中文文件名称乱码了之外，不过无关紧要）我们退出容器，可以看到数据卷列表中新增了我们这个容器需要使用的：

![image-20220703153514730](https://s2.loli.net/2022/07/03/m6VCIbXyMxt3ilT.png)

![image-20220703153542739](https://s2.loli.net/2022/07/03/KyLUic5r6oW4HDx.png)

这个位置实际上就是数据存放在当前主机上的位置了，不过是由Docker进行管理而不是我们自定义的。现在我们就可以创建一个新的容器直接继承了：

```sh
docker run -p 80:80 --volumes-from=data_test -d nginx
```

访问一下Nginx服务器，可以看到成功代理：

![image-20220703111937254](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309151452962.png)

这样我们就实现了将数据放在容器中进行共享，我们不需要刻意去指定宿主主机的挂载点，而是Docker自行管理，这样就算迁移主机依然可以快速部署。

***

## 容器资源管理

前面我们已经完成Docker的几个主要模块的学习，最后我们来看看如何对容器的资源进行管理。

### 容器控制操作

在开始之前，我们还是要先补充一些我们前面没有提到的其他容器命令。

首先我们的SpringBoot项目在运行是，怎么查看输出的日志信息呢？

```sh
docker logs test
```

这里使用`log`命令来打印容器中的日志信息：

![image-20220701221210083](https://s2.loli.net/2022/07/01/scNgb1uheEpiKL8.png)

当然也可以添加`-f`参数来持续打印日志信息。

![image-20220701215617022](https://s2.loli.net/2022/07/01/QTDeKASvHW1rXlw.png)

现在我们的容器已经启动了，但是我们想要进入到容器监控容器的情况怎么办呢？我们可以是`attach`命令来附加到容器启动命令的终端上：

```sh
docker attach 容器ID/名称
```

![image-20220701215829492](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309151453594.png)

注意现在就切换为了容器内的终端，如果想要退出的话，需要先按Ctrl+P然后再按Ctrl+Q来退出终端，不能直接使用Ctrl+C来终止，这样会直接终止掉Docker中运行的Java程序的。

![image-20220701220018207](https://s2.loli.net/2022/07/01/XkFKtxq3Epua5ib.png)

 退出后，容器依然是处于运行状态的。

我们也可以使用`exec`命令在容器中启动一个新的终端或是在容器中执行命令：

```sh
docker exec -it test bash
```

`-it`和`run`命令的操作是一样的，这里执行后，会创建一个新的终端（当然原本的程序还是在正常运行）我们会在一个新的终端中进行交互：

![image-20220701220601732](https://s2.loli.net/2022/07/01/lMc2JueBLIFz9bf.png)

当然也可以仅仅在容器中执行一条命令：

![image-20220701220909626](https://s2.loli.net/2022/07/01/aVvzjuEM56JmGd7.png)

执行后会在容器中打开一个新的终端执行命令，并输出结果。

前面我们还学习了容器的停止操作，通过输入`stop`命令来停止容器，但是此操作并不会立即停止，而是会等待容器处理善后，那么怎么样才能强制终止容器呢？我们可以直接使用`kill`命令，相当于给进程发送SIGKILL信号，强制结束。

```sh
docker kill test
```

相比`stop`命令，`kill`就没那么温柔了。

有时候可能只是希望容器暂时停止运行，而不是直接终止运行，我们希望在未来的某个时间点，恢复容器的运行，此时就可以使用`pause`命令来暂停容器：

```sh
docker pause test
```

暂停容器后，程序暂时停止运行，无法响应浏览器发送的请求：

![image-20220701222537737](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309151453332.png)

![image-20220701222243900](https://s2.loli.net/2022/07/01/ovbqk7xS3LKhmOH.png)

此时处于爱的魔力转圈圈状态，我们可以将其恢复运行，使用`unpause`命令：

```sh
docker unpause test
```

恢复运行后，瞬间就响应成功了。

![image-20220701222323948](https://s2.loli.net/2022/07/01/g2b8mxVz1i7WJop.png)

### 物理资源管理

对于一个容器，在某些情况下我们可能并不希望它占据所有的系统资源来运行，我们只希望分配一部分资源给容器，比如只分配给容器2G内存，最大只允许使用2G，不允许再占用更多的内存，此时我们就需要对容器的资源进行限制。

```sh
docker run -m 内存限制 --memory-swap=内存和交换分区总共的内存限制 镜像名称
```

其中`-m`参数是对容器的物理内存的使用限制，而`--memory-swap`是对内存和交换分区总和的限制，它们默认都是`-1`，也就是说没有任何的限制（如果在一开始仅指定`-m`参数，那么交换内存的限制与其保持一致，内存+交换等于`-m`的两倍大小）默认情况下跟宿主主机一样，都是2G内存，现在我们可以将容器的内存限制到100M试试看，其中物理内存50M，交换内存50M，尝试启动一下SpringBoot程序：

```sh
docker run -it -m 50M --memory-swap=100M nagocoler/springboot-test:1.0
```

可以看到，上来就因为内存不足无法启动了：

![image-20220702104653971](https://s2.loli.net/2022/07/02/MrBWZKIzgxE94Ck.png)

当然除了对内存的限制之外，我们也可以对CPU资源进行限额，默认情况下所有的容器都可以平等地使用CPU资源，我们可以调整不同的容器的CPU权重（默认为1024），来按需分配资源，这里需要使用到`-c`选项，也可以输入全名`--cpu-share`：

```sh
docker run -c 1024 ubuntu
docker run -c 512 ubuntu
```

这里容器的CPU权重比例为16比8，也就是2比1（注意多个容器时才会生效），那么当CPU资源紧张时，会按照此权重来分配资源，当然如果CPU资源并不紧张的情况下，依然是有机会使用到全部的CPU资源的。

这里我们使用一个压力测试工具来进行验证：

```sh
docker run -c 1024 --name=cpu1024 -it ubuntu
docker run -c 512 --name=cpu512 -it ubuntu
```

接着我们分别进入容器安装`stress`压力测试工具：

```sh
apt update && apt install -y stress
```

接着我们分别在两个容器中都启动压力测试工具，产生4个进程不断计算随机数的平方根：

```sh
stress -c 4
```

接着我们进入top来看看CPU状态（看完之后记得赶紧去kill掉容器，不然CPU拉满很卡的）：

![image-20220702114126128](https://s2.loli.net/2022/07/02/3dHkMWnq1ZxCyKm.png)

可以看到权重高的容器中，分配到了更多的CPU资源，而权重低的容器中，只分配到一半的CPU资源。

当然我们也可以直接限制容器使用的CPU数量：

```sh
docker run -it --cpuset-cpus=1 ubuntu
```

`--cpuset-cpus`选项可以直接限制在指定的CPU上运行，比如现在我们的宿主机是2核的CPU，那么就可以分0和1这两个CPU给Docker使用，限制后，只会使用CPU 1的资源了：

![image-20220702115538699](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309151453831.png)

可以看到，4个进程只各自使用了25%的CPU，加在一起就是100%，也就是只能占满一个CPU的使用率。如果要分配多个CPU，则使用逗号隔开：

```sh
docker run -it --cpuset-cpus=0,1 ubuntu
```

这样就会使用这两个CPU了：

![image-20220702115818344](https://s2.loli.net/2022/07/02/rdAPYlfsgeLOZa9.png)

当然也可以直接使用`--cpus`来限制使用的CPU资源数：

```sh
docker run -it --cpus=1 ubuntu
```

![image-20220702120329140](https://s2.loli.net/2022/07/02/pUGCjlsQbEM2Ika.png)

限制为1后，只能使用一个CPU提供的资源，所以这里加载一起只有一个CPU的资源了。当然还有更精细的`--cpu-period `和`--cpu-quota`，这里就不做介绍了。

最后我们来看一下对磁盘IO读写性能的限制，我们首先使用`dd`命令来测试磁盘读写速度：

```sh
dd if=/dev/zero of=/tmp/1G bs=4k count=256000 oflag=direct
```

可以不用等待跑完，中途Ctrl+C结束就行：

![image-20220702121839871](https://s2.loli.net/2022/07/02/1y3O2qbaMsxDFUJ.png)

可以看到当前的读写速度为86.4 MB/s，我们可以通过`--device-read/write-bps`和`--device-read/write-iops`参数对其进行限制。

这里要先说一下区别：

* bps：每秒读写的数据量。
* iops：每秒IO的次数。

为了直观，这里我们直接使用BPS作为限制条件：

```sh
docker run -it --device-write-bps=/dev/sda:10MB ubuntu
```

因为容器的文件系统是在`/dev/sda`上的，所以这我们就`/dev/sda:10MB`来限制对/dev/sda的写入速度只有10MB/s，我们来测试一下看看：

![image-20220702122557288](https://s2.loli.net/2022/07/02/EczxDAmUCvlwT5u.png)

可以看到现在的速度就只有10MB左右了。

### 容器监控

最后我们来看看如何对容器的运行状态进行实时监控，我们现在希望能够对容器的资源占用情况进行监控，该怎么办呢？

我们可以使用`stats`命令来进行监控：

```sh
docker stats
```

![image-20220702153236692](https://s2.loli.net/2022/07/02/hl6qw7sXuavA4pY.png)

可以实时对容器的各项状态进行监控，包括内存使用、CPU占用、网络I/O、磁盘I/O等信息，当然如果我们限制内存的使用的话：

```sh
docker run -d -m 200M nagocoler/springboot-test:1.0
```

可以很清楚地看到限制情况：

![image-20220702153704729](https://s2.loli.net/2022/07/02/CGc6T4iYyN7PD51.png)

除了使用`stats`命令来实时监控情况之外，还可以使用`top`命令来查看容器中的进程：

```sh
docker top 容器ID/名称
```

![image-20220702153957780](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309151453801.png)

当然也可以携带一些参数，具体的参数与Linux中`ps`命令参数一致，这里就不多做介绍了。

但是这样的监控是不是太原始了一点？有没有那种网页面板可以进行实时监控和管理的呢？有的。

我们需要单独部署一个Docker网页管理面板应用，一般比较常见的有：Portainer，我们这里可以直接通过Docker镜像的方式去部署这个应用程序，搜索一下，发现最新版维护的地址为：https://hub.docker.com/r/portainer/portainer-ce

CE为免费的社区版本，当然也有BE商业版本，这里我们就直接安装社区版就行了，官方Linux安装教程：https://docs.portainer.io/start/install/server/docker/linux，包含一些安装前需要的准备。

首先我们需要创建一个数据卷供Portainer使用：

```sh
docker volume create portainer_data
```

接着通过官方命令安装启动：

```sh
docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```

注意这里需要开放两个端口，一个是8000端口，还有一个是9443端口。

![image-20220702155450772](https://s2.loli.net/2022/07/02/m71ha8YWsUzPFJ4.png)

OK，开启成功，我们可以直接登录后台面板：https://IP:9443/，这里需要HTTPS访问，浏览器可能会提示不安全，无视就行：

![image-20220702155637366](https://s2.loli.net/2022/07/02/mukzgvnWZyrxeaM.png)

![image-20220702155703962](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309151453313.png)

进入后就需要我们进行注册了，这里我们只需输入两次密码即可，默认用户名就是admin，填写完成后，我们就可以开始使用了：

![image-20220702160124676](https://s2.loli.net/2022/07/02/P1JIKaMCl7guYoz.png)

点击Get Started即可进入到管理页面，我们可以看到目前有一个本地的Docker服务器正在运行：

![image-20220702160328972](https://s2.loli.net/2022/07/02/OUTrAEmwsNoSG8Y.png)

我们可以点击进入，进行详细地管理，不过唯一缺点就是没中文，挺难受的，也可以使用非官方的汉化版本：https://hub.docker.com/r/6053537/portainer-ce。

***

## 单机容器编排

最后我们来讲解一下Docker-Compose，它能够对我们的容器进行编排。比如现在我们要在一台主机上部署很多种类型的服务，包括数据库、消息队列、SpringBoot应用程序若干，或是想要搭建一个MySQL集群，这时我们就需要创建多个容器来完成来，但是我们希望能够实现一键部署，这时该怎么办呢？我们就要用到容器编排了，让多个容器按照我们自己的编排进行部署。

**官方文档：**https://docs.docker.com/get-started/08_using_compose/，视频教程肯定不可能把所有的配置全部介绍完，所以如果各位小伙伴想要了解更多的配置，有更多需求的话，可以直接查阅官方文档。

### 快速开始

在Linux环境下我们需要先安装一下插件：

```sh
sudo apt install docker-compose-plugin
```

接着输入`docker compose version`来验证一下是否安装成功。

![image-20220703163126221](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309151453341.png)

这里我们就以部署SpringBoot项目为例，我们继续使用之前打包好的SpringBoot项目，现在我们希望部署这个SpringBoot项目的同时，部署一个MySQL服务器，一个Redis服务器，这时我们SpringBoot项目要运行的整个完整环境，先获取到对应的镜像：

```sh
docker pull mysql/mysql-server
docker pull redis
```

接着，我们需要在自己的本地安装一下DockerCompose，下载地址：https://github.com/docker/compose/releases，下载自己电脑对应的版本，然后在IDEA中配置：

![image-20220703175103531](https://s2.loli.net/2022/07/03/GmcqXEV3tsPQYd9.png)

下载完成后，将Docker Compose可执行文件路径修改为你存放刚刚下载的可执行文件的路径，Windows直接设置路径就行，MacOS下载之后需要进行下面的操作：

```sh
mv 下载的文件名称 docker-compose
sudo chmod 777 docker-compose
sudo mv docker-compose /usr/local/bin
```

配置完成后就可以正常使用了，否则会无法运行，接着我们就可以开始在IDEA中编写docker-compose.yml文件了。

![image-20220703180206437](https://s2.loli.net/2022/07/03/M1gcJFUfQtnEpmB.png)

这里点击右上角的“与服务工具窗口同步”按钮，这样一会就可以在下面查看情况了。

我们现在就从头开始配置这个文件，现在我们要创建三个服务，一个是MySQL服务器，一个是Redis服务器，还有一个是SpringBoot服务器，需要三个容器来分别运行，首先我们先写上这三个服务：

```yaml
version: "3.9"  #首先是版本号，别乱写，这个是和Docker版本有对应的
services:   #services里面就是我们所有需要进行编排的服务了
  spring:   #服务名称，随便起
    container_name: app_springboot  #一会要创建的容器名称
  mysql:
    container_name: app_mysql
  redis:
    container_name: app_redis
```

这样我们就配置好了一会要创建的三个服务和对应的容器名称，接着我们需要指定一下这些容器对应的镜像了，首先是我们的SpringBoot应用程序，可能我们后续还会对应用程序进行更新和修改，所以这里我们部署需要先由Dockerfile构建出镜像后，再进行部署：

```yaml
spring:
  container_name: app_springboot
  build: .  #build表示使用构建的镜像，.表示使用当前目录下的Dockerfile进行构建
```

我们这里修改一下Dockerfile，将基础镜像修改为已经打包好JDK环境的镜像：

```dockerfile
FROM adoptopenjdk/openjdk8
COPY target/DockerTest-0.0.1-SNAPSHOT.jar app.jar
CMD java -jar app.jar
```

接着是另外两个服务，另外两个服务需要使用对应的镜像来启动容器：

```yml
mysql:
  container_name: app_mysql
  image: mysql/mysql-server:latest  #image表示使用对应的镜像，这里会自动从仓库下载，然后启动容器
redis:
  container_name: app_redis
  image: redis:latest
```

还没有结束，我们还需要将SpringBoot项目的端口进行映射，最后一个简单的docker-compose配置文件就编写完成了：

```yaml
version: "3.9"  #首先是版本号，别乱写，这个是和Docker版本有对应的
services:   #services里面就是我们所有需要进行编排的服务了
  spring:   #服务名称，随便起
    container_name: app_springboot  #一会要创建的容器名称
    build: .
    ports:
    - "8080:8080"
  mysql:
    container_name: app_mysql
    image: mysql/mysql-server:latest
  redis:
    container_name: app_redis
    image: redis:latest
```

现在我们就可以直接一键部署了，我们点击下方部署按钮：

![image-20220703182541976](https://s2.loli.net/2022/07/03/bTWZkQidsqfNc9w.png)

![image-20220703182559020](https://s2.loli.net/2022/07/03/YHzOEhS5giBVql2.png)

看到 Running 4/4 就表示已经部署成功了，我们现在到服务器这边来看看情况：

![image-20220703182657205](https://s2.loli.net/2022/07/03/ZAsg3KM8r19malT.png)

可以看到，这里确实是按照我们的配置，创建了3个容器，并且都是处于运行中，可以正常访问：

![image-20220703182958392](https://s2.loli.net/2022/07/03/GqbV1SWMRY8jnEc.png)

如果想要结束的话，我们只需要点击停止就行了：

![image-20220703183240400](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309151454652.png)

当然如果我们不再需要这套环境的话，可以直接点击下方的按钮，将整套编排给down掉，这样的话相对应的容器也会被清理的：

![image-20220703183730693](https://s2.loli.net/2022/07/03/IOVsb3tGpqAnHk9.png)

![image-20220703183807157](https://s2.loli.net/2022/07/03/ZWbxDKTCimdo6Mr.png)

注意在使用docker-compose部署时，会自动创建一个新的自定义网络，并且所有的容器都是连接到这个自定义的网络里面：

![image-20220703210431690](https://s2.loli.net/2022/07/03/NB2MfgA5GZuCSnd.png)

这个网络默认也是使用bridge作为驱动：

![image-20220703210531073](https://s2.loli.net/2022/07/03/jEazItdPKxuRcCQ.png)

这样，我们就完成了一个简单的配置，去部署我们的整套环境。

### 部署完整项目

前面我们学习了使用`docker-compose`进行简单部署，但是仅仅只是简单启动了服务，我们现在来将这些服务给连起来。首先是SpringBoot项目，我们先引入依赖：

```xml
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>

<dependency>
   <groupId>mysql</groupId>
   <artifactId>mysql-connector-java</artifactId>
</dependency>
```

接着配置一下数据源，等等，我们怎么知道数据库的默认密码是多少呢？所以我们先配置一下MySQL服务：

```yaml
mysql:
  container_name: app_mysql
  image: mysql/mysql-server:latest
  environment:   #这里我们通过环境变量配置MySQL的root账号和密码
    MYSQL_ROOT_HOST: '%'   #登陆的主机，这里直接配置为'%'
    MYSQL_ROOT_PASSWORD: '123456.root'    #MySQL root账号的密码，别设定得太简单了
    MYSQL_DATABASE: 'study'    #在启动时自动创建的数据库
    TZ: 'Asia/Shanghai'    #时区
  ports:
  - "3306:3306"    #把端口暴露出来，当然也可以不暴露，因为默认所有容器使用的是同一个网络
```

有关MySQL的详细配置请查阅：https://registry.hub.docker.com/_/mysql

接着我们将数据源配置完成：

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://app_mysql:3306/study   #地址直接输入容器名称，会自动进行解析，前面已经讲过了
    username: root
    password: 123456.root
```

然后我们来写点测试的代码吧，这里我们使用JPA进行交互：

```xml
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<dependency>
   <groupId>org.projectlombok</groupId>
   <artifactId>lombok</artifactId>
</dependency>
```

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@Entity
@Table(name = "db_account")
public class Account {

    @Column(name = "id")
    @Id
    long id;

    @Column(name = "name")
    String name;

    @Column(name = "password")
    String password;
}
```

```java
@Repository
public interface AccountRepository extends JpaRepository<Account, Long> {

}
```

```java
@RestController
public class MainController {

    @Resource
    AccountRepository repository;

    @RequestMapping("/")
    public String hello(){
        return "Hello World!";
    }

    @GetMapping("/get")
    public Account get(@RequestParam("id") long id){
        return repository.findById(id).orElse(null);
    }

    @PostMapping("/post")
    public Account get(@RequestParam("id") long id,
                       @RequestParam("name") String name,
                       @RequestParam("password") String password){
        return repository.save(new Account(id, name, password));
    }
}
```

接着我们来修改一下配置文件：

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://app_mysql:3306/study
    username: root
    password: 123456.root
  jpa:
    database: mysql
    show-sql: true
    hibernate:
      ddl-auto: update   #这里自动执行DDL创建表，全程自动化，尽可能做到开箱即用
```

现在代码编写完成后，我们可以将项目打包了，注意执行我们下面的打包命令，不要进行测试，因为连不上数据库：

```sh
mvn package -DskipTests
```

重新生成jar包后，我们修改一下docker-compose配置，因为MySQL的启动速度比较慢，我们要一点时间等待其启动完成，如果连接不上数据库导致SpringBoot项目启动失败，我们就重启：

```yaml
spring:   #服务名称，随便起
  container_name: app_springboot  #一会要创建的容器名称
  build: .
  ports:
  - "8080:8080"
  depends_on:  #这里设置一下依赖，需要等待mysql启动后才运行，但是没啥用，这个并不是等到启动完成后，而是进程建立就停止等待
  - mysql
  restart: always  #这里配置容器停止后自动重启
```

然后我们将之前自动构建的镜像删除，等待重新构建：

![image-20220703215050497](https://s2.loli.net/2022/07/03/frdTCPDGIuqwAWH.png)

现在我们重新部署docker-compos吧：

![image-20220703215133786](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309151454801.png)

当三个服务全部为蓝色时，就表示已经正常运行了，现在我们来测试一下吧：

![image-20220703215211999](https://s2.loli.net/2022/07/03/3TYABoDZGpK6Rjb.png)

接着我们来试试看向数据库传入数据：

![image-20220703215236719](https://s2.loli.net/2022/07/03/nVEURiAe7qjworl.png)

![image-20220703215245757](https://s2.loli.net/2022/07/03/QKFDdriwJCgPbxW.png)

可以看到响应成功，接着我们来请求一下：

![image-20220703215329690](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309151454831.png)

这样，我们的项目和MySQL基本就是自动部署了。

接着我们来配置一下Redis：

```xml
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

接着配置连接信息：

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://app_mysql:3306/study
    username: root
    password: 123456.root
  jpa:
    database: mysql
    show-sql: true
    hibernate:
      ddl-auto: update
  redis:
    host: app_redis
```

```java
//再加两个Redis操作进来
@Resource
StringRedisTemplate template;

@GetMapping("/take")
public String take(@RequestParam("key") String key){
    return template.opsForValue().get(key);
}

@PostMapping("/put")
public String  put(@RequestParam("key") String key,
                   @RequestParam("value") String value){
    template.opsForValue().set(key, value);
    return "操作成功！";
}
```

最后我们来配置一下docker-compose的配置文件：

```yaml
redis:
  container_name: app_redis
  image: redis:latest
  ports:
  - "6379:6379"
```

OK，按照之前的方式，我们重新再部署一下，然后测试：

![image-20220703220941562](https://s2.loli.net/2022/07/03/2O9ExC4YgrJsjfe.png)

![image-20220703221002195](https://s2.loli.net/2022/07/03/1SRG8EDtx5Oqr2M.png)

这样我们就完成整套环境+应用程序的配置了，我们在部署整个项目时，只需要使用docker-compose配置文件进行启动即可，这样就大大方便了我们的操作，实现开箱即用。甚至我们还可以专门使用一个平台来同时对多个主机进行一次性配置，大规模快速部署，而这些就留到以后的课程中再说吧。
