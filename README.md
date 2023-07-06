# hk4e

## What's this

#### A Genshin Impact game server written in Golang.

#### This project aims to build a high performance ARPG game server, instead of supporting all in-game features

#### The network protocols and configuration tables are mainly from version 3.2, so we suggest you to use a v3.2 client, although it is not necessary

[Download version 3.2.0 from miHoYo](https://autopatchhk.yuanshen.com/client_app/download/pc_zip/20221024103618_h2e3o3zijYKEqHnQ/GenshinImpact_3.2.0.zip)

#### You need to use an https proxy and a patch to use it. Grasscutter is a good example.

#### You can use a newer patch to avoid using https proxies. It supports custom keys and multiple servers. Special thanks to [Jx2f/mhypbase](https://github.com/Jx2f/mhypbase)

## Features

* Native high-availability cluster architecture. Crashes on any node servers won't affect the whole system. So it is highly extendable.
* Player-level non-statued game server. Non-lock single thread structure. Easy to develope. And fully-featured player data exchanger(`Memory<->Cache<->Database`), say goodbye to Deny synchronization-blocked database access
* 新颖的玩家在线跨服无缝迁移功能
* 独创的网关服务器侧客户端协议代理转换功能，拒绝因协议号消息号混淆而带来代码改动的烦恼
* 完整的密钥交换机制实现，安全性++，拒绝一个写死的随机数种子和XOR密钥文件用到天荒地老

## 编译和运行环境

* Go >= 1.18
* Protoc >= 3.21
* Protoc Gen Go >= 1.28
* Docker >= 20.10
* Docker Compose >= 1.29

#### 本项目未使用CGO构建，理论上Windows、Linux系统都可以编译运行，macOS下没有`unix.Gettid`的实现，需要使用CGO构建

## 快速启动

* 首次需要安装工具

```shell
make dev_tool
```

* 生成协议

```shell
make gen_natsrpc      # 生成natsrpc协议
make gen_proto        # 生成客户端协议
make gen_client_proto # 生成客户端协议代理(非必要 详见gate/client_proto/README.md)
```

* 构建

```shell
make build         # 构建服务器二进制文件
make docker_config # 复制配置模板等文件
make docker_build  # 构建镜像
```

* 启动

```shell
cd docker
# 启动前请先确保各服务器的配置文件正确(如docker/node/bin/application.toml)
docker-compose up -d # 启动服务器
```

#### 第三方组件

* mongodb
* nats-server
* redis

#### 服务器组件

* node 节点服务器 (仅单节点 有状态)
* dispatch 登录服务器 (可多节点 无状态)
* gate 网关服务器 (可多节点 有状态)
* anticheat 反作弊服务器 (可多节点 有状态 尚不完善非必要启动)
* pathfinding 寻路服务器 (可多节点 无状态 尚不完善非必要启动)
* gs 游戏服务器 (可多节点 有状态)
* gm 游戏管理服务器 (仅单节点 无状态)

#### 其它

* 独立运行需要配置环境变量

```shell
GOLANG_PROTOBUF_REGISTRATION_CONFLICT=ignore
```

## 代码提交规范

* 提交前**必须**进行go fmt(GoLand可在commit窗口的设置里勾选，默认是启用的)
* 进行全局格式化时，请跳过gdconf目录，这是配置表数据，包含大量的json、lua、txt等文件
* 单行注释的注释符与注释内容之间需要加一个空格(GoLand可在设置Editor/CodeStyle/Go/Other里打开)
* 导入包时需要将标准库、本地包、第三方包用空行分开(GoLand可在设置Editor/CodeStyle/Go/Imports里打开)
