## 检查环境
### 查看系统版本
>uname -r
```
3.10.0-693.2.2.el7.x86_64
```
### 查看yum仓库配置
- 阿里云centos默认配置了阿里云的仓库地址
>more /etc/yum.repos.d/CentOS-Base.repo
```
[base]
name=CentOS-$releasever
enabled=1
failovermethod=priority
baseurl=http://mirrors.cloud.aliyuncs.com/centos/$releasever/os/$basearch/
gpgcheck=1
gpgkey=http://mirrors.cloud.aliyuncs.com/centos/RPM-GPG-KEY-CentOS-7

[updates]
name=CentOS-$releasever
enabled=1
failovermethod=priority
baseurl=http://mirrors.cloud.aliyuncs.com/centos/$releasever/updates/$basearch/
gpgcheck=1
gpgkey=http://mirrors.cloud.aliyuncs.com/centos/RPM-GPG-KEY-CentOS-7

[extras]
name=CentOS-$releasever
enabled=1
failovermethod=priority
baseurl=http://mirrors.cloud.aliyuncs.com/centos/$releasever/extras/$basearch/
gpgcheck=1
gpgkey=http://mirrors.cloud.aliyuncs.com/centos/RPM-GPG-KEY-CentOS-7
```
### 查看可装的docker列表

>yum list docker --showduplicates |sort -r
```
docker.x86_64              2:1.13.1-75.git8633870.el7.centos              extras
docker.x86_64              2:1.13.1-74.git6e3bb8e.el7.centos              extras
docker.x86_64              2:1.13.1-68.gitdded712.el7.centos              extras
docker.x86_64              2:1.13.1-63.git94f4240.el7.centos              extras
docker.x86_64              2:1.13.1-58.git87f2fab.el7.centos              extras
docker.x86_64              2:1.13.1-53.git774336d.el7.centos              extras
```

## 安装开始
### 安装docker，不选择版本默认安装最新版本
>yum install docker
```
Loading mirror speeds from cached hostfile
正在解决依赖关系
--> 正在检查事务
---> 软件包 docker.x86_64.2.1.13.1-75.git8633870.el7.centos 将被 安装
---> 软件包 docker-client.x86_64.2.1.13.1-75.git8633870.el7.centos 将被 安装
---> 软件包 docker-common.x86_64.2.1.13.1-75.git8633870.el7.centos 将被 安装
...
总下载量：36 M
Is this ok [y/d/N]: y #需要36M的空间，是否安装？选择是
...
已安装:
  docker.x86_64 2:1.13.1-75.git8633870.el7.centos

作为依赖被安装:
  PyYAML.x86_64 0:3.10-11.el7
  atomic-registries.x86_64 1:1.22.1-25.git5a342e3.el7.centos
  audit-libs-python.x86_64 0:2.8.1-3.el7_5.1
  checkpolicy.x86_64 0:2.5-6.el7
  container-selinux.noarch 2:2.68-1.el7
  container-storage-setup.noarch 0:0.11.0-2.git5eaf76c.el7
  device-mapper-event.x86_64 7:1.02.146-4.el7
  device-mapper-event-libs.x86_64 7:1.02.146-4.el7
  device-mapper-persistent-data.x86_64 0:0.7.3-3.el7
  docker-client.x86_64 2:1.13.1-75.git8633870.el7.centos
  docker-common.x86_64 2:1.13.1-75.git8633870.el7.centos
  libaio.x86_64 0:0.3.109-13.el7
  libcgroup.x86_64 0:0.41-15.el7
  libsemanage-python.x86_64 0:2.5-11.el7
  libyaml.x86_64 0:0.1.4-11.el7_0
  lvm2.x86_64 7:2.02.177-4.el7
  lvm2-libs.x86_64 7:2.02.177-4.el7
  oci-register-machine.x86_64 1:0-6.git2b44233.el7
  oci-systemd-hook.x86_64 1:0.1.17-2.git83283a0.el7
  oci-umount.x86_64 2:2.3.3-3.gite3c9055.el7
  policycoreutils-python.x86_64 0:2.5-22.el7
  python-IPy.noarch 0:0.75-6.el7
  python2-pytoml.noarch 0:0.1.18-1.el7
  setools-libs.x86_64 0:3.3.8-2.el7
  skopeo-containers.x86_64 1:0.1.31-1.dev.gitae64ff7.el7.centos
  subscription-manager-rhsm-certificates.x86_64 0:1.20.11-1.el7.centos
  yajl.x86_64 0:2.0.4-4.el7

作为依赖被升级:
  audit.x86_64 0:2.8.1-3.el7_5.1                             audit-libs.x86_64 0:2.8.1-3.el7_5.1
  device-mapper.x86_64 7:1.02.146-4.el7                      device-mapper-libs.x86_64 7:1.02.146-4.el7
  libselinux.x86_64 0:2.5-12.el7                             libselinux-python.x86_64 0:2.5-12.el7
  libselinux-utils.x86_64 0:2.5-12.el7                       libsemanage.x86_64 0:2.5-11.el7
  libsepol.x86_64 0:2.5-8.1.el7                              policycoreutils.x86_64 0:2.5-22.el7
  selinux-policy.noarch 0:3.13.1-192.el7_5.6                 selinux-policy-targeted.noarch 0:3.13.1-192.el7_5.6

完毕！
```
### 设置开机启动
>systemctl enable docker.service
```
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.
```
### 启动docker服务
>systemctl start docker
### 查看docker版本
>docker -v
```
Docker version 1.13.1, build 8633870/1.13.1
```
