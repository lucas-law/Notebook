# 远程控制软件： Rustdesk 中继服务搭建指南

1. 在服务端安装docker-ce
2. 编辑docker-compose.yml

```
version: '3'

networks:
  rustdesk-net:
    external: false

services:
  hbbs:
    container_name: hbbs
    ports:
      - 21115:21115
      - 21116:21116
      - 21116:21116/udp
      - 21118:21118
    image: rustdesk/rustdesk-server:latest
    command: hbbs -r rustdesk.example.com:21117
    volumes:
      - ./data:/root
    networks:
      - rustdesk-net
    depends_on:
      - hbbr
    restart: unless-stopped

  hbbr:
    container_name: hbbr
    ports:
      - 21117:21117
      - 21119:21119
    image: rustdesk/rustdesk-server:latest
    command: hbbr
    volumes:
      - ./data:/root
    networks:
      - rustdesk-net
    restart: unless-stopped
```
3. 将 ```rustdesk.example.com```替换成你自己的域名
4. 执行 docker-compose up -d 启动docker
5. 启动后，在 data下面会生成一个id_ed25519.pub文件，cat id_ed25519.pub即可查看key
6. 将上面的key保存下来，在将防火墙的21115-21119 TCP端口放开，将21116的UDP端口放开。

至此，服务端就部署完成了，接下来下载桌面端（被控制端）
下载地址：https://github.com/rustdesk/rustdesk
下载好后，打开运行，点击ID的选项->ID/中继服务器，在ID服务器中填入<your domain>:21116, 在中继服务器填入<your domain>:21117, 在key栏填入上面保存到的key
  
点击确认后，如果下面的状态为就绪，则证明连接到中继服务器成功
  
在控制端操作上面同样的流程，输入被控端id和密码，即可正常控制了。





参考链接：https://github.com/rustdesk/rustdesk-server
