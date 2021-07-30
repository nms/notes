# docker 常用命令

作用|命令
--:|--:
下载镜像|`docker pull xxx/yyy`
推送镜像|`docker push xxx/yyy`
查看镜像|`docker images`
删除镜像|`docker rmi xxx/yyy`
删除所有镜像|`docker rmi $(docker images)`
Dockerfile构建镜像|`docker build -t [镜像名称] .(点 是默认查找)`
强制删除镜像|`docker rmi -f xxx/yyy`
查看镜像构建历史|`docker history [镜像]`
查看运行的容器|`docker ps`
查看所有容器|`docker ps -a`
创建容器|`docker run -itd --name [容器名称] [镜像名称]`
进入容器|`docker exec -it [容器名称] [挂起命令(top ping sh bash)]`
容器转为镜像|`docker commit -m="消息内容" [容器名称] [镜像名称]`
启动容器|`docker start [容器名称]`
停止容器|`docker stop [容器名称]`
删除容器|`docker rm [容器名称]`
删除所有容器|`docker rm $(docker ps -a -q)`
查看所有容器|`docker ps -a`
查看所有容器|`docker ps -a`
查看所有容器|`docker ps -a`
查看所有容器|`docker ps -a`
查看所有容器|`docker ps -a`




