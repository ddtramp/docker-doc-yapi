## README

DOCKER [YAPI](https://github.com/YMFE/yapi)

## INSTALL MONGO

Run `docker stack deploy -c stack.yml mongo` (or `docker-compose -f stack.yml up -d`), wait for it to initialize completely, and visit http://swarm-ip:8081, http://localhost:8081, or http://host-ip:8081 (as appropriate).

mongodb need initlize

```
    1. docker exec -it containerId /bin/bash

    2. mongo -u root -p root

    3. show dbs

    4. use yapi

    4. db.test.inserOne({ test: "should be delete this collection" })

```

## INSTALL YAPI

[使用 Docker 构建 Yapi](https://www.jianshu.com/p/a97d2efb23c5)

1. 获取 Yapi 镜像，版本信息可在 阿里云镜像仓库 查看

`docker pull registry.cn-hangzhou.aliyuncs.com/anoy/yapi`

2. 初始化 Yapi 数据库索引及管理员账号

```bash

docker run -it --rm \
  --link docker-yapi_default:mongo \
  --network docker-yapi_default \
  -v /d/github/docker-yapi/config.json:/api/config.json \
  --entrypoint npm \
  --workdir /api/vendors \
  registry.cn-hangzhou.aliyuncs.com/anoy/yapi \
  run install-server

```
初始化管理员账号成功,账号名："w********o001@gmail.com"，密码："a**1********9"

自定义配置文件挂载到目录 /api/config.json，官方自定义配置文件 -> [传送门](https://github.com/YMFE/yapi/blob/master/config_example.json)

4. 启动 Yapi 服务

```
docker run -d \
  --name yapi \
  --link docker-yapi_default:mongo \
  --network docker-yapi_default \
  -v /d/github/docker-yapi/config.json:/api/config.json \
  --workdir /api/vendors \
  -p 3008:3000 \
  registry.cn-hangzhou.aliyuncs.com/anoy/yapi \
  server/app.js
```



5. 使用 Yapi

访问 http://localhost:3000 登录账号 admin@admin.com，密码 ymfe.org

[Yapi 官方文档](https://hellosean1025.github.io/yapi/)

[Yapi 版本更新记录](https://github.com/YMFE/yapi/blob/master/CHANGELOG.md)


4. 其他相关操作

关闭 yapi

`docker stop yapi`

启动 Yapi

`docker start yapi`

升级 yapi

```
# 1、停止并删除旧版容器
docker rm -f yapi

# 2、获取最新镜像
docker pull registry.cn-hangzhou.aliyuncs.com/anoy/yapi

# 3、启动新容器
docker run -d \
  --name yapi \
  --link mongo-yapi:mongo \
  --workdir /api/vendors \
  -p 3000:3000 \
  registry.cn-hangzhou.aliyuncs.com/anoy/yapi \
  server/app.js
```

## 手动构建 yapi 镜像

1. 下载 YAPI 到本地

`wget -o yapi.tar.gz https://github.com/YMFE/yapi/archive/v1.8.0.tar.gz`

下载地址：[https://github.com/YMFE/yapi/releases](https://github.com/YMFE/yapi/releases)

2. 编辑 Dockerfile

```
FROM node:12-alpine as builder

RUN apk add --no-cache git python make openssl tar gcc

COPY yapi.tar.gz /home

RUN cd /home && tar zxvf yapi.tar.gz && mkdir /api && mv /home/yapi-1.8.0 /api/vendors

RUN cd /api/vendors && \
    npm install --production --registry https://registry.npm.taobao.org

FROM node:12-alpine

MAINTAINER 545544032@qq.com

ENV TZ="Asia/Shanghai" HOME="/"

WORKDIR ${HOME}

COPY --from=builder /api/vendors /api/vendors

COPY config.json /api/

EXPOSE 3000

ENTRYPOINT ["node"]

```

3. 构建镜像

`docker build -t yapi .`


## ISSUE

1. cross-request 插件无法安装

    1. 选择本地下载
    
    2. 解压 出 *.crx 文件

    3. 解压 *.crx 文件

    4. chrome 扩展程序 -> 加载已解压的扩展程序

    5. 选择 step 3 解压出来的文件夹

    6. 完成

在线 store 无法安装。

直接安装 `.crx` 无法使用。

## REFERENCES

[https://github.com/YMFE/yapi](https://github.com/YMFE/yapi)

[yapi 官方文档](https://hellosean1025.github.io/yapi)