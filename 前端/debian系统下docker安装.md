1、解决不能用ifconfig命令的问题

```
apt-get install net-tools
```

2、解决不能用curl命令的问题

```
apt-get install curl
```

3、安装docker需要添加软件源的 GPG 密钥

```
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/debian/gpg | apt-key add -
```

4、当安装软件源的 GPG 密钥的时候提示错误

```
E: gnupg, gnupg2 and gnupg1 do not seem to be installed, but one of them is required for this operation
```

可以通过安装gnupg2来解决

```
apt-get install -y gnupg2
```

5、向 source.list 中添加 Docker CE 软件源

```
add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/debian $(lsb_release -cs) stable"
```

6、当安装软件源提示错误

```
bash: add-apt-repository：未找到命令
```

可以通过安装properties来解决

```
apt-get install python-software-properties
apt-get install software-properties-common
```

7、安装docker

```
apt-get update
apt-get install docker-ce
```

8、解决下载镜像缓慢更换国内docker源

1. 创建一个镜像源配置文件

   ```
    /etc/docker/daemon.json
   ```

2. deamon.json文件内容

   ```
   {
   	"registry-mirrors": [
   		"http://hub-mirror.c.163.com"
   	]
   }
   ```

9、配置完成重启docker

```
systemctl restart docker
```

10、启动docker版本gitlab

```
docker run --detach --publish 8443:443 --publish 8090:80 --publish 8022:22 --hostname 10.10.120.162 --name gitlab --restart always -v /root/gitrom/gitlab/etc:/etc/gitlab -v /root/gitrom/gitlab/log:/var/log/gitlab -v /root/gitrom/gitlab/data:/var/opt/gitlab -v /etc/localtime:/etc/localtime:ro --privileged=true gitlab/gitlab-ce
```

