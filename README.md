## 鸣谢

- [zonemeen/musicn](https://github.com/zonemeen/musicn)

## 概述

可播放及下载音乐的命令行工具 [musicn](https://github.com/zonemeen/musicn) 的容器版本，支持 amd64/arm64 架构。

目前已不能下载无损格式。

![image](https://user-images.githubusercontent.com/98247050/230909773-52d95ba7-e42e-4612-86dd-7cb363bc3f2f.png)


## 部署

  命令行 + Web 模式
  ```
  docker run -d --name=musicn --restart=unless-stopped -v ${PWD}/musicn:/data -p 7478:7478 ghcr.io/wy580477/musicn-container:latest msc -q
  ```
  仅命令行模式 （空闲时几乎不耗内存）
  ```
  docker run -d --name=musicn --restart=unless-stopped -v ${PWD}/musicn:/data ghcr.io/wy580477/musicn-container:latest tail -f
  ```
  ${PWD}/musicn 为命令行模式下载文件存放目录，默认当前目录下 musicn 文件夹。
  
  执行 musicn 命令:

  ```
  docker exec -it musicn msc 周杰伦
  
  # 设置 bash 命令别名方便使用，重新登陆 shell 后生效
  echo "alias msc='docker exec -it musicn msc'" >> ~/.bashrc
  
  # 查看命令帮助
  msc -h
  
  # 指定子目录 test 为下载目录
  msc 周杰伦 -p ./test
  
  # 升级版本
  docker container rm musicn --force && docker pull ghcr.io/wy580477/musicn-container:latest
  # 然后重新执行安装命令
  
  # 容器内升级 （不推荐，万一我以后弃坑不更新 image 版本，可以用这个方法更新）
  docker exec -it musicn npm i musicn -g
  ```
  更多命令用法详见： [musicn 文档](https://github.com/zonemeen/musicn#%E6%90%9C%E7%B4%A2%E7%9A%84%E9%A1%B5%E7%A0%81%E6%95%B0%E9%BB%98%E8%AE%A4%E6%98%AF%E7%AC%AC1%E9%A1%B5)
  
  访问 http://<宿主机 ip>:7478 可到达 Web 搜索和下载界面。(扫码访问功能不可用, 请手动输入网址访问)
  
  ![image](https://user-images.githubusercontent.com/98247050/230908384-99c5d283-26f6-4a9b-aa9f-104ccf7e4702.png)
  
### Docker-Compose 部署
  命令行 + Web 模式
```
version: '3.4'
services:

  musicn:
    image: ghcr.io/wy580477/musicn-container:latest
    container_name: musicn
    restart: unless-stopped
    entrypoint: ["/sbin/tini", "--", "msc", "-q"]
    ports:
      - "7478:7478"
    volumes:
      - ./musicn:/data
 ``` 
   
仅命令行模式
```
version: '3.4'
services:

  musicn:
    image: ghcr.io/wy580477/musicn-container:latest
    container_name: musicn
    restart: unless-stopped
    entrypoint: ["/sbin/tini", "--", "tail", "-f"]
    volumes:
      - ./musicn:/data
 ``` 
  
sudo kubectl port-forward --address 0.0.0.0 -n default service/musicn-container 7478:7478 &

<img width="1384" alt="image" src="https://github.com/gmicroul/musicn-container/assets/121873438/ac7461ba-eb96-4867-91fa-a593cb8a2f5b">
