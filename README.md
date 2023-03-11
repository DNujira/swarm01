## Ref (plex)
- https://github.com/docker/awesome-compose/tree/master/plex

## Wakatime swarm01
- https://wakatime.com/@spcn18/projects/tjlkinogtb

## URL (plex)
- http://nujiplex.xops.ipv9.me/

## สร้าง VM
- Ubuntu 22.04
- CPU 2 cores
- RAM 2 GB
- HDD 32 GB

**clone VM ออกมา 3 ตัว คือ**
- nuji-docker-manager-232
- nuji-docker-work1-233
- nuji-docker-work2-234

![image](https://user-images.githubusercontent.com/117592447/224509905-c9b6952a-936c-41e6-bb3c-72ce9e599404.png)

```
timedatectl set-timezone Asia/Bangkok   //เพื่อเปลี่ยน timezone เป็น Asia/Bangkok
```
```
hostnamectl set-hostname "ชื่อที่ต้องการ"   //เพื่อเปลี่ยน hostname ของ VM แต่ละตัว
```
**เปลี่ยน IP ของ VM เพื่อไม่ให้ซ้ำกัน**
```
cp /dev/null /etc/machine-id
rm /var/lib/dbus/machine-id
ln -s /etc/machine-id /var/lib/dbus/machine-id
init 0
```
## Install Docker
```
sudo -i   //เพื่อเข้าไปที่ root
```
**Install docker engine for Ubuntu**
```
apt update ; apt upgrade -y
apt-get install \
  ca-certificates \
  curl wget \
  gnupg \
lsb-release -y
         
mkdir -m 0755 -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
         
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) stable" |  tee /etc/apt/sources.list.d/docker.list > /dev/null
         
apt-get update
apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```
## Install Nala เพื่อใช้แทน apt (ติดตั้งหรือไม่ก็ได้)
```
wget https://gitlab.com/volian/nala/uploads/605d833bdffd23cee4bb6670b2d6c27b/nala_0.12.1_all.deb
dpkg -i nala_0.12.1_all.deb 
apt-get -f install -y  
```
```
tee -a /etc/apt/sources.list.d/nala-sources.list <<EOF 
# Sources file built for nala
deb https://mirror1.ku.ac.th/ubuntu/ jammy main restricted universe multiverse
deb https://mirrors.nipa.cloud/ubuntu/ jammy main restricted universe multiverse
deb https://mirror.kku.ac.th/ubuntu/ jammy main restricted universe multiverse
deb http://mirror1.totbb.net/ubuntu/ jammy main restricted universe multiverse
EOF
```
```
nala update  
nala upgrade -y 
nala list —upgradable
nala install htop dnsutils mtr -y
```
```
reboot
```

## Swarm init
```
docker swarm init   //เพื่อสร้าง token ของ swarm ในเครื่อง manager
```
**เมื่อได้ token มาให้นำไปรันในเครื่อง work1 และ work2**
```
sudo docker node ls   //เพื่อดูว่าเครื่องไหน join swarm แล้ว
```
![image](https://user-images.githubusercontent.com/117592447/224510395-0f0912de-0a5f-46a3-b15a-ea0c5a38e090.png)

**ติดตั้ง portainer สำหรับใช้กับ swarm (ติดตั้งที่ manager)**
