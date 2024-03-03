# Dify 安装

## Clone Dify
Clone Dify 源代码至本地
```
git clone https://github.com/langgenius/dify.git   
```

## 启动Dify
进入 dify 源代码的 docker 目录，执行一键启动命令：
```
cd dify/docker
docker compose up -d
```
> 如果您的系统安装了 Docker Compose V2 而不是 V1，请使用 docker compose 而不是 docker-compose。通过$ docker compose version检查这是否为情况。
部署结果：
```
[+] Running 8/8
 - Network docker_default Created                                                                  
 - Container docker-weaviate-1  Started  
 - Container docker-redis-1     Started        
 - Container docker-web-1       Started   
 - Container docker-db-1        Started   
 - Container docker-api-1       Started     
 - Container docker-worker-1    Started     
 - Container docker-nginx-1     Started  
```
最后检查是否所有容器都正常运行：
```
docker compose ps
```
包括 3 个业务服务 api / worker / web，以及 4 个基础组件 weaviate / db / redis / nginx。
```
NAME                IMAGE                              COMMAND                  SERVICE             CREATED             STATUS              PORTS
docker-api-1        langgenius/dify-api:0.3.2          "/entrypoint.sh"         api                 4 seconds ago       Up 2 seconds        80/tcp, 5001/tcp
docker-db-1         postgres:15-alpine                 "docker-entrypoint.s…"   db                  4 seconds ago       Up 2 seconds        0.0.0.0:5432->5432/tcp
docker-nginx-1      nginx:latest                       "/docker-entrypoint.…"   nginx               4 seconds ago       Up 2 seconds        0.0.0.0:80->80/tcp
docker-redis-1      redis:6-alpine                     "docker-entrypoint.s…"   redis               4 seconds ago       Up 3 seconds        6379/tcp
docker-weaviate-1   semitechnologies/weaviate:1.18.4   "/bin/weaviate --hos…"   weaviate            4 seconds ago       Up 3 seconds        
docker-web-1        langgenius/dify-web:0.3.2          "/entrypoint.sh"         web                 4 seconds ago       Up 3 seconds        80/tcp, 3000/tcp
docker-worker-1     langgenius/dify-api:0.3.2          "/entrypoint.sh"         worker              4 seconds ago       Up 2 seconds        80/tcp, 5001/tcp
```

## 升级Dify
进入 dify 源代码的 docker 目录，按顺序执行以下命令：
```
cd dify/docker
git pull origin main
docker compose down
docker compose pull
docker compose up -d
```

## 踩坑日志
1. git clone 命令报错不能下载代码。异常信息:
```
Cloning into 'dify'...

fatal: unable to access 'https://github.com/langgenius/dify/': Failed to connect to 192.168.110.46 port 7890 after 21053 ms: Couldn't connect to server
```
初步排查是因为没有配置代理，因为是windows安装，先配置控制台代理:
```
set HTTP_PROXY=http://127.0.0.1:9876
set HTTPS_PROXY=https://127.0.0.1:9876
```
配置完之后还是报错，继续配置git的代理
```
git config --global http.proxy http://127.0.0.1:9876
git config --global https.proxy https://127.0.0.1:9876
```
配置完之后还是报相同错误，意识到可能是之前配置过代理，由于改动ip地址导致之前的失效了。查看全部代理
```
git config --global --get-regexp proxy

core.gitproxy trace

remote.origin.proxy 192.168.110.46:7890

https.proxy https://127.0.0.1:9876

http.proxy http://127.0.0.1:9876
```
删除失效代理
```
git config --global --unset remote.origin.proxy
```

git clone命令可以正常下载代码。