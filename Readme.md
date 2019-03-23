# 实验一 : Docker环境在云上服务器搭建

## 亚马逊云服务器的使用

在观看视频之前，要先进行“AWS在线研讨会2019”的注册，这里的注册比亚马逊云账号的注册要简单，提供有效的邮箱即可，不再赘述。

首先看两个视频教程演示:

- AWS_基础服务介绍及实操_-_计算、存储和访问权限管理

URL链接 : [https://amazonaws-china.com/cn/about-aws/events/webinar/#AWS_基础服务介绍及实操_-_计算、存储和访问权限管理](https://amazonaws-china.com/cn/about-aws/events/webinar/#AWS_基础服务介绍及实操_-_计算、存储和访问权限管理)

- AWS 基础服务介绍及实操 - 利用 Amazon VPC 服务搭建经典 Web 三层架构

URL链接 : [https://amazonaws-china.com/cn/about-aws/events/webinar/#AWS_基础服务介绍及实操_-_利用_Amazon_VPC_服务搭建经典_Web_三层架构](https://amazonaws-china.com/cn/about-aws/events/webinar/#AWS_基础服务介绍及实操_-_利用_Amazon_VPC_服务搭建经典_Web_三层架构)

## Docker的使用

### 1. 准备 Docker 环境

下面演示在服务器的环境是 Ubuntu 18.04 下安装Docker，既然采用的是亚马逊云服务器，服务器在国外，可以使用Docker官方的文档来安装Docker。
[https://docs.docker.com/install/linux/docker-ce/ubuntu/](https://docs.docker.com/install/linux/docker-ce/ubuntu/)

**注意 :** 安装的是 Docker CE 版本

### 2. 编写 Dockerfile

```
mkdir base-tomcat-maven

cd base-tomcat-maven

cat > Dockerfile <<'EOF'
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
EOF
```

### 3. build 镜像

```
docker build -t base-tomcat-maven .

-t # 镜像名称:标签

. # 表示使用当前目录下的 Dockerfile, 还可以通过-f 指定 Dockerfile 所在路径
```

运行成功会看到一个叫base-tomcat-maven的镜像

基于base-tomcat-maven镜像，将java应用通过maven编译打包放到tomcat webapps目 录，生成最终镜像。demo代码见github

```
git clone https://github.com/2019-web/docker-demo-java-tomcat.git

cd docker-demo-java-tomcat

sed -i '/^FROM/c FROM base-tomcat-maven' Dockerfile # 修改 Dockerfile 中的 base 镜像

docker build -t docker-demo-java-tomcat .
```


### 4. 运行容器

基于镜像，运行容器

```
docker run -idt --name demo -p 8001:8080 docker-demo-java-tomcat:latest  
curl localhost:8001
```



如果使用 AWS 服务器，默认的安全组规则会拦截服务器的入站流量。为了能够正常访问，我们需要放开对 8080 端口的限制：

1. 进入 AWS 控制面板，点击左边的【安全组】

2. 点击下方的【入站】->【编辑】

3. 增加入站规则： HTTP 或 HTTPS 可以允许他人访问你服务器上的网址；自定义 TCP 端口 8001 ，允许来源 【任意位置】。

如果一切正常，就可以通过 服务器公网 IP:8001 浏览器来访问你的项目了。






