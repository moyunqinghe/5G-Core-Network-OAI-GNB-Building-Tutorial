# 声明

**以下内容仅供参考**

* 安装方式分为两种：
  
  * OAI + GNB 安装到一台物理机
  * OAI + GNB 安装到两台物理机

# 硬件信息
* Ubuntu： 18.04

# 安装过程
```
git clone https://gitlab.eurecom.fr/oai/cn5g/oai-cn5g-fed.git
```
然后需要安装docker,这里注意``docker-ce和docker-ce-cli``版本需要保持一致
```
# 安装5:19.03.6~3-0~ubuntu-bionic版
sudo apt-get install -y docker-ce=5:19.03.6~3-0~ubuntu-bionic
# 安装docker-ce-cli也是一样
sudo apt-get install -y docker-ce-cli=5:19.03.6~3-0~ubuntu-bionic
```
![image](https://github.com/user-attachments/assets/5f26d014-2513-40db-ba02-232dbcdaab49)

之后安装docker-compose
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```
![image](https://github.com/user-attachments/assets/e4e357b3-4c65-4977-887d-1d2a5fcd14ce)

之后再添加用户到docker 
```
# xin 是我的linux用户，自行更换就好了
sudo usermod -a -G docker xin
```
之后再登录docker
```
# 输入自己账户
docker -hub 
zzhdyyds
qq12xxxxx
# 测试说明docker安装ok
docker run hello-world
```
![image](https://github.com/user-attachments/assets/387dc619-cfa9-4d98-9c85-61c68fb0cb7f)

之后把镜像拉到docker
```
docker pull ubuntu:bionic
docker pull mysql:8.0
# 检测镜像是否ok
docker images 
```
![image](https://github.com/user-attachments/assets/4c9288f2-4c37-4703-a9e4-f3f0c4f46f4e)

之后再配置ipv4
```
sudo sysctl net.ipv4.conf.all.forwarding=1
sudo iptables -P FORWARD ACCEPT
```
![image](https://github.com/user-attachments/assets/3cf8c331-23d8-45a5-9e8a-5444b8f99992)

之后拉镜像
```
docker pull oaisoftwarealliance/oai-amf:v1.5.0 
docker pull oaisoftwarealliance/oai-nrf:v1.5.0
docker pull oaisoftwarealliance/oai-spgwu-tiny:v1.5.0  
docker pull oaisoftwarealliance/oai-smf:v1.5.0  
docker pull oaisoftwarealliance/oai-udr:v1.5.0
docker pull oaisoftwarealliance/oai-udm:v1.5.0
docker pull oaisoftwarealliance/oai-ausf:v1.5.0
docker pull oaisoftwarealliance/oai-upf-vpp:v1.5.0
docker pull oaisoftwarealliance/oai-nssf:v1.5.0
docker pull oaisoftwarealliance/oai-pcf:v1.5.0
docker pull oaisoftwarealliance/oai-nef:v1.5.0
docker pull oaisoftwarealliance/trf-gen-cn5g:latest 
```
最后使用 docker images 查看镜像，我这里没拉完，中断了
![image](https://github.com/user-attachments/assets/513ad299-cdb5-40be-b207-b91c437dbdbc)

创建网桥
```
docker network create --driver=bridge --subnet=192.168.70.128/26 -o "com.docker.network.bridge.nadelme"="demo-oai" demo-oai-public-net
```
![image](https://github.com/user-attachments/assets/e02cf0cc-f007-4347-9847-bc5547de940e)

看现在是否有 demo-oi
```
ifconfig
```
![image](https://github.com/user-attachments/assets/dc340fc3-9430-4e10-ad66-990c6e1ead26)

**启动核心网**
```
python3 core-network.py --type start-basic
```
![image](https://github.com/user-attachments/assets/bb6a9b3f-d5cf-4efe-b476-624afac5e863)

**暂停核心网**
```
python3 core-network.py --type stop-basic
```
_OK，到这里核心网就安装完了，下面开始UERANSIM_

# UERANSIM安装
_我这里把GNB和OAI安装到一个平台了_

先安装make  
```
sudo snap install cmake --classic
```
安装 UERANSIM
```
git clone https://github.com/aligungr/UERANSIM
```
安装依赖
```
sudo apt update 
sudo apt upgrade
sudo apt install make
sudo apt install g++
sudo apt install libsctp-dev
sudo apt install lksctp-tools
sudo apt install iproute2
```
然后make，时间有点长

![image](https://github.com/user-attachments/assets/5be7654e-94da-4cb8-9240-57e516181875)

ok

![image](https://github.com/user-attachments/assets/beec7808-e3dd-45a7-a9c9-42ff8f38cd97)

进入 config/open5gc-gnb
```
cd config/open5gc-gnb
```
对open5gc-gnb-yaml文件进行配置
```
vim open5gs-gnb-yaml
```
对ngapIp、tgpIp进行修改，如果OAI和GNB安装到了同一个机器，改为我这个

![image](https://github.com/user-attachments/assets/b2026c36-5e20-43a0-bc9c-11906bc4efaa)

之后运行这个文件
```
build/nr-gnb -c config/open5gc-gnb-yaml
```
![image](https://github.com/user-attachments/assets/da7e422f-b050-47bb-b5f2-85925c8557bc)

运行wireshark抓包工具，进行抓包

![image](https://github.com/user-attachments/assets/5e134162-a55a-46b7-b061-5a796910bb03)

**OK ，我们的GNB已经接入核心网中，下面开始UE接入**

对UE信息进行配置
```
vim open5gs-ue-yaml
```

主要修改key、opc、mcc、mnc、supi

**注意IMSI一定要已经在核心网中完成注册**

![image](https://github.com/user-attachments/assets/5c8cd251-b726-4ce7-9bdb-a725aa9c971e)

## 在核心网中查看UE是否已经注册方法
```
docker exec -it mysql /bin/bash
mysql -uroot -plinux -D oai_db
select * from AuthenticationSubscription;
```

核对IMSI码值即可

若未注册，按照以下方法进行注册
```
INSERT INTO `AuthenticationSubscription` (`ueid`, `authenticationMethod`, `encPermanentKey`, `protectionParameterId`, `sequenceNumber`, `authenticationManagementField`, `algorithmId`, `encOpcKey`, `encTopcKey`, `vectorGenerationInHss`, `n5gcAuthMethod`, `rgAuthenticationInd`, `supi`) VALUES ('001010000000001', '5G_AKA', '0C0A34601D4F07677303652C0462535B', '0C0A34601D4F07677303652C0462535B', '{\"sqn\": \"000000000020\", \"sqnScheme\": \"NON_TIME_BASED\", \"lastIndexes\": {\"ausf\": 0}}', '8000', 'milenage', '63bfa50ee6523365ff14c1f45f88737d', NULL, NULL, NULL, NULL, '001010000000001');
```

注册完成后，就可以启动UE了
```
build/nr-ue -c config/open5gs-ue.yaml
```
![image](https://github.com/user-attachments/assets/f2fea0dc-1c1e-48d7-a120-f7250b01e6d3)

![image](https://github.com/user-attachments/assets/95b5824e-f2af-495b-a438-926825bae4d0)

可以看到这里核心网也为我们分配了用户IP

**OK，UE接入也搞定了**














































