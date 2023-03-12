# อธิบาย docker-compose.yml
- **version: '3.3'**

  - กำหนด version ของ compose ให้เป็น version 3.3

- **image: dnujira/plex:sw01**

  - กำหนด image ให้ชื่อว่า dnujira/plex:sw01 โดยเป็น image ที่เรามีอยู่ใน docker hub
  
- **networks: - webproxy**

  - กำหนด network ให้ container ชื่อ webproxy

- **logging: driver: json-file**

   - กำหนดว่าเราจะใช้ json-file ในการเก็บ log
   
-  **deploy: replicas: 1**

    - กำหนดให้ deploy แค่ 1 replicas
  
- **traefik.docker.network=webproxy**

  - กำหนดให้ traefik ใช้ network ที่ชื่อว่า webproxy

-  **traefik.http.routers.${APPNAME}-https.entrypoints=websecure**

    - กำหนดให้ traefik ใช้ entrypoints เป็น websecure โดยใน ${APPNAME} ตั้งชื่อเป็น nujiplex

-  **traefik.http.routers.${APPNAME}-https.rule=Host("${APPNAME}.xops.ipv9.me")**

    - กำหนดให้ traefik ใช้ routers "nujiplex.xops.ipv9.me" ในการ handle request

-  **traefik.http.routers.${APPNAME}-https.tls.certresolver=default**

    - กำหนดให้ traefik ใช้ certresolver ชื่อ default ในการดึง TLS โดยใน ${APPNAME} ตั้งชื่อเป็น nujiplex

-  **traefik.http.services.${APPNAME}.loadbalancer.server.port=32400**

    - กำหนดให้ traefik ใช้ port 32400 โดยใน ${APPNAME} ตั้งชื่อเป็น nujiplex

- **resources:<br>
       reservations:<br>
         cpus: '0.1'<br>
         memory: 10M <br>
       limits:<br>
         cpus: '0.4'<br>
         memory: 160M**<br>
    - กำหนด resource ขั้นต่ำ คือ
          
