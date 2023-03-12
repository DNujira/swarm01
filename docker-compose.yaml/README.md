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

  - กำหนดให้ traefik ใช้ entrypoints เป็น websecure
