# อธิบาย compose.yml

- **image: linuxserver/plex** 

  - เป็นการกำหนด image ของ Docker ซึ่งเป็น image ที่ใช้กับ plex
- **container_name: plex**

  - กำหนดชื่อ container ให้ชื่อว่า plex
- **network_mode: host**

  - ระบุชนิด network ของ service ให้เป็น host
- **environment:- VERSION=docker--**

  - กำหนด environment variable และกำหนดค่า version เพื่อให้รู้ว่าจะใช้ version ไหน
- **restart: always**

  - เป็นการกำหนด service ให้ restart ตลอดเมื่อมีข้อผิดพลาด หรือสั่งให้ start เมื่อเปิดเครื่องขึ้นมาใหม่
- **volumes:- ${PLEX_MEDIA_PATH}:/media/**

  - volume นี้ อยู่ใน service จีงเป็นการเชื่อม volume กับ service
