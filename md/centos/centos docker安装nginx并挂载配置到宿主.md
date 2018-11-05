### 1.拉取镜像
>docker pull nginx
```shell
Using default tag: latest
Trying to pull repository docker.io/library/nginx ...
latest: Pulling from docker.io/library/nginx
f17d81b4b692: Pull complete
d5c237920c39: Pull complete
a381f92f36de: Pull complete
Digest: sha256:b73f527d86e3461fd652f62cf47e7b375196063bbbd503e853af5be16597cb2e
Status: Downloaded newer image for docker.io/nginx:latest
```
查看镜像
>docker images

|REPOSITORY  |              TAG     |           IMAGE ID    |        CREATED      |       SIZE|
|---|---|---|---|---|
docker.io/nginx    |       latest       |       dbfc48660aeb     |   2 weeks ago    |     109 MB|

### 2.挂载配置
#### 先挂载html文件
>mkdir /root/docker_data/nginx/html

>vim html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Nginx</title>
</head>
<body>
<h1>Hello nginx!</h1>
</body>
</html>
```
然后启动docker，添加html挂载目录
>docker run -d -p 80:80 --name mynginx -v /root/docker_data/nginx/html:/usr/share/nginx/html nginx

访问 服务器ip地址

![img](https://github.com/yangzhenlong/image.server/blob/master/images/upload/image.png)
说明html挂载成功了

### 挂载其他配置文件
把docker容器中的配置文件拷贝的宿主机器当前目录
>cd /root/docker_data/nginx
>docker cp bbd7d30fc8f1:/etc/nginx /root/docker_data/nginx/

把nginx目录重命名为conf
>mv nginx conf

>ls
```
conf  html
```

这时候停止并删除之前运行的nginx容器
>docker stop bbd7d30fc8f1

>docker rm bbd7d30fc8f1

重新启动一个nginx容器，挂载html文件，挂载配置文件
>cd /root/docker_data/nginx

>ls
```
conf  html
```
>docker run -d -p 80:80 --name mynginx -v /root/docker_data/nginx/html:/usr/share/nginx/html -v /root/docker_data/nginx/conf:/etc/nginx nginx
