# 服务器搭建Docker环境,方便迁移

我需要docker的环境是有这些的:

* centOS

* nano

* shadowsocks

* nginx

* nodejs

* python

* mongoDB

* mySQL

* git

## Install Docker

* MAC

Install Docker Engine

```bash
brew cask install docker
```

* Linux

Uninstall Older version

```bash
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

Install Docker Engine

```bash
sudo yum update

sudo yum install docker-ce docker-ce-cli containerd.io
```

Install Docker Engine

```bash
sudo yum update

sudo yum install docker-ce docker-ce-cli containerd.io
```

Start Docker

```bash
sudo systemctl start docker
```

## Create Image By Dockerfile

// TODO ...

```Dockerfile
FROM centos
RUN echo 'bullet.Y CentOS Image Start...'

RUN echo 'yum Update & Install...'
RUN yum update && yum install -y

RUN echo 'Install Nano...'
RUN yum install -y nano

RUN echo 'Install Nginx...'
RUN yum install -y nginx
RUN echo 'Starting Nginx...'
RUN sudo systemctl start nginx.service
RUN sudo systemctl enable nginx.service

```
