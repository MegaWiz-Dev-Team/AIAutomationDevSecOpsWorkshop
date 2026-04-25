# 🗺️ Student Journey — AI Automation DevSecOps Workshop
**ตั้งแต่ลงทะเบียน จนถึง Download ใบประกาศ**

---

## ภาพรวม Timeline

```
ก่อน Day 1          Day 1              Day 2              Day 3           หลัง Day 3
────────────        ─────────────      ─────────────      ─────────────   ───────────
Register         →  Warmup🧪 (7q)  →  Warmup🧪 (5q)  →  Warmup🧪 (5q) →  Certificate
ติดตั้ง tools       Lab Setup           Red Team           Blue Team          Download
                    OWASP Intro         Attack AI          Monitor
                    Homework📸          Findings📄          Report📄
                    Pre-pull🐋          Pre-pull🐋          Post-test🎯(15q)
```

---

## ⬛ ก่อนเริ่ม Day 1 (ทำล่วงหน้า 1-3 วัน)

### 👤 นักเรียนทำ

---

### ขั้นตอนที่ 1 — ลงทะเบียนใน MyHero

**1.1 เปิด Landing Page**

เปิด browser → ไปที่:
```
https://hero.megawiz.co.th/ai-devsecops
```

**1.2 กรอกแบบฟอร์มลงทะเบียน**

| ช่อง | ข้อมูล |
|------|--------|
| ชื่อ-นามสกุล | ภาษาไทย เช่น สมชาย ใจดี |
| อีเมล | อีเมลที่ใช้งานจริง (ใช้ login) |
| พื้นฐาน | Developer / Security / DevOps / นักศึกษา |

กด **"ลงทะเบียน"** → รอ email ยืนยัน

**1.3 ยืนยัน OTP ทาง Email**

เปิด inbox → รับ OTP 6 หลัก → กรอกใน Student Portal

```
URL: https://hero.megawiz.co.th
กรอก: Email + OTP ที่ได้รับ
```

> ⏱️ OTP หมดอายุใน 10 นาที — ถ้าไม่ได้รับให้กด "ส่ง OTP ใหม่"

---

### ขั้นตอนที่ 2 — ติดตั้ง Prerequisites (ทำก่อน Day 1)

**System Requirements ขั้นต่ำ:**
- OS: macOS 12+ / Windows 10/11 (WSL2) / Ubuntu 22+
- RAM: 4GB+ (8GB แนะนำ)
- Storage: 20GB free (Docker images + Wazuh)

---

#### 🐳 1. Docker Desktop (หรือ OrbStack สำหรับ Mac)

> รัน Kali Linux, LLMGoat, Wazuh ผ่าน container

**macOS — ทางเลือกที่ 1: OrbStack (เบากว่า แนะนำ)**
```bash
brew install orbstack
# หรือดาวน์โหลดโดยตรง: https://orbstack.dev
```

**macOS — ทางเลือกที่ 2: Docker Desktop**
```bash
brew install --cask docker
# หรือดาวน์โหลด: https://www.docker.com/products/docker-desktop
```

**Windows 10/11**
```
1. เปิด PowerShell as Admin → ติดตั้ง WSL2:
   wsl --install
2. ดาวน์โหลด Docker Desktop: https://www.docker.com/products/docker-desktop
3. ติดตั้ง → เปิดโปรแกรม → ติ๊ก "Use WSL 2 based engine"
```

**Linux (Ubuntu/Debian)**
```bash
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker $USER
newgrp docker
```

**ตรวจสอบ:**
```bash
docker --version        # Docker version 27.x.x
docker run hello-world  # ต้องเห็น "Hello from Docker!"
```

---

#### 🐙 2. Git

> ใช้ clone LLMGoat, Wazuh-MCP และ workshop materials

**macOS**
```bash
brew install git
# หรือ: git จะถูกติดตั้งอัตโนมัติเมื่อรัน git ครั้งแรก (ผ่าน Xcode CLI tools)
```

**Windows**
```
ดาวน์โหลด: https://git-scm.com/download/win
ติดตั้งและเลือก "Git Bash" + "Git from command line"
```

**Linux**
```bash
sudo apt install git -y       # Debian/Ubuntu
sudo dnf install git -y       # Fedora/RHEL
```

**ตรวจสอบ:**
```bash
git --version    # git version 2.x.x
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

---

#### 🔧 3. GitHub CLI (gh)

> ใช้ clone private repo, manage issues, PR ใน workshop

**macOS**
```bash
brew install gh
```

**Windows**
```
winget install --id GitHub.cli
# หรือดาวน์โหลด: https://cli.github.com/
```

**Linux**
```bash
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg \
  | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] \
  https://cli.github.com/packages stable main" \
  | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null
sudo apt update && sudo apt install gh -y
```

**ตรวจสอบ:**
```bash
gh --version    # gh version 2.x.x
gh auth login   # login ครั้งแรก (เลือก Browser)
```

---

#### 🤖 4. Ollama

> รัน Local LLM โดยไม่ต้อง API Key

**macOS / Windows / Linux**
```
ดาวน์โหลด: https://ollama.com/download
macOS: ลาก Ollama.app ไปที่ Applications
Windows: รัน OllamaSetup.exe
Linux: curl -fsSL https://ollama.com/install.sh | sh
```

**Pull model — เลือกตามสเปคเครื่อง (pull แค่ตัวเดียว):**

| Model | RAM ขั้นต่ำ | Disk | หมายเหตุ |
|-------|-----------|------|---------|
| `qwen2.5-coder:3b` ⭐ | 8GB+ | ~2GB | แนะนำ — คุณภาพดีที่สุด |
| `qwen2.5-coder:1.5b` | 4GB+ | ~1GB | สเปคต่ำ — ยังใช้งานได้ดี |
| `deepseek-coder:1.3b` | 4GB+ | ~800MB | สเปคต่ำมาก — เล็กสุด |

```bash
# แนะนำ (RAM 8GB+)
ollama pull qwen2.5-coder:3b

# สเปคต่ำ (RAM 4-8GB)
# ollama pull qwen2.5-coder:1.5b

# สเปคต่ำมาก (RAM ≤4GB)
# ollama pull deepseek-coder:1.3b
```

**ตรวจสอบ:**
```bash
ollama list                              # ต้องเห็น model ที่ pull มา
ollama run qwen2.5-coder:3b "say hello" # ต้องได้ reply
```

---

#### ✅ Checklist ก่อน Day 1

```bash
docker --version      # ✅ Docker ติดตั้งแล้ว
git --version         # ✅ Git ติดตั้งแล้ว
gh --version          # ✅ GitHub CLI ติดตั้งแล้ว
ollama list           # ✅ มี model อย่างน้อย 1 ตัว
docker run hello-world # ✅ Docker รัน container ได้
```

---

## 🟢 Day 1 — Foundations & Dev Automation Setup

### 👨‍🏫 Instructor เตรียม (30 นาทีก่อน session)

- [ ] Start Zoom/Meet, share link
- [ ] Open Student Portal admin → ตรวจสอบ registrations
- [ ] เปิด Pre-test session ใน Admin Portal → สร้าง PIN (Kahoot mode, 7 ข้อ, 20 วิ/ข้อ)
- [ ] ทดสอบ LLMGoat รันได้: `curl http://localhost:5001`
- [ ] ทดสอบ Ollama: `ollama run qwen2.5-coder:3b`
- [ ] เตรียม screen share: VS Code + Terminal + Browser

---

### ⏱️ Session Day 1 (1 – 1.5 ชั่วโมง)

| เวลา | เนื้อหา | Instructor | นักเรียน |
|------|---------|-----------|---------|
| 0:00 | เปิด session, แนะนำตัว | Share screen | Join Zoom |
| 0:05 | **Pre-test 🧪 Kahoot-style** | เปิด PIN ใน Student Portal | เข้า portal → กด Pre-test → ป้อน PIN |
| 0:15 | Workshop overview + Story arc 3 วัน | อธิบาย Big picture | ดู roadmap |
| 0:20 | **OWASP Top 10 for LLMs 2025** | เปิด owasp.org + อธิบาย 10 ข้อ | Vote ใน chat |
| 0:35 | **ติดตั้ง Ollama + Pull model** | Demo live | ทำตาม |
| 0:45 | **Deploy LLMGoat (Ollama-patched)** | `git clone` + apply patch + `docker compose -f compose.local.yaml up -d --build` | ทำตาม |
| 0:55 | **Demo: Aider ช่วย debug** | จำลอง error → ให้ AI แก้ | ดู |
| 1:05 | **Setup Kali Linux + Aider** | `docker run kali` + `pip install aider` | ทำตาม |
| 1:15 | **Verify checklist** | ถามทีละคน | ✅/❌ ใน chat |
| 1:20 | Preview Day 2 + การบ้าน | อธิบาย homework | จด |
| 1:25 | Q&A + ปิด session | | ถามใน chat |

---

### 📤 การบ้าน Day 1 (ส่งก่อน Day 2)

**ส่งผ่าน Student Portal → "การบ้าน Day 1"**

ถ่าย screenshot 3 ภาพ (หรือรวมเป็น 1 PDF):

```
ภาพ 1: Terminal แสดง Ollama ตอบคำถาม
        (เช่น "What is Prompt Injection?")

ภาพ 2: Browser เปิด LLMGoat UI ที่ localhost:5001
        (chatbot โหลดขึ้นมาแล้ว)

ภาพ 3: Terminal แสดง aider --version ใน Kali container
```

**เกณฑ์คะแนน (15 คะแนน):**
- Ollama ตอบได้ → 4 คะแนน
- LLMGoat UI โหลด → 4 คะแนน
- Aider ใน Kali → 4 คะแนน
- ส่งครบ → 3 คะแนน

**👉 Upload ที่:** Student Portal → การบ้าน Day 1 (รับไฟล์ `.png` / `.jpg` / `.pdf`)

---

### 📦 Pre-pull Wazuh (ทำหลัง submit การบ้าน — ปล่อยรันทิ้งไว้)

```bash
# [HOST] รันแล้วปล่อยทิ้งไว้ข้ามคืน
git clone https://github.com/wazuh/wazuh-docker.git -b v4.11.0
cd wazuh-docker/single-node
docker compose pull   # ~5GB, 15-30 นาที

# ตรวจสอบเมื่อ download เสร็จ
docker images | grep wazuh
# ต้องเห็น: wazuh-manager, wazuh-indexer, wazuh-dashboard
```

---

## 🔴 Day 2 — Red Team Operations

### 👨‍🏫 Instructor เตรียม (30 นาทีก่อน session)

- [ ] ตรวจ homework Day 1 ที่ส่งมา (Admin portal)
- [ ] เปิด Warmup Day 2 session ใน Admin Portal → สร้าง PIN (5 ข้อ, 20 วิ/ข้อ)
- [ ] ทดสอบ LLMGoat ยังรัน: `curl http://localhost:5001`
- [ ] เตรียม sample payloads ไว้ใน notepad
- [ ] เปิด LLMGoat source code ใน VS Code ไว้ล่วงหน้า
- [ ] เตรียม terminal: Kali container + Aider พร้อม

---

### ⏱️ Session Day 2 (1 – 1.5 ชั่วโมง)

| เวลา | เนื้อหา | Instructor | นักเรียน |
|------|---------|-----------|---------|
| 0:00 | เปิด session | Share screen | Join Zoom |
| 0:03 | **Warmup 🧪 Kahoot-style (5 ข้อ)** | แชร์ PIN ใน chat | เข้า portal → ป้อน PIN |
| 0:10 | เฉลย + ทบทวน Day 1 highlight | อธิบายข้อที่ตอบผิดเยอะ | |
| 0:15 | **Blackbox vs Whitebox Pentest** | อธิบาย + diagram | |
| 0:23 | **LLMGoat Architecture** | เปิด source code วิเคราะห์ร่วมกัน | Explore LLMGoat |
| 0:30 | **Attack Surface Mapping** | ชี้ 5 จุด + ให้นักเรียน explore | explore 3 นาที |
| 0:35 | **Attack 1: Direct Prompt Injection** | Demo Level 1–4 live | ทำตาม + บันทึก |
| 0:43 | **ใช้ Aider สร้าง payload** | Demo ใน Kali | ทำตาม |
| 0:50 | **Attack 2: Data Leakage (LLM06)** | Demo 5 strategies | ทำตาม + บันทึก |
| 1:00 | **Attack 3: Insecure Output (LLM02)** | Demo XSS via AI | ทำตาม |
| 1:07 | **สร้าง Findings Log** | Template + Aider เขียน report | กรอก findings |
| 1:15 | **docker cp ออกมา host** | Demo copy command | ทำตาม |
| 1:20 | Attack Matrix review + Preview Day 3 | สรุป ✅/❌ | กรอก matrix |
| 1:27 | Q&A + ปิด session | | ถามใน chat |

---

### 📤 การบ้าน Day 2 (ส่งก่อน Day 3)

**ส่งผ่าน Student Portal → "การบ้าน Day 2"**

ไฟล์ที่ส่ง: `day2_findings.md`

```bash
# [HOST] ตรวจสอบก่อนส่ง
cat day2_findings.md
# ต้องมี:
# - Finding อย่างน้อย 2 รายการ
# - OWASP Category (LLM01/LLM02/LLM06)
# - Payload ที่ใช้จริง
# - Response/Evidence
```

**เกณฑ์คะแนน (15 คะแนน):**
- 2+ findings → 4 คะแนน
- OWASP category ถูก → 3 คะแนน
- Payload จริง → 3 คะแนน
- Impact → 3 คะแนน
- Copy log ออกมาได้ → 2 คะแนน

**👉 Upload ที่:** Student Portal → การบ้าน Day 2 (รับไฟล์ `.md` / `.txt` / `.pdf`)

---

### 📦 Pre-pull + ตรวจสอบ Wazuh (ทำหลัง submit)

```bash
# ตรวจสอบว่า pull เสร็จแล้ว (จาก homework Day 1)
docker images | grep wazuh

# ถ้ายังไม่ได้ pull: รันด่วน
cd wazuh-docker/single-node && docker compose pull
```

---

## 🔵 Day 3 — Blue Team Operations

### 👨‍🏫 Instructor เตรียม (30 นาทีก่อน session)

- [ ] ตรวจ homework Day 2 — `day2_findings.md` ส่งมาครบไหม
- [ ] เปิด Warmup Day 3 session ใน Admin Portal → สร้าง PIN (5 ข้อ, 20 วิ/ข้อ)
- [ ] เปิด Post-test session ไว้ล่วงหน้า (15 ข้อ — จะใช้ช่วงท้าย)
- [ ] ตรวจสอบ Wazuh images: `docker images | grep wazuh`
- [ ] เตรียม terminal แยก: หนึ่งสำหรับ Wazuh, หนึ่งสำหรับ Kali
- [ ] ทดสอบ LLMGoat ยังรัน + logs มีข้อมูล

---

### ⏱️ Session Day 3 (1 – 1.5 ชั่วโมง)

| เวลา | เนื้อหา | Instructor | นักเรียน |
|------|---------|-----------|---------|
| 0:00 | เปิด session | Share screen | Join Zoom |
| 0:03 | **Warmup 🧪 Kahoot-style (5 ข้อ)** | แชร์ PIN ใน chat | เข้า portal → ป้อน PIN |
| 0:10 | เฉลย + Red Team → Blue Team story arc | อธิบายข้อที่ตอบผิดเยอะ | |
| 0:15 | **Start Wazuh** — generate certs + `docker compose up` | Demo live | ทำตาม |
| 0:19 | รอ Wazuh healthy (อธิบาย Wazuh architecture ระหว่างรอ) | อธิบาย | ดู docker ps |
| 0:25 | **เข้า Wazuh Dashboard** `https://localhost` | Show dashboard | ทำตาม |
| 0:30 | **Ship attack logs** — Aider เขียน python shipper | Demo live | ทำตาม |
| 0:40 | **Setup Wazuh MCP Server** | Config `.env` + start | ทำตาม |
| 0:45 | **Aider + MCP: วิเคราะห์ events** | Demo Blue Team analyst | ทำตาม |
| 0:55 | **Code Audit ด้วย Aider** | audit LLMGoat source | ทำตาม |
| 1:02 | **AI เขียน Vulnerability Report** | Demo Ollama API call | ทำตาม |
| 1:10 | `docker cp` report ออกมา host + **Workshop Recap** | สรุป key takeaways | |
| 1:17 | **🎯 Post-test Kahoot (15 ข้อ)** | แชร์ PIN ใน chat | Join ด้วย PIN |
| 1:27 | Leaderboard + อธิบาย certificate + ปิด | Show leaderboard | |

---

### 🎯 Post-test Kahoot (ในเซสชัน Day 3)

**Instructor:**
1. Admin Portal → Quiz Host → Create Session
2. เลือก batch: `AIDevSecOps01`
3. เลือก quiz: `ai-devsecops-basics` (Post-test mode)
4. ตั้ง timer: 20 วินาที/ข้อ
5. แชร์ **PIN 6 หลัก** ใน Zoom chat

**นักเรียน:**
1. เปิด Student Portal → กด **"Post-test"**
2. กรอก PIN + ชื่อเล่น
3. ตอบ 15 ข้อ → ดู leaderboard real-time

```
คะแนน Kahoot: 1,000 คะแนน/ข้อ (full marks ถ้าตอบเร็ว + ถูก)
Streak bonus: +100 ต่อข้อต่อเนื่อง (สูงสุด +300)
คะแนนสูงสุด: 15,000 + streak = ~16,500 คะแนน
```

> 💡 คะแนน Kahoot แสดงใน leaderboard แต่สิ่งที่บันทึกลง enrollment คือจำนวนข้อที่ตอบถูก (0-15) ซึ่งใช้คำนวณ certificate eligibility

---

### 📤 การบ้าน Day 3 (ส่งหลัง session — ภายใน 3 วัน)

**ส่งผ่าน Student Portal → "การบ้าน Day 3"**

ไฟล์ที่ส่ง: `vulnerability_report.md`

```bash
# [HOST] ตรวจสอบก่อนส่ง
cat vulnerability_report.md
# ต้องมี:
# - Executive Summary ภาษาไทย
# - Findings Table (OWASP, Severity)
# - 2+ Detailed Findings
# - Remediation Checklist
```

**เกณฑ์คะแนน (15 คะแนน):**
- Executive Summary ภาษาไทย → 3 คะแนน
- Findings Table ≥ 2 รายการ → 3 คะแนน
- OWASP Category ถูก → 2 คะแนน
- Severity ระบุ → 2 คะแนน
- Remediation → 3 คะแนน
- Wazuh event count → 2 คะแนน

**👉 Upload ที่:** Student Portal → การบ้าน Day 3 (รับไฟล์ `.md` / `.pdf`)

---

## 🏆 หลัง Day 3 — Certificate

### การคำนวณคะแนนรวม

| ส่วน | คะแนนเต็ม | เกณฑ์ผ่าน |
|------|----------|----------|
| Post-test Quiz (15 ข้อ) | 15 คะแนน | ≥ 9/15 (60%) |
| การบ้าน Day 1 | 15 คะแนน | — |
| การบ้าน Day 2 | 15 คะแนน | — |
| การบ้าน Day 3 | 15 คะแนน | — |
| **รวมทั้งหมด** | **60 คะแนน** | **Post-test ≥ 60%** |

> ⚠️ **เงื่อนไขขอ Certificate:** ต้องผ่าน Post-test ≥ 9/15 ข้อ
> การบ้านทั้ง 3 วันใช้แสดงใน portal แต่ไม่ block certificate

---

### ขั้นตอน Download Certificate

**ขั้นตอนที่ 1 — ตรวจสอบสิทธิ์**

เข้า Student Portal → เลือก batch → ดูที่ส่วน **"คะแนน"**

```
✅ เงื่อนไขครบ:
   Post-test: X/15 (≥ 9 = 60%)
   → ปุ่ม "ดูใบประกาศ" จะ active

❌ ยังไม่ผ่าน:
   "คะแนนยังไม่ถึงเกณฑ์: X/15 (Y%) — ต้องการ 60%"
```

**ขั้นตอนที่ 2 — เปิดใบประกาศ**

กด **"ดูใบประกาศ"** → ระบบตรวจสอบ:
```
1. ✅ Post-test score ≥ 60%?
2. ✅ requireDisc = false (ไม่ต้องทำ DISC สำหรับ workshop นี้)
→ แสดง Certificate พร้อม PDF download
```

**ขั้นตอนที่ 3 — Download PDF**

กด **"Download PDF"** → บันทึกไฟล์

```
ชื่อไฟล์: certificate-[ชื่อ]-AIDevSecOps-2026.pdf
ข้อมูลในใบประกาศ:
  - ชื่อ-นามสกุล (ภาษาไทย)
  - Workshop: AI Automation DevSecOps Workshop
  - Batch: #1 | วันที่จบ
  - Watermark: AI DEVSECOPS WORKSHOP
```

> 💡 หลัง download ระบบบันทึก `certificateDownloadedAt` อัตโนมัติ

---

## 📊 สรุป Checklist ทั้ง Journey

```
PRE-WORKSHOP
[ ] ลงทะเบียนที่ landing page
[ ] ยืนยัน OTP ทาง email
[ ] Login Student Portal สำเร็จ
[ ] ทำ Pre-test (self mode)
[ ] ติดตั้ง Docker + Ollama + Git + Python

DAY 1 SESSION
[ ] เข้า Zoom ตรงเวลา
[ ] ตอบ Warmup Kahoot 7 ข้อ (รับ PIN จาก Instructor)
[ ] ติดตั้ง Ollama + pull model
[ ] Deploy LLMGoat (`docker compose -f compose.local.yaml up -d --build`)
[ ] Setup Kali + Aider
[ ] Verify checklist ✅ ทุกข้อ

DAY 1 HOMEWORK (ก่อน Day 2)
[ ] ถ่าย 3 screenshots
[ ] Upload ที่ Student Portal → การบ้าน Day 1
[ ] Pre-pull Wazuh images (ปล่อยรันข้ามคืน)

DAY 2 SESSION
[ ] เข้า Zoom ตรงเวลา
[ ] ตอบ Warmup Kahoot 5 ข้อ (ทบทวน Day 1)
[ ] ทำ Attack 1: Prompt Injection
[ ] ทำ Attack 2: Data Leakage
[ ] ทำ Attack 3: Insecure Output
[ ] สร้าง day2_findings.md
[ ] docker cp ออกมา host

DAY 2 HOMEWORK (ก่อน Day 3)
[ ] Upload day2_findings.md → Student Portal → การบ้าน Day 2
[ ] ตรวจสอบ Wazuh images พร้อม

DAY 3 SESSION
[ ] เข้า Zoom ตรงเวลา
[ ] ตอบ Warmup Kahoot 5 ข้อ (ทบทวน Day 2)
[ ] Start Wazuh + เข้า Dashboard
[ ] Ship attack logs → Wazuh
[ ] Wazuh MCP + Aider วิเคราะห์
[ ] Code Audit ด้วย Aider
[ ] AI เขียน Vulnerability Report
[ ] เข้าร่วม Post-test Kahoot 15 ข้อ (รับ PIN จาก Instructor)

DAY 3 HOMEWORK (ภายใน 3 วัน)
[ ] Upload vulnerability_report.md → Student Portal → การบ้าน Day 3

CERTIFICATE
[ ] Post-test score ≥ 9/15 (60%)
[ ] Student Portal → "ดูใบประกาศ" → Download PDF
```

---

## 🆘 Troubleshooting & Support

| ปัญหา | วิธีแก้ |
|-------|--------|
| ไม่ได้รับ OTP | ตรวจ spam folder / กด "ส่งใหม่" |
| Login ไม่ได้ | ตรวจ email ให้ถูกต้อง / ขอ OTP ใหม่ |
| LLMGoat ไม่ขึ้น | `docker logs llmgoat-cpu` ดู error (เช็คว่าเชื่อม Ollama ได้) + ถาม Instructor |
| Ollama ช้า/ค้าง | RAM ไม่พอ → ลอง `qwen2.5-coder:1.5b` แทน |
| Wazuh ไม่ start | RAM ไม่พอ → ต้องการ 4GB+ สำหรับ Wazuh |
| ส่งการบ้านไม่ได้ | ตรวจขนาดไฟล์ (ต้อง < 10MB) |
| ใบประกาศไม่ขึ้น | ตรวจ Post-test score ใน portal |
| คะแนนไม่ update | รอ 5 นาที / refresh หน้า |

**ติดต่อ Instructor:** พิมพ์ใน Zoom chat หรือ LINE Group ของ batch
