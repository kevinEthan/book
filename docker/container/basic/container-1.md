`docker`中的容器就是一个轻量级的虚拟机，是镜像运行起来的一个状态，本文就先来看看容器的基本操作。

## 查看容器

### 查看容器

启动`docker`后，使用`docker ps`命令可以查看当前正在运行的容器：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191115110517793.png)

### 查看所有容器

上面这条命令是查看当前正在运行的容器，如果需要查看所有容器，则可以通过`docker ps -a`命令查看：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191115110740203.png)

在查看容器时，涉及到几个查看参数，含义分别如下：
    
   1. CONTAINER ID
     
      `CONTAINER ID`是指容器的id，是一个=唯一标识符，这是一个64位的十六进制整数，
      在不会混淆的情况下可以只采取id的前几位进行标识一个容器。   
   
   2. IMAGE
     
      `IMAGE`表示创建容器时使用的镜像。

   3. CONTAINER
     
      `CONTAINER ID`表示容器最后运行的命令。   

   4. CREATED
     
      创建容器时间。 
   
   5. STATUS
   
      容器的状态，这里可能显示一个容器启动时间，也能显示容器关闭时间。具体显示哪个要看容器当前的状态。       
   
   6. PORTS
      
      容器对外开放的端口
   
   7. NAMES  
   
      容器的名字，如果不设置，会随机生成一个名字
      
### 查看最新创建的容器

使用`docker ps -l`可以查看最新创建的容器，如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191115111945502.png)

### 查看最新创建的n个容器

可以使用`docker ps -n=num`来查看最新创建的n个容器，如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191115112124965.png)

## 创建容器

创建容器整体上来说有两种不同的方式，可以先创建，再启动，也可以连创建带启动一步到位，无论是哪种方式，
流程都是相似的，当执行一个创建命令之后，`docker`首先会先去本地路径下查找是否有相应的镜像，如果没有，
就去`docker hub`上搜索，如果搜索到了，则下载下来，然后利用该镜像创建一个容器并启动。容器的文件系统
是在只读的镜像文件上添加一层可读写的文件层，这样可以使在不改变镜像情况下，只记录改变的数据。下面对
这两种方式分别予以介绍。

### 容器创建

开发者可以首先使用`docker create`命令创建一个容器，这个时候创建出来的容器是处于停止状态，没有运行，
例如要创建一个nginx容器，创建命令如下：

```bash
docker create nginx
```

创建成功后，可以查看容器是否创建成功：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191115113450334.png)

此时创建的容器并未运行，处于停止状态，容器的name也是随机生成的，开发者也可以在创建容器时指定name，如下：

```bash
docker create --name=nginx nginx
```

运行结果如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191115132035699.png)

此时的name属性就不是随机生成的，而是用户指定的那么。

这种方式只是单纯的创建一个容器，并未启动。

### 容器创建+启动

如果开发者需要即创建又启动容器，则可以使用`docker run`命令。`docker run`命令又可以启动二种不同
模式的容器：后台型容器和交互型容器，顾名思义，后台型容器就是一个在后台运行的容器，默默地在后台执行
计算就行了，不需要和开发者进行交互，而交互型容器则需要接收开发者的输入进行处理给出反馈。对于开发者
而言，大部分情况下创建的都是后台型容器，不过很多时候，即使是后台型容器也不可避免的需要进行交互，
下面分别来看：

#### 后台型容器

后台型容器以`nginx`为例，一般`nginx`在后台运行即可

```bash
docker run --name nginx -d -p 8080:80 nginx
```

--name 含义和上文一样，表示创建的容器的名字。
-d 表示容器在后台运行。
-p 标识将容器的80端口映射到宿主机的8080端口。
创建过程如下图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191115134324213.png)

首先依然会去本地检查，本地没有相应的容器，则会去`Docker Hub`上去查找，查找到了下载并运行，
并且生成了一个容器id。运行成功后，在浏览器中输入`http://localhost:8080`就能看到Nginx的
默认的页面了，如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191115134911556.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FiZWxldGhhbg==,size_16,color_FFFFFF,t_70)

这是一个后台型容器的基本创建方式。

#### 交互型容器

也可以创建交互型容器，例如创建`Centos`容器，开发者可能需要在`Centos`上面输入命令执行相关操作，
交互型容器创建方式如下：

```bash
docker run --name centos -it centos /bin/bash
```

参数含义都和上文的一致。除了-it，-it参数，i表示开发容器的标准输入（STDIN）,t则表示告诉`docker`，
容器创建一个命令行终端，执行结果如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191115135407660.png)

该命令执行完后，会打开一个输入终端，读者就可以在这个终端愉快的操作`Centos`了。
想要退出该终端，只需输入exit命令即可。