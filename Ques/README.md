# 以下是我遇到的一些问题

## 1. 安装ii时遇到问题？
![image](https://github.com/user-attachments/assets/826714f6-242b-4161-8553-917b5b655eb0)
* 解决办法：
```
$ ps -e|grep apt-get
#显示
# 6965 ?        00:00:01 apt-get
# 然后执行
$ sudo kill 6965
# 强制解锁
sudo rm /var/cache/apt/archives/lock  
sudo rm /var/lib/dpkg/lock
#之后再安装
apt install ii
```
* 问题解决：
  
  ![image](https://github.com/user-attachments/assets/3e3a3df3-939c-4206-aaef-4d0980c42adb)
## 2. 出现docker-ce和docker-ce-cli版本不一致时
![image](https://github.com/user-attachments/assets/920ff68a-632d-472c-bc87-f76af382e36d)

* 解决办法：
```
1.卸载旧版本Docker
#卸载旧版本docker
sudo apt-get remove docker docker-engine docker-ce docker.io    

#清空旧版docker占用的内存
sudo apt-get remove --auto-remove docker

#更新系统源
sudo apt-get update

2.配置安装环境
sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
3.添加阿里云的docker GPG密钥
curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
4. 添加阿里镜像源,这是amd架构
sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"

sudo apt-get update
sudo apt-get install docker-ce=5:19.03.6~3-0~ubuntu-bionic
sudo apt-get install docker-ce-cli=5:19.03.6~3-0~ubuntu-bionic
#更新
sudo apt-get update
5.查看有哪些版本
apt-cache madison docker-ce
6.安装最新版/指定版本
#安装最新版
sudo apt-get install -y docker-ce
#安装5:19.03.6~3-0~ubuntu-bionic版
sudo apt-get install -y docker-ce=5:19.03.6~3-0~ubuntu-bionic
7.安装docker-ce-cli也是一样
apt-cache madison docker-ce-cli
sudo apt-get install -y docker-ce-cli=5:19.03.6~3-0~ubuntu-bionic
docker version
```
* 问题解决：

![image](https://github.com/user-attachments/assets/bab0e354-7c7f-4128-acdb-d9aac5a3db37)

## 3. 启动GNB时，出错？

![image](https://github.com/user-attachments/assets/a85931fc-2c99-43b0-b97b-c39694061589)

* 解决方法：
```
# 扫描端口
netstat -tulpn
找到4997的，然后杀掉
kill -9 id
再去build，就可以抓包了
```

## 4. 启动UE时，PDU会话建立失败？
![image](https://github.com/user-attachments/assets/81507fee-b7bb-46eb-b6b2-9af014e53777)

* 解决方法：

进入 UERANSIM 中的 ue，修改APN ----> DNN

![image](https://github.com/user-attachments/assets/94e6354b-58ab-4af9-ab83-ca3fb134dffb)











