# github actions构建docker镜像并推送到docker hub
#### 推送到docker hub要设置三个actions secrets参数

```bash
#docker hub 仓库名(用户名/仓库名)，比如：user/ubuntu-vnc
DOCKER_REPO 

#docker hub 用户名
DOCKER_USERNAME

#docker hub 密码
DOCKER_PASSWORD
```


### 镜像TAG:(tag名可以在main.yml里更改)

vnc18lxqt #ubuntu18.04-sshd-lxqt

vnc18lxde #ubuntu18.04-sshd-lxde

vnc20lxqt #ubuntu20.04-sshd-lxqt

vnc20lxde #ubuntu20.04-sshd-lxde



### 环境（默认值）

```bash
#用户
USER=ubuntu

#密码
PASSWORD=ubuntu

#VNC登陆密码
VNC_PW=ubuntu

#VNC分辨率
VNC_RESOLUTION=1024x768

#VNC端口
VNC_PORT=5900

#SSH登陆密钥(不需密码)，如果不设置，则只能用密码(PASSWORD)登陆，SSH端口为22。
SSH_KEY=**None**
```
docker run -d \
  --name=ubuntu-vnc \
  -e USER=ubuntu \
  -e VNC_PW=ubuntu \
  -e VNC_RESOLUTION=1024x768 \
  -e VNC_PORT=5900 \
  -p 3389:5900 `#rdp` \
  -p 2222:22 `#ssh` \
  --security-opt seccomp=unconfined \
  --restart unless-stopped \
  tvision08/ubuntu-sshd-vnc:vnc20lxde

### 注意，部署并运行镜像后：
#### ubuntu 18.04 默认安装chromium，ubuntu 20.04 默认安装chrome（20.04只能用snap安装chromium，但docker里运行不了snap）
#### 时区为Asia/Shanghai，vnc18lxqt/vnc20lxde默认中文环境
