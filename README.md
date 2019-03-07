## 感谢
[@v2ray](https://github.com/v2ray/v2ray-core)
[@Caddy](https://github.com/mholt/caddy)
[@caddy-docker](https://github.com/abiosoft/caddy-docker)
[@cloudflare](https://www.cloudflare.com/)

## docker-v2ray ？
-  GCP 是不是还是经常断流？掉包严重？
我们就通过Cloudflare 的CDN过一下
-  网上不是有v2ray 一键脚本吗？
NO!NO!NO!容器化比较符合撸主口味
但是没发现有懒人版。
这里就集成整合一下。
- 我这里的V2RAY设置了什么？
这里采用了最保险的方案Websocket tls cloudflare
如果大家有ALIYUN国内服务器可以省略cloudflare环节。 
-  这里为什么用Caddy？不用Nginx?
因为Caddy继承了Let's Encrypt申请，不用再单独用 certbot 去申请了。
记住需要mount Caddy 的$HOME/.caddy:/root/.caddy，因为Let’s Encrypt[限制](https://letsencrypt.org/docs/rate-limits/)了

## 需要修改哪些
因为V2RAY属于链式协议栈，大家可以根据自己小鸡情况自由编排，我这里只放出了最简单通用的配置。

GCP（服务端）

配置 server/config.json

用8082逻辑上混淆概率更高一点，至于加密大家可以根据自己需求配置

本地（客户端） client/config.json 

设置socks代理 1080端口 HTTP代理1082端口

默认配置的UUID是：65b30af2-6694-4a4c-9677-54bf7b2b8d10,自己[生成UUID](https://www.uuidgenerator.net/)替换


## 怎么使用
- 修改 docker-compose.yml 中 cloudflare 自己的信息
- 运行脚本
```bash
## build docker image
docker-compose build
## exec at server side
docker-compose up -d
## exec at client side
docker run -it --rm  -d -v $PWD/client:/etc/v2ray v2ray/official
```
## 其他
如果build Caddy时候出现  ERROR: error initializing submodules: usage: git submodule [--quiet] add [-b <branch>] [-f|--force] [--name <name>] [--reference <repository>] [--] <repository> [<path>]

主要是因为 Docker 会在Clone以后执行  git submodule update --init --recursive --depth=1 
但是--depth这个参数在git 2.10之前并不支持。 
我的是Centos解决办法如下

```bash
sudo yum install -y https://centos7.iuscommunity.org/ius-release.rpm
sudo yum swap git git2u
```
