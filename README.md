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
```
curl -L https://downloads.portainer.io/ce2-17/portainer-agent-stack.yml -o portainer-agent-stack.yml
docker stack deploy -c portainer-agent-stack.yml portainer
```

## Install Traefik

**สร้าง floder ชื่อ Traefik และไฟล์ ชื่อ traefik-host.yml**
**ข้อมูลในไฟล์ traefik-host.yml อ้างอิงมาจาก**

https://github.com/pitimon/dockerswarm-inhoure/blob/main/ep03-traefik/traefik-host.yml

![image](https://user-images.githubusercontent.com/117592447/224510594-fcb77b25-1731-4808-ad11-8a73811a9ef5.png)

**Create a network**
```
docker network create --driver=overlay traefik-public
```
**Create tag**
```
export NODE_ID=$(docker info -f '{{.Swarm.NodeID}}')
echo $NODE_ID
```
```
docker node update --label-add traefik-public.traefik-public-certificates=true $NODE_ID
```
**เข้าไปที่ C:\Windows\System32\drivers\etc เพื่อเพิ่ม domain ที่ต้องการในไฟล์ hosts**
![image](https://user-images.githubusercontent.com/117592447/224511039-cf28160c-9e6f-4a2e-8771-540e0a618d6f.png)

![image](https://user-images.githubusercontent.com/117592447/224511173-fcae8792-8765-4e54-a1ed-085d2e3d95e3.png)


**เปลี่ยน email,domain, Username, Password ได้ (domain ต้องตรงกับในไฟล์ host ที่เพิ่มเข้าไป)**
```
export EMAIL=user@smtp.com
export DOMAIN=traefik.cpedemo.local
export USERNAME=admin
export PASSWORD=changeMe
export HASHED_PASSWORD=$(openssl passwd -apr1 $PASSWORD)
echo $HASHED_PASSWORD
```

**Deploy traefik stack**
```
docker stack deploy -c traefik-host.yml traefik
```

**ทดลองเข้าใน traefik ว่าสามารถใช้งานได้หรือไม่ โดยเข้าผ่าน traefik.cpedemo.local**

![image](https://user-images.githubusercontent.com/117592447/224511797-661b23fe-52de-4de1-bc48-1695ab33018c.png)

## Install Swarmpit
**สร้าง floder ชื่อ swarmpit และไฟล์ ชื่อ swarmpit.yml**
**ข้อมูลในไฟล์ swarmpit.yml อ้างอิงมาจาก**

https://github.com/pitimon/dockerswarm-inhoure/blob/main/ep04-swarmpit/swarmpit.yml

![image](https://user-images.githubusercontent.com/117592447/224511707-e2357316-7452-4ead-aa64-4fff8e4aa9aa.png)


**กำหนด domain ที่ต้องการ**
```
export DOMAIN=swarmpit.cpedemo.local
```
**เพิ่ม domain ในไฟล์ hosts ที่ path C:\Windows\System32\drivers\etc**

![image](https://user-images.githubusercontent.com/117592447/224511490-b602a353-faad-47b9-b5bf-871cceb5bdcc.png)

**Create label ใน node นี้ เพื่อใช้งานฐานข้อมูล CouchDB**
```
export NODE_ID=$(docker info -f '{{.Swarm.NodeID}}')
docker node update --label-add swarmpit.db-data=true $NODE_ID
```
**Create label ใน node นี้ เพื่อใช้งานฐานข้อมูล Influx**
```
export NODE_ID=$(docker info -f '{{.Swarm.NodeID}}')
docker node update --label-add swarmpit.influx-data=true $NODE_ID
```
**Deploy Swarmpit stack**
```
docker stack deploy -c swarmpit.yml swarmpit
```
**เช็ค stack ได้โดย**
```
docker stack ps swarmpit
docker service logs swarmpit_app
```
**ทดลองเข้าใน swarmpit ว่าสามารถใช้งานได้หรือไม่ โดยเข้าผ่าน swarmpit.cpedemo.local**

![image](https://user-images.githubusercontent.com/117592447/224511779-bed491fb-8759-4588-9061-dcf2d5c25cd1.png)
