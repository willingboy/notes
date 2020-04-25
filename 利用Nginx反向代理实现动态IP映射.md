# 利用Nginx反向代理实现动态IP映射

### 组网场景

| 设备位置           | IP地址    |                    | 系统环境 |
| ------------------ | --------- | ------------------ | -------- |
| 公网服务器有固定IP | 183.2.3.4 | ip固定不变         | centos8  |
| 树莓派             | 172.1.2.3 | ip由运营商动态分配 | debian10 |

### 服务器配置nginx

- 安装nginx

  ```
  yum install openssl openssl-devel
  mkdir ~/nginx
  cd ~/nginx
  wget http://nginx.org/download/nginx-1.18.0.tar.gz
  tar -zxvf nginx-1.18.0.tar.gz
  cd nginx-1.18.0
  ./configure --with-http_stub_status_module --with-http_ssl_module
  make
  make install
  ```

- 配置nginx文件

  ```nginx
  # /usr/local/nginx/conf/nginx.conf(先对文件进行备份)
  server {
          listen       443 ssl;
          server_name  XXX.com;
  
          ssl_certificate      XXX.pem;
          ssl_certificate_key  XXX.key;
  
          ssl_session_cache    shared:SSL:1m;
          ssl_session_timeout  5m;
  
          ssl_ciphers  HIGH:!aNULL:!MD5;
          ssl_prefer_server_ciphers  on;
  
          location / {
                  proxy_pass http://172.1.2.3:8000/;
          }
      }
  ```

- 创建一个监听服务并根据监听信息修改和重载nginx(server.py)

  ```python
  #!/usr/bin/python3
  import socket
  import time
  import re
  import os
  
  config_file = '/usr/local/nginx/conf/nginx.conf'
  
  
  def putIP(ip: str):
      '''
      修改nginx反向代理的配置并重新加载nginx
      :param ip:
      :return:
      '''
      file = open(config_file, "r")
      context = file.read()
      file.close()
      if ip not in context:
          pattern = re.compile(r'proxy_pass http://(\d[\d\.]+\d):8000/', re.I)
          context = pattern.sub(r'proxy_pass http://' + ip + ':8000/', context)
          with open(config_file, "w") as file:
              file.write(context)
          os.system(r"nginx -s reload")
          file = open("/root/xintiao.log", "a")
          localtime = time.asctime(time.localtime(time.time()))
          file.write(f'{localtime}: {ip}\n')
          file.close()
  
  def isLocalPort(port):
      '''
      通过tcp连接的方式探测端口是否被占用
      :param port:
      :return:如果是0表示被占用，如果非0表示端口不在用
      '''
      ip = '127.0.0.1'
      skt = socket.socket(family=socket.AF_INET, type=socket.SOCK_STREAM)
      flag = skt.connect_ex((ip, port))
      skt.close()
      return flag
  
  def getFromClientUDP():
      IP = '0.0.0.0'
      PORT = 5788
      text = b'heart is online'
      if isLocalPort(PORT):
          # 创建UDP嵌套字
          skt = socket.socket(family=socket.AF_INET, type=socket.SOCK_DGRAM)
          # 绑定端口
          skt.bind((IP, PORT))
          revc_data, addr = skt.recvfrom(1024)
          if revc_data == text:
              putIP(addr[0])
          skt.close()
  
  
  if __name__ == '__main__':
      getFromClientUDP()
  ```

- 创建每20分钟监听一次的定时任务

  ```
  crontab -e
  */20 * * * * /root/server.py
  ```

- 修改防火墙开放5788端口

### 树莓派配置

- 路由器端口映射

  我的是用的华为 SA1456C光猫路由合一设备：*登陆-转发规则-端口映射配置-新建*

  | 选项         | 配置            |
  | ------------ | --------------- |
  | 类型         | 用户            |
  | 使能端口映射 | True            |
  | 映射名称     | nginx(随便填写) |
  | 内部主机     | 树莓派局域网ip  |
  | 外部源IP地址 | 空白            |
  | 协议         | TCP             |
  | 内部端口号   | 8000-8000       |
  | 外部端口号   | 8000-8000       |
  | 外部源端口号 | 空白            |

- 发送UDP数据上报动态IP(client.py)

  ```python
  #!/usr/bin/python3
  import socket
  
  def sendToServerUDP():
      IP = '183.2.3.4'
      PORT = 5788
      text = b'heart is online'
      # 创建UDP嵌套字
      skt = socket.socket(family=socket.AF_INET, type=socket.SOCK_DGRAM)
      skt.sendto(text, (IP, PORT))
      skt.close()
  
  
  if __name__ == '__main__':
      sendToServerUDP()
  ```

- 创建每20分钟一次的定时任务

  ```
  crontab -e
  */20 * * * * /home/pi/client.py
  ```

- 这样就可以在本地树莓派启动端口为8000的服务

  ```
  https://xxx.com
  ```

### 备注

- 需要修改python文件为可执行

  ```
  chmod +x server.py
  chmod +x client.py
  ```
