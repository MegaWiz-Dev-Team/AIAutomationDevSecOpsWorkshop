# AIAutomationDevSecOps Workshop Day 1: Environment Setup Guide

ยินดีต้อนรับสู่ Workshop Day 1! เอกสารนี้จะเป็นคู่มือแบบ Step-by-Step สำหรับการเตรียมสภาพแวดล้อม (Environment) ทั้งเครื่องมือการโจมตี (Attacker) และเครื่องเป้าหมาย (Vulnerable Targets) ครับ

---

## Step 1: Install Prerequisites (โปรแกรมพื้นฐาน)

### 1. Docker Desktop
เราจะใช้ Docker ในการรันเครื่องเป้าหมายอย่าง OWSAP Juice Shop และ Metasploitable 2 เพื่อให้ง่ายและไม่กินทรัพยากรเครื่องมากเกินไป
1. ดาวน์โหลดและติดตั้ง: [Docker Desktop](https://www.docker.com/products/docker-desktop/)
2. **การตรวจสอบ:** เปิด Terminal / Command Prompt และพิมพ์คำสั่ง
   ```bash
   docker --version
   ```

### 2. Git
ใช้สำหรับดาวน์โหลดโค้ดและ Repository ต่าง ๆ เช่น MCP Servers
1. ดาวน์โหลดและติดตั้ง: [Git](https://git-scm.com/downloads)
2. **การตรวจสอบ:** พิมพ์คำสั่ง
   ```bash
   git --version
   ```

---

## Step 2: Set up the Attacker Machine (Kali Linux)

เครื่องมือหลักที่เราจะใช้ในการแฮ็กหรือทดสอบระบบคือ Kali Linux สามารถเลือกติดตั้งได้ 2 รูปแบบ:

**Option A: Virtual Machine (แนะนำ - ได้ GUI และครบเครื่อง)**
1. ติดตั้ง VM Player เช่น [VirtualBox](https://www.virtualbox.org/) หรือ VMware
2. ดาวน์โหลด Kali Linux แบบ Pre-built VM (เลือกให้ตรงกับ VM Player): [Kali Linux VMs](https://www.kali.org/get-kali/#kali-virtual-machines)
3. Import ไฟล์เข้าสู่โปรแกรมและกดเปิดเครื่อง (Username/Password เริ่มต้นคือ: `kali` / `kali`)

**Option B: Docker (สำหรับคนที่ต้องการแค่ Command Line)**
```bash
docker pull kalilinux/kali-rolling
docker run -ti kalilinux/kali-rolling /bin/bash
```
*(หมายเหตุ: หากใช้ Docker คุณจะต้องติดตั้งเครื่องมือเอง เช่น `apt update && apt install -y metasploit-framework nmap curl`)*

---

## Step 3: Deploy Vulnerable Targets (เครื่องเป้าหมาย)

เราจะเปิดระบบที่มีช่องโหว่เพื่อให้เราทดลองเจาะระบบได้จำลอง

### 1. OWASP Juice Shop (เป้าหมายประเภท Web Application)
Juice Shop เป็นเว็บแอปพลิเคชันสมัยใหม่ที่จงใจใส่ช่องโหว่ (OWASP Top 10) ไว้ให้เราได้ฝึกฝน
- **เปิด Terminal และรันคำสั่ง:**
  ```bash
  docker run --rm -p 3000:3000 bkimminich/juice-shop
  ```
- **การเข้าใช้งาน:** เปิดเบราว์เซอร์ไปที่ http://localhost:3000

### 2. Metasploitable 2 (เป้าหมายประเภท Infrastructure / Network)
เป็น Linux Server ที่เต็มไปด้วยบริการที่มีช่องโหว่ (เช่น FTP, SSH เก่า ๆ ฯลฯ)
- **ดาวน์โหลดรูปแบบ VM (ดั้งเดิม):** [Rapid7 Metasploitable 2](https://www.rapid7.com/products/metasploit/metasploitable/)
- **ทางเลือกในการรันด้วย Docker:**
  ```bash
  docker run -it --rm -p 21:21 -p 80:80 -p 445:445 tleemcjr/metasploitable2:latest
  ```
*(คำเตือน: โปรดรันเครื่องมือเหล่านี้ในสภาพแวดล้อมจำลอง (Local/VM Network) ที่ปลอดภัยและไม่ต่อกับอินเทอร์เน็ตสาธารณะ)*

---

## Step 4: Clone MCP Servers (สำหรับระบบ Monitoring & AI)

เราจะเตรียมดาวน์โหลด Model Context Protocol (MCP) Servers ที่เกี่ยวข้องเอาไว้ในเครื่อง

เปิด Terminal, สร้างโฟลเดอร์สำหรับ Workshop และรันคำสั่งเหล่านี้:

**1. Wazuh MCP Server**
```bash
git clone https://github.com/gensecaihq/Wazuh-MCP-Server.git
```

**2. Zabbix MCP Server**
```bash
git clone https://github.com/mpeirone/zabbix-mcp-server.git
```

---

## Useful Resources (แหล่งข้อมูลเพิ่มเติมสำหรับ Workshop)
- **OWASP Top 10 (2025):** รายชื่อช่องโหว่ที่อันตรายและพบบ่อยที่สุดในปีล่าสุด [https://owasp.org/Top10/2025/](https://owasp.org/Top10/2025/)
- **VirusTotal:** เครื่องมือวิเคราะห์มัลแวร์, ไอพี, หรือโดเมนที่น่าสงสัย [https://www.virustotal.com/](https://www.virustotal.com/)
