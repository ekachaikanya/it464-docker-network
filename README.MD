# 🐳 Docker Compose Network & Port Configuration

โปรเจกต์นี้เป็นการตั้งค่าระบบแบบ Multi-tier ที่แบ่งแยก Network เพื่อความปลอดภัย และแสดงผลผ่าน Portainer Dashboard

## 📄 1. ไฟล์ Docker Compose (`docker-compose.yml`)

```yaml
services:
  frontend:
    image: nginx:alpine
    container_name: web-frontend
    ports:
      - "8086:80"
    networks:
      - frontend-net
      - shared-net

  backend:
    image: cloudnativelabs/whats-my-ip
    container_name: api-backend
    ports:
      - "3001:8080"
    networks:
      - backend-net
      - shared-net

  database:
    image: postgres:alpine
    container_name: db-service
    ports:
      - "5433:5432"
    environment:
      POSTGRES_PASSWORD: password
    networks:
      - backend-net

  portainer:
    image: portainer/portainer-ce:latest
    container_name: docker-manager
    ports:
      - "9000:9000"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - portainer_data:/data
    networks:
      - frontend-net
      - shared-net
      - backend-net

volumes:
  portainer_data:

networks:
  frontend-net:
  backend-net:
  shared-net:
    driver: bridge
```

## 🚀 2. ขั้นตอนการเตรียมเครื่องและรัน (Execution Guide)
###  ✅ ขั้นเตรียมการ (Prerequisites)
ติดตั้ง Docker Desktop (สำหรับ Windows/Mac) หรือ Docker Engine (สำหรับ Linux)
ตรวจสอบเวอร์ชันด้วยคำสั่ง docker compose version
###   🛠️ คำสั่งที่ใช้รัน
ให้เปิด Terminal ในโฟลเดอร์ที่เก็บไฟล์ไว้ แล้วพิมพ์คำสั่งดังนี้:
### เริ่มการทำงาน:
```bash
docker compose up -d
```

###  ตรวจสอบสถานะ Container:
```bash
docker compose ps
```

### ตรวจสอบ Network ที่ถูกสร้างขึ้น:
```bash
docker network ls
```

## 🌐 3. การเข้าใช้งาน (Access URLs)
### Service	URL	คำอธิบาย
* Frontend	http://localhost:8086	หน้าเว็บหลัก (Nginx)
* Backend API	http://localhost:3001	เช็คสถานะ API และ 
IP ภายใน
* Portainer	http://localhost:9000	Dashboard สำหรับดู Network และ Container

## ⚙️ 4. คำสั่งจัดการเพิ่มเติม (Maintenance)
* หยุดการทำงานชั่วคราว: 
 ```bash
docker compose stop
 ```
* เริ่มการทำงานใหม่: 
```bash 
docker compose start
```

* ลบระบบทิ้งทั้งหมด (รวม Network): 
 ```bash 
docker compose down
```

* ดู Log การทำงาน: 
```bash  
docker compose logs -f
```

#### คำแนะนำด้านความปลอดภัย: สำหรับพอร์ต 5433 ของ Database หากไม่ได้ใช้ต่อจากภายนอก แนะนำให้เอาส่วน ports ออก เพื่อให้เข้าถึงได้เฉพาะจาก backend-net เท่านั้น ตามหลักการ Docker Networking Best Practices