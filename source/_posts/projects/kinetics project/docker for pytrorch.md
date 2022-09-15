---
title: 使用docker配置深度学习环境
tags: 
- 环境配置
- docker
categories:
- kinetics项目
---

# docker使用基础

> 参考教程：
>
> https://www.runoob.com/docker/docker-container-usage.html
>
> https://blog.csdn.net/dreamhome_s/article/details/106049253
>
> 这里默认docker已安装好

## 1. dockers运行命令

```sh
docker run --gpus all -itd --name torchtest -v $PWD -w /tmp/workplace -p 10035:22 -p 8889:8888 pytorch/pytorch:latest bash
```

**notes**:

- 10035映射到容器的22号端口：用于SSH远程连接服务器

- 8889映射到8888端口：用于远程访问服务器的jupyter notebook
- 若端口号被占用，换一个未被占用的即可

- -v -w：将当前目录挂载到容器的workplace目录下
- -i: 交互式操作。
- -t: 终端。

- -d: 在后台执行

可以将该命令写入**脚本**里方便持续执行，如图：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202204141420457.png" alt="image-20220414142028424" style="zoom: 67%;" />

## 2. docker进入命令

- **docker exec**：推荐大家使用 docker exec 命令，因为此命令会退出容器终端，但不会导致容器的停止。

```sh
docker exec -it torchtest /bin/bash
```

使用该命令可以进入终端，退出root也不会导致容器停止。

## 3. 开启SSH服务

- 安装SSH服务：

```sh
apt update
apt install openssh-server
service ssh start
```

- 设置容器密码：

```
输入passwd设置密码
```

- 修改容器配置：

```sh
apt install vim
vim /etc/ssh/sshd_config
# 在 sshd_config 文件里
PermitRootLogin prohibit-password # 注释掉
PermitRootLogin yes # 添加该行
```

- 重新激活SSH服务：

```sh
service ssh restart
```

- 在本机测试是否可以连接成功：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202204141504398.png" alt="image-20220414150403363" style="zoom:50%;" />

 ## 4. docker 保存镜像

> 当对环境做出了修改时，安装新的包等，我们需要对当前比较稳定的环境做一个备份。方便错误恢复或是迁移到其他机器。

> 有这个需求的原因是需要在宿主机和docker容器加一个端口映射，但是我不想丢失之前对容器所做的任何变动，才有了这一步骤

### 基础版-基于备份

**导出容器**

```sh
sudo docker export 1e560fca3906 > ubuntu.tar
```

**导入容器**

```sh
cat docker/ubuntu.tar | sudo docker import - test/ubuntu:v1
```

## 进阶版-基于版本控制

**更新镜像**

```sh
docker commit -m="has update" -a="runoob" e218edb10161 runoob/ubuntu:v2
```

各个参数说明：

- **-m:** 提交的描述信息
- **-a:** 指定镜像作者
- **e218edb10161：**容器 ID
- **runoob/ubuntu:v2:** 指定要创建的目标镜像名

可以使用 **docker images** 命令来查看我们的新镜像 **runoob/ubuntu:v2**：