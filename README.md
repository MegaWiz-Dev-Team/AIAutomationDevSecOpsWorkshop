# AIAutomationDevSecOps Workshop: Outline & Environment Setup

## 📅 Workshop Outline (Agenda - 3 Days, 1 - 1.5 Hours/Day)

เพื่อให้สอดคล้องกับเวลา 1-1.5 ชั่วโมงต่อวัน เนื้อหาของ Workshop จะถูกแบ่งออกเป็น 3 วัน เพื่อให้ผู้เข้าร่วมได้เรียนรู้และลงมือทำอย่างค่อยเป็นค่อยไปครับ

---

### **Day 1: Foundations & Dev Automation Setup (ปูพื้นฐานและวางระบบอัตโนมัติ)**
*ระยะเวลาที่ใช้: 1 - 1.5 ชั่วโมง*
*   **[ 20 นาที ]** แนะนำหลักสูตร AIAutomationDevSecOps และการรับมือภัยคุกคามด้วยหลักการ **OWASP Top 10 (2025)**
*   **[ 30 นาที ]** Dev Automation & Lab Setup: การเตรียมเครื่องมือและการ Deploy แบบอัตโนมัติ (Automated Provisioning)
    *   การตั้งค่ากระบวนการ **Dev Automation** (การใช้สคริปต์/Docker Compose/AI Tools ในการจัดการ Environment)
    *   การดีพลอยเครื่องเป้าหมายจำลอง (**LLMGoat**)
*   **[ 20 นาที ]** Attacker Environment & Threat Intelligence
    *   ติดตั้งและปรับแต่งเครื่องโจมตี **Kali Linux** (ผ่าน Docker Desktop)
    *   สาธิต Crash Course เบื้องต้น: วิเคราะห์ภัยคุกคามด้วย **VirusTotal**
*   **[ 10 นาที ]** ถาม-ตอบ และตรวจสอบความพร้อม (Verify Systems) ให้ผู้เรียนทุกคนพร้อมสำหรับวันต่อไป

---

### **Day 2: Red Team Operations (ปฏิบัติการเจาะระบบแบบ Whitebox & Blackbox)**
*ระยะเวลาที่ใช้: 1 - 1.5 ชั่วโมง*
*   **[ 30 นาที ]** AI Blackbox & Whitebox Pentest: 
    *   เรียนรู้ความแตกต่างระหว่างการแฮ็กแบบไม่รู้โครงสร้าง (Blackbox) กับการตรวจโค้ดระบบ AI (Whitebox)
    *   วิเคราะห์การทำงานของ LLM และทำความเข้าใจ Architecture ของ **LLMGoat**
*   **[ 30 นาที ]** Hands-On AI Security Offensive: 
    *   ลงมือเจาะระบบและหลอกลวง **AI Agent** (โปรเจกต์ LLMGoat) อ้างอิง OWASP Top 10 for LLMs
    *   ฝึกหาช่องโหว่แบบ Prompt Injection, Data Leakage และ Insecure Output Handling
*   **[ 20 นาที ]** สรุปข้อมูลช่องโหว่: รวบรวมหลักฐานและข้อมูลที่ตรวจเจอทั้งหมดในวันนี้ (Artifacts) เตรียมส่งต่อให้ทีมป้องกันเซสชั่นหน้า

---

### **Day 3: Blue Team Operations (Report, Monitor & Audit)**
*ระยะเวลาที่ใช้: 1 - 1.5 ชั่วโมง*
*   **[ 30 นาที ]** สร้างผู้ตรวจการ AI (Build AI Monitor Agent):
    *   ติดตั้งและปรับแต่ง **Wazuh-MCP-Server** (Security Monitor) 
    *   เชื่อมต่อ Agent เข้ากับ **LLMGoat** เพื่อดักจับพฤติกรรมที่น่าสงสัยตามมาตรฐาน OWASP Top 10 for LLMs
*   **[ 30 นาที ]** Monitor & Audit (ตรวจสอบทราฟฟิกและพฤติกรรม LLM):
    *   ตรวจสอบประวัติทราฟฟิก (Log & Event) จากการโจมตี Prompt Injection และ Data Leakage เมื่อวาน
    *   ใช้ AI วิเคราะห์และ Audit การตัดสินใจของ LLMGoat ว่าถูกหลอกลวงสำเร็จในจุดใดบ้าง
*   **[ 20 นาที ]** Automated Reporting & Wrap-up:
    *   ประยุกต์ทำรายงานสรุปช่องโหว่ (Vulnerability Report) ด้วย AI จากข้อมูล Red Team 
    *   ถาม-ตอบ Q&A และสรุปแนวทางการประยุกต์ใช้ AIAutomationDevSecOps ในการทำงานจริง

---

## 🛠️ Environment Setup Guide (คู่มือเตรียมความพร้อม)

### 1. Prerequisites (โปรแกรมพื้นฐาน)
*   **Code Editor & AI Assistant:** แนะนำ VS Code พร้อมเสริมด้วย **Antigravity** และใช้งานร่วมกับ **Opencode CLI** เพื่อการใช้ AI ช่วยทำงานให้ครอบคลุมทั้งหน้า UI และหน้าต่าง Terminal
*   **LLM API (โมเดล AI):** คลาสนี้จะใช้ **GLM (Zhipu AI)** เป็นสมองหลัก ผู้เรียนจำเป็นต้องเปิดบัญชีเพื่อรับ API Key ฟรีที่ [BigModel Platform](https://open.bigmodel.cn/) ก่อนเริ่มคลาส
*   **Docker Desktop:** ใช้สำหรับรันเซอร์วิสเป้าหมายและจำลองระบบ [ดาวน์โหลด Docker Desktop](https://www.docker.com/products/docker-desktop/)
*   **Git:** ใช้สำหรับดาวน์โหลดไฟล์โปรเจกต์ [ดาวน์โหลด Git](https://git-scm.com/downloads)

### 2. Set up AI Automation (Opencode CLI & GLM)
ตั้งค่าให้ Opencode CLI ทำงานร่วมกับ โมเดล Zhipu AI (GLM) โดยเปิด Terminal แล้วรันคำสั่ง:
```bash
# ติดตั้ง Opencode CLI (กรณีใช้ Node.js)
npm install -g opencode-cli

# ตั้งค่าให้ Opencode เชื่อมต่อกับ Zhipu AI (GLM-4)
opencode config set provider zhipu
opencode config set api_key "ใส่_API_KEY_ที่ได้จาก_BigModel_ที่นี่"
```

### 3. Set up the Attacker Machine (Kali Linux)
รัน Kali Linux ผ่าน Docker Desktop แบบ Background (เพื่อสามารถกลับมาใช้งานซ้ำได้ในวันต่อๆ ไป):
```bash
# 1. สร้างและรัน Kali Container
docker run --name attacker_kali -itd kalilinux/kali-rolling

# 2. เข้าสู่ Shell ของ Kali Linux
docker exec -it attacker_kali /bin/bash

# 3. อัปเดตและติดตั้งเครื่องมือเจาะระบบพื้นฐาน (รันภายในหน้าจอ Kali)
apt update && apt install -y kali-linux-default curl jq
```

### 4. Deploy Vulnerable Targets (เครื่องเป้าหมาย)

**เป้าหมาย: LLMGoat (Vulnerable AI Agent — patched ให้ใช้ Ollama)**
เป้าหมาย AI Agent ที่ถูกสร้างมาให้มีช่องโหว่ (OWASP Top 10 for LLMs) — workshop นี้ patch LLMGoat ให้เรียก Ollama แทน llama-cpp ฝังในตัว เพื่อประหยัด RAM และใช้ model เดียวกับ Aider
```bash
git clone https://github.com/MegaWiz-Dev-Team/LLMGoat.git
cd LLMGoat
# apply patch (manager.py / definitions.py / compose.local.yaml) — instructor distribute
docker compose -f compose.local.yaml up -d --build
```
*(default port mapping: host 5001 → container 5000 หลบ AirPlay บน macOS — เปิด `http://localhost:5001`)*

### 5. Clone Monitoring & AI Protocol Servers (MCP)
**Wazuh MCP Server (Security Monitoring)**
ใช้สำหรับมอนิเตอร์และตรวจสอบภัยคุกคามใน LLMGoat (การตั้งค่า Config และทดสอบระบบจะทำในเนื้อหา **Day 3**)
```bash
git clone https://github.com/gensecaihq/Wazuh-MCP-Server.git
```
