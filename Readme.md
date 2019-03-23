# 实验一 : Docker环境在云上服务器搭建

## 亚马逊云服务器的使用

在观看视频之前，要先进行“AWS在线研讨会2019”的注册，这里的注册比亚马逊云账号的注册要简单，提供有效的邮箱即可，不再赘述。

首先看两个视频教程演示:

- AWS_基础服务介绍及实操_-_计算、存储和访问权限管理

URL链接 : [https://amazonaws-china.com/cn/about-aws/events/webinar/#AWS_基础服务介绍及实操_-_计算、存储和访问权限管理](https://amazonaws-china.com/cn/about-aws/events/webinar/#AWS_基础服务介绍及实操_-_计算、存储和访问权限管理)

- AWS 基础服务介绍及实操 - 利用 Amazon VPC 服务搭建经典 Web 三层架构

URL链接 : [https://amazonaws-china.com/cn/about-aws/events/webinar/#AWS_基础服务介绍及实操_-_利用_Amazon_VPC_服务搭建经典_Web_三层架构](https://amazonaws-china.com/cn/about-aws/events/webinar/#AWS_基础服务介绍及实操_-_利用_Amazon_VPC_服务搭建经典_Web_三层架构)

## 实验——场景 2：带有公有子网和私有子网 (NAT) 的 VPC

URL链接 : [https://docs.aws.amazon.com/zh_cn/vpc/latest/userguide/VPC_Scenario2.html](https://docs.aws.amazon.com/zh_cn/vpc/latest/userguide/VPC_Scenario2.html)

> 这个场景的配置包括一个有公有子网和私有子网的 Virtual Private Cloud (VPC)。如果您希望运行面向公众的 Web 应用程序，并同时保留不可公开访问的后端服务器，我们建议您使用此场景。常用例子是一个多层网站，其 Web 服务器位于公有子网之内，数据库服务器则位于私有子网之内。您可以设置安全性和路由，以使 Web 服务器能够与数据库服务器建立通信。

> 公有子网中的实例可直接将出站流量发往 Internet，而私有子网中的实例不能这样做。但是，私有子网中的实例可使用位于公有子网中的网络地址转换 (NAT) 网关访问 Internet。数据库服务器可以使用 NAT 网关连接到 Internet 进行软件更新，但 Internet 不能建立到数据库服务器的连接。

我们最终的期末Project是前后端分离的项目，简单点的建构是 : 需要两个服务器实例，一台服务器运行前端程序，位于**公有子网**，后端程序，位于**私有子网**。

## Docker的使用

在公有子网下创建实例，启动一台服务器，在这台服务器上配置Docker，完成下面的实验。

URL链接 : [https://github.com/2019-web/base-tomcat-maven](https://github.com/2019-web/base-tomcat-maven)

URL链接 : [https://github.com/2019-web/docker-demo-java-tomcat](https://github.com/2019-web/docker-demo-java-tomcat)


### 1. 准备 Docker 环境

下面演示在服务器环境是 Ubuntu 18.04 下安装Docker，既然采用的是亚马逊云服务器，服务器在国外，可以使用Docker官方的文档来安装Docker。

安装文档URL链接 : [https://docs.docker.com/install/linux/docker-ce/ubuntu/](https://docs.docker.com/install/linux/docker-ce/ubuntu/)

**注意 :** 安装的是 Docker CE 版本


### 2. 编写 Dockerfile

```
mkdir base-tomcat-maven

cd base-tomcat-maven

# 创建并编辑Dockerfile
FROM maven:3.3.3
ENV CATALINA_HOME /usr/local/tomcat
ENV PATH CATALINAHOME/bin:PATH
RUN mkdir -p "$CATALINA_HOME"
WORKDIR $CATALINA_HOME
ENV TOMCAT_VERSION 8.5.39
ENV TOMCAT_TGZ_URL https://www.apache.org/dist/tomcat/tomcat-8/vTOMCATVERSION/bin/apache−tomcat−TOMCAT_VERSION.tar.gz
RUN set -x \
&& curl -fSL "$TOMCAT_TGZ_URL" -o tomcat.tar.gz \
&& tar -xvf tomcat.tar.gz --strip-components=1 \
&& rm bin/*.bat \
&& rm tomcat.tar.gz*
EXPOSE 8080
CMD ["catalina.sh", "run"]
```

### 3. build 镜像

```
docker build -t base-tomcat-maven .

-t # 镜像名称:标签

. # 表示使用当前目录下的 Dockerfile, 还可以通过-f 指定 Dockerfile 所在路径
```

```
docker ps
```
运行成功会看到一个叫base-tomcat-maven的镜像

基于base-tomcat-maven镜像，将java应用通过maven编译打包放到tomcat webapps目 录，生成最终镜像。demo代码见github

```
git clone https://github.com/2019-web/docker-demo-java-tomcat.git

cd docker-demo-java-tomcat

docker build -t docker-demo-java-tomcat .
```


### 4. 运行容器

基于镜像，运行容器

```
# 创建并启动一个名为demo的docker容器，主机的8001端口映射docker容器的8080端口
docker run -idt --name demo -p 8001:8080 docker-demo-java-tomcat:latest
# 模拟HTTP请求，测试浏览器是否能正常访问  
curl localhost:8001
```


### 说明

如果使用 AWS 服务器，默认的安全组规则会拦截服务器的入站流量。为了能够正常访问，我们需要放开对 8080 端口的限制：

1. 进入 AWS 控制面板，点击左边的【安全组】

2. 点击下方的【入站】->【编辑】

3. 增加入站规则： HTTP 或 HTTPS 可以允许他人访问你服务器上的网址；自定义 TCP 端口 8001 ，允许来源 【任意位置】。

如果一切正常，就可以通过 服务器公网 IP:8001 浏览器来访问你的项目了。






