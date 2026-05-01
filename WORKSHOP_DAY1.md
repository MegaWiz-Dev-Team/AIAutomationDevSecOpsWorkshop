# Day 1: Foundations & Dev Automation Setup
**ระยะเวลา:** 1.5 ชั่วโมง | **รูปแบบ:** Online — Screen share + Hands-on

> **Story Arc (3 วัน):**
> Day 1 = วางสนามรบ 🛠️ — ติดตั้ง Lab ให้พร้อม
> Day 2 = ลงสนามโจมตี 🔴 — Red Team กับ Kali + Aider
> Day 3 = สลับข้าง 🔵 — Blue Team กับ Wazuh + AI Report

---

## 🪟 สำหรับนักเรียน Windows — อ่านก่อนเริ่ม

> ทุกคำสั่ง `[HOST]` ในเอกสารนี้ใช้ **bash syntax** — Windows ต้องเตรียม 4 อย่างนี้ก่อน:
>
> | สิ่งที่ต้องติดตั้ง | ดาวน์โหลด | หมายเหตุ |
> |------------------|-----------|----------|
> | **Docker Desktop** | https://docs.docker.com/desktop/install/windows-install/ | เปิดทิ้งไว้ตลอด workshop |
> | **Git for Windows** (ได้ Git Bash มาด้วย) | https://git-scm.com/download/win | ติ๊ก "Git Bash Here" ตอนติดตั้ง |
> | **GitHub CLI** | https://cli.github.com/ | ใช้ส่งการบ้าน |
> | **Ollama** | https://ollama.com/download → OllamaSetup.exe | รัน installer ปกติ |
>
> ⚠️ **ใช้ Git Bash เท่านั้น** สำหรับคำสั่ง `[HOST]` — ไม่ใช่ CMD หรือ PowerShell
> Git Bash รองรับ `export`, `grep`, `curl`, `cat`, `wc` ได้เหมือน Mac/Linux ทุกอย่าง
> เปิด Git Bash: คลิกขวาที่ Desktop → **"Git Bash Here"** หรือค้นหา "Git Bash" ใน Start

---

## ⚙️ ตั้งค่าก่อนเริ่ม (ทุกคนทำก่อน session)

```bash
# 🪟 Windows: เปิด Git Bash ก่อน (ไม่ใช่ CMD / PowerShell)
# เลือก MODEL ตามสเปคเครื่อง — เปลี่ยนแค่บรรทัดนี้ บรรทัดเดียว
export MODEL=qwen2.5-coder:3b      # RAM 8GB+  ⭐ แนะนำ
# export MODEL=qwen2.5-coder:1.5b  # RAM 4-8GB
# export MODEL=deepseek-coder:1.3b # RAM ≤4GB
```

---

## 👨‍🏫 Instructor Prep (30 นาทีก่อน session)

```bash
# 🪟 Windows: รันใน Git Bash
# ตรวจสอบทุกอย่างพร้อมก่อนสอน
ollama list                                                         # ต้องเห็น model
docker ps                                                           # Docker รัน
curl -s -o /dev/null -w "%{http_code}" http://localhost:5001        # ต้องได้ 200
docker exec attacker_kali bash -c 'source ~/.bashrc && aider --version'  # Kali + Aider พร้อม
```

Admin Portal → เปิด **Warmup Pre-test session** → สร้าง PIN (7 ข้อ, 20 วิ/ข้อ)

---

## 🧪 Warmup — Pre-test Kahoot [10 นาที]

**Instructor:** แชร์ PIN ใน Zoom chat

**นักเรียน — ทำตามขั้นตอน:**
```
1. เปิด https://hero.megawiz.co.th
2. Login → เลือก "AI Automation DevSecOps Workshop #1"
3. กด "Warmup Day 1 — Pre-test"
4. กรอก PIN ที่ Instructor ให้
5. ตอบ 7 ข้อ (20 วินาที/ข้อ)
```

> หลังเฉลย Instructor อธิบายข้อที่ตอบผิดเยอะ → เชื่อมเข้า OWASP

---

## Session 1 — OWASP + Big Picture [20 นาที]

### 1.1 AIAutomationDevSecOps คืออะไร?

```
AI          = ใช้ LLM เป็น brain ให้ tools (โดยไม่ต้อง API Key ด้วย Ollama)
Automation  = scripting, Docker, CI/CD ทำงานแทนมนุษย์
DevSecOps   = Security ฝังอยู่ใน Dev pipeline ตั้งแต่ต้น

รวมกัน = ใช้ AI ช่วยหาช่องโหว่ + ป้องกัน + รายงาน — อัตโนมัติ
```

**ตัวอย่างจริงใน Workshop นี้:**

| บทบาท | ใช้ AI ทำอะไร |
|--------|--------------|
| 🔴 Red Team | Aider เขียน Prompt Injection payload อัตโนมัติ |
| 🔵 Blue Team | Aider วิเคราะห์ log + ตรวจจับ anomaly |
| 📄 Report | Ollama สรุป vulnerability report จาก raw findings |

### 1.2 OWASP Top 10 for LLMs 2025

เปิด: `https://owasp.org/www-project-top-10-for-large-language-model-applications/`

| # | ชื่อ | ความหมาย | ฝึกวันไหน |
|---|------|----------|----------|
| LLM01 | Prompt Injection | หลอก AI ด้วย input | **Day 2** ⭐ |
| LLM02 | Insecure Output Handling | output ถูกนำไป execute อันตราย | **Day 2** ⭐ |
| LLM03 | Training Data Poisoning | ข้อมูลฝึก AI ถูก tamper | (reference) |
| LLM04 | Model Denial of Service | ทำให้ AI หยุดทำงาน | (reference) |
| LLM05 | Supply Chain | ช่องโหว่ใน dependency | (reference) |
| LLM06 | Sensitive Info Disclosure | AI หลุดข้อมูลลับ | **Day 2** ⭐ |
| LLM07 | Insecure Plugin Design | Plugin ถูกโจมตี | (reference) |
| LLM08 | Excessive Agency | AI ทำงานเกินขอบเขต | **Day 3** |
| LLM09 | Overreliance | เชื่อ AI มากเกินไป | **Day 3** |
| LLM10 | Model Theft | ขโมย model/prompt | (reference) |

> 💬 Instructor: ถามใน chat "ข้อไหนน่ากลัวที่สุดในงานของคุณ?"

---

## Session 2 — Lab Setup [35 นาที]

### 2.1 ติดตั้ง Ollama + Pull Model

> 🖥️ **[HOST]** — รันบนเครื่องของตัวเอง

**ขั้นตอนที่ 1 — ดาวน์โหลด Ollama:**
```
Windows: https://ollama.com/download  → รัน OllamaSetup.exe  ⭐ (นักเรียนส่วนใหญ่)
macOS:   https://ollama.com/download  → ลาก Ollama.app ไป Applications
Linux:   curl -fsSL https://ollama.com/install.sh | sh
```
> 🪟 Windows: หลังติดตั้ง Ollama จะรันเป็น background service อัตโนมัติ — ไม่ต้องรัน `ollama serve` แยก

**ขั้นตอนที่ 2 — Pull model (เลือกแค่ 1 ตัว):**
```bash
ollama pull qwen2.5-coder:3b       # ⭐ แนะนำ  RAM 8GB+  ~2GB
# ollama pull qwen2.5-coder:1.5b  # สเปคต่ำ  RAM 4GB+  ~1GB
# ollama pull deepseek-coder:1.3b # เล็กสุด  RAM 4GB+  ~800MB
```

**ขั้นตอนที่ 3 — ทดสอบ:**
```bash
ollama run $MODEL "What is Prompt Injection in 1 sentence?"
```
> ✅ ต้องได้คำตอบกลับ — ถ้าไม่ได้ให้ตรวจว่า Ollama app เปิดอยู่

---

### 2.2 Deploy LLMGoat (Ollama-patched fork)

> 🖥️ **[HOST]**
>
> หมายเหตุ: ใช้ workshop fork ที่ `MegaWiz-Dev-Team/LLMGoat` — patch ให้เรียก Ollama แทน llama-cpp ฝังในตัว (ประหยัด RAM ~5.5GB, ใช้ model เดียวกับ Aider) student `git clone` ได้ patch ครบเลย

**ขั้นตอนที่ 1 — Clone + Start:**
```bash
git clone https://github.com/MegaWiz-Dev-Team/LLMGoat.git
cd LLMGoat
docker compose -f compose.local.yaml up -d --build
```
> Build ครั้งแรก ~80 วินาที — รันรอบต่อไปใช้ cache ~5 วิ
> Port mapping: host **5001** → container 5000

**ขั้นตอนที่ 2 — ตรวจสอบ:**
```bash
docker ps --filter "name=llmgoat"  # ต้องเห็น llmgoat-cpu Up + ports 5001->5000
docker logs llmgoat-cpu | grep -E "Ollama host|Selecting Ollama"
# ต้องเห็น:
#   Ollama host: http://host.docker.internal:11434
#   Selecting Ollama model: qwen2.5-coder:3b
```

> 🌐 **[BROWSER]** — เปิด `http://localhost:5001`
> ✅ ต้องเห็น LLMGoat chat UI โหลดขึ้นมา

---

### 2.3 Demo: AI ช่วย Debug Error [5 นาที]

> Instructor สาธิต — นักเรียนดู

จำลอง Docker error แล้วถาม Ollama:

```bash
# 🖥️ [HOST]
ollama run $MODEL
```

```
# พิมพ์ใน Ollama prompt:
I have this Docker error:
"Error: port is already allocated. Port 5001 is already in use."
How do I fix this? Give me the exact command.
```

> AI จะแนะนำคำสั่งแก้ปัญหา port conflict → นี่คือ Dev Automation จริงๆ
> 🪟 Windows: คำสั่ง `lsof` ไม่มี — ถ้า AI แนะนำ `lsof` ให้บอกว่า "I'm on Windows, give me the equivalent command"

---

## Session 3 — Attacker Environment [30 นาที]

### 3.1 รัน Kali Linux Container

> 🖥️ **[HOST]**

**ขั้นตอนที่ 1 — Pull + Start Kali:**
```bash
# 🪟 Windows / Mac (Docker Desktop) — ใช้คำสั่งนี้:
docker run --name attacker_kali \
  -itd \
  kalilinux/kali-rolling
```
> 🪟 **Docker Desktop (Windows/Mac):** `host.docker.internal` ทำงานอัตโนมัติ — ไม่ต้องใส่ `--add-host`
> 🐧 Linux Docker Engine เท่านั้น ที่ต้องเพิ่ม `--add-host=host.docker.internal:host-gateway`

**ขั้นตอนที่ 2 — เข้า Kali shell:**
```bash
docker exec -it attacker_kali /bin/bash
```

**ขั้นตอนที่ 3 — อัปเดตและติดตั้ง tools:**
```bash
# 🐧 [KALI] — รันภายใน container
apt update -qq && apt install -y \
  python3 python3-pip curl git wget jq
```

---

### 3.2 ติดตั้ง Aider

> 🐧 **[KALI]** — ทำต่อจากขั้นตอนข้างบน (ยังอยู่ใน Kali)
>
> ⚠️ Kali rolling ใช้ Python 3.13 ทำให้ `pip install aider-chat` และ `pipx install aider-chat` พังทั้งคู่ (numpy==1.24.3 ไม่มี wheel + audioop ถูกลบจาก stdlib) → ต้องใช้ `uv tool install --with audioop-lts`

**ขั้นตอนที่ 1 — ติดตั้ง uv (modern Python tool):**
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
export PATH="$HOME/.local/bin:$PATH"
uv --version   # uv 0.11.x
```

**ขั้นตอนที่ 2 — ติดตั้ง Aider + audioop-lts shim:**
```bash
uv tool install aider-chat --with audioop-lts
# ใช้เวลา ~80 วินาที (ครั้งเดียว — ครั้งต่อมาใช้ cache)
```

**ขั้นตอนที่ 3 — เพิ่ม PATH และบันทึกถาวร:**
```bash
export PATH="$HOME/.local/bin:$PATH"
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
```
> ⚠️ ขั้นตอนนี้สำคัญมาก — หากไม่ทำ `aider` จะ "not found" ทุกครั้งที่ `docker start` ใหม่

**ขั้นตอนที่ 4 — ตั้งค่า Ollama endpoint:**
```bash
export OLLAMA_API_BASE=http://host.docker.internal:11434
export MODEL=qwen2.5-coder:3b   # เปลี่ยนตาม model ที่ pull มา

# บันทึกถาวรใน .bashrc ด้วย
echo 'export OLLAMA_API_BASE=http://host.docker.internal:11434' >> ~/.bashrc
echo 'export MODEL=qwen2.5-coder:3b' >> ~/.bashrc
```

**ขั้นตอนที่ 5 — ทดสอบ:**
```bash
aider --model ollama/$MODEL --version   # → aider 0.86.x
```
> ✅ ต้องเห็น version string ของ aider

**ขั้นตอนที่ 4 — ทดสอบสร้าง script:**
```bash
aider --model ollama/$MODEL \
      --no-auto-commits \
      --message "Write a Python script that sends a GET request to http://host.docker.internal:5001 and prints the HTTP status code"
```

> ✅ Aider จะสร้างไฟล์ Python ให้ — นี่คือ Agentic AI ทำงาน

---

### 3.3 VirusTotal Demo [5 นาที]

> 🌐 **[BROWSER]** — Instructor สาธิต

เปิด `https://www.virustotal.com`

```
URL ที่ใช้ demo: http://testphp.vulnweb.com
```

1. วาง URL → กด Search
2. ดู Detection Results จาก 70+ security vendors
3. อธิบาย: นี่คือ Threat Intelligence — reconnaissance ก่อน pentest

> ⚠️ ห้ามใช้ `localhost` หรือ IP ส่วนตัว — VirusTotal ทำงานกับ public URL เท่านั้น

---

## Session 4 — Verify + Q&A [10 นาที]

### Checklist ยืนยัน (ทุกคนพิมพ์ ✅ หรือ ❌ ใน chat)

```bash
# 🖥️ [HOST] รันทีละบรรทัด แล้วบอก result ใน chat

# 🪟 Windows: รันใน Git Bash
ollama list                                    # [1] เห็น model?
docker ps --format "{{.Names}}" | grep llmgoat # [2] LLMGoat รัน?
curl -s -o /dev/null -w "%{http_code}" http://localhost:5001  # [3] ได้ 200?
docker ps --format "{{.Names}}" | grep kali   # [4] Kali รัน?
docker exec attacker_kali bash -c 'source ~/.bashrc && aider --version'  # [5] Aider พร้อม?
```

### Troubleshooting

| ปัญหา | สาเหตุ | แก้ไข |
|-------|--------|-------|
| Docker command ไม่ทำงาน | เปิด CMD/PowerShell แทน Git Bash | เปิด **Git Bash** แล้วลองใหม่ |
| Docker Desktop ไม่เริ่ม | App ไม่ได้เปิด | ค้นหา "Docker Desktop" ใน Start menu → รอ icon ใน system tray เปลี่ยนเป็นปกติ |
| `ollama list` ว่างเปล่า | ยังไม่ได้ pull | `ollama pull qwen2.5-coder:3b` |
| LLMGoat ไม่ขึ้น | build error หรือ port ซ้ำ | `docker compose -f compose.local.yaml logs` หรือ `docker logs llmgoat-cpu` |
| `host.docker.internal` ใช้ไม่ได้ (Linux เท่านั้น) | Linux Docker Engine ต้องเพิ่ม flag | รัน Kali ใหม่พร้อม `--add-host=host.docker.internal:host-gateway` |
| `pip/pipx install aider-chat` fail | numpy 1.24.3 / audioop ไม่มี wheel | ใช้ `uv tool install aider-chat --with audioop-lts` แทน |
| Kali container หาย | หยุดรันหลัง restart | `docker start attacker_kali` |

---

## 📤 การบ้าน Day 1 — Lab Screenshot (ส่งก่อน Day 2)

**ส่งผ่าน:** Student Portal → "การบ้าน Day 1"

ถ่าย screenshot 3 ภาพ (หรือรวมเป็น PDF เดียว):

**ภาพที่ 1 — Ollama ตอบคำถาม:**
```bash
# 🖥️ [HOST]
ollama run $MODEL "What is Prompt Injection? Answer in 2 sentences."
```
> ถ่าย screenshot ที่เห็น terminal + คำตอบ

**ภาพที่ 2 — LLMGoat Chat UI:**
```
เปิด browser → http://localhost:5001 → คลิก A01 Prompt Injection → พิมพ์ "สวัสดี"
```
> ถ่าย screenshot ที่เห็น Billy the Goat ตอบกลับ chat ≥ 1 รอบ

**ภาพที่ 3 — Aider create file ผ่าน Ollama (end-to-end ⭐):**

ขั้นตอนที่ 1 — เข้า Kali (รันใน Git Bash บน Windows หรือ Terminal บน Mac):
```bash
# 🖥️ [HOST]
docker exec -it attacker_kali /bin/bash
```

ขั้นตอนที่ 2 — รันใน Kali shell:
```bash
# 🐧 [KALI]
source ~/.bashrc   # โหลด PATH + env vars ที่ตั้งไว้ตอนติดตั้ง
cd /tmp && mkdir -p hw && cd hw && git init -q
aider --model ollama/$MODEL --no-auto-commits --no-stream --yes-always \
      --message "Write a Python one-liner that prints hello world"
```
> ถ่าย screenshot ที่เห็น `Applied edit to hello_world.py` + ไฟล์ถูกสร้าง — ครอบทั้ง stack (Kali → host network → Ollama → response → file write)

### โครงสร้างที่ส่ง — GitHub repo

```
my-ai-devsecops-lab/
├── README.md                    # ชื่อ-นามสกุล + batch + intro 1-2 บรรทัด
└── day1/
    ├── screenshots/
    │   ├── 01-ollama-run.png
    │   ├── 02-llmgoat-chat.png
    │   └── 03-aider-via-ollama.png    ⭐
    ├── owasp-notes.md           # 3 ข้อจาก OWASP LLM + อธิบายเกี่ยวกับงานคุณ
    ├── troubleshooting.md       # ปัญหา ≥ 2 อย่าง + วิธีแก้
    └── questions.md             # ≥ 2 คำถามค้างใจสำหรับ Day 2
```

### Git Workflow — ส่งงานใน 5 ขั้นตอน

> 🪟 **Windows:** รันทุกคำสั่งใน **Git Bash** (ไม่ใช่ CMD) — `mkdir -p`, `touch`, `git`, `gh` ทำงานได้ปกติ
> สำหรับ screenshots: drag ไฟล์จาก Explorer ไปวางใน folder `day1/screenshots/` ได้เลย

```bash
# 1) Login GitHub CLI (ครั้งแรกเท่านั้น)
gh auth login

# 2) สร้าง repo + clone ลงเครื่อง
gh repo create my-ai-devsecops-lab --public --clone
cd my-ai-devsecops-lab

# 3) สร้างโครงสร้าง + วาง screenshots + เขียน .md
mkdir -p day1/screenshots
touch README.md day1/owasp-notes.md day1/troubleshooting.md day1/questions.md
# ...copy 3 screenshots ไป day1/screenshots/ และเขียนเนื้อหา .md ทั้ง 3 ไฟล์...
# 🪟 Windows tip: เขียน .md ด้วย Notepad, VS Code, หรือ Notepad++ ก็ได้

# 4) Stage → Commit → Push
git status
git add .
git commit -m "day 1 lab — ollama + llmgoat + aider"
git push

# 5) Copy URL ไปวางใน Student Portal
gh repo view --web   # → copy URL จาก browser
```

### เกณฑ์คะแนน (15 คะแนน)

| ไฟล์ | คะแนน | เกณฑ์ผ่าน |
|------|-------|-----------|
| `screenshots/01-ollama-run.png` | 3 | เห็น `ollama run` + คำตอบจริงจาก model |
| `screenshots/02-llmgoat-chat.png` | 3 | เห็น `localhost:5001` + Billy ตอบ chat ≥ 1 รอบ |
| `screenshots/03-aider-via-ollama.png` ⭐ | 4 | Aider create ไฟล์จริงผ่าน Ollama (`Applied edit`) |
| `owasp-notes.md` | 2 | 3 ข้อ + อธิบาย ≥ 1 ประโยค/ข้อ ว่าเกี่ยวกับงานคุณยังไง |
| `troubleshooting.md` | 2 | ปัญหา ≥ 2 อย่าง + วิธีแก้ (เช่น port 5001, uv install) |
| `questions.md` | 1 | ≥ 2 คำถามค้างใจสำหรับ Day 2 |
| **รวม** | **/15** | |

> 💡 repo เดียวใช้ทั้ง 3 วัน — Day 2/3 commit เพิ่ม → portfolio พร้อม showcase
> ⚠️ ห้าม commit secrets (.env, API keys) — ใช้ `.gitignore`

---

## 📦 Pre-pull Wazuh (ทำหลังส่งการบ้าน — ปล่อยรันข้ามคืน)

> 🖥️ **[HOST]** — เปิด **Git Bash** ใหม่แล้วรัน ปล่อยทิ้งไว้ (~5GB, 15-30 นาที)

```bash
# 🪟 Windows: รันใน Git Bash — ห้ามปิด terminal จนกว่าจะ pull เสร็จ
git clone https://github.com/wazuh/wazuh-docker.git -b v4.11.0
cd wazuh-docker/single-node
docker compose pull
```
> 🪟 Windows tip: ถ้า terminal ปิดระหว่าง pull → รัน `docker compose pull` ใหม่ได้เลย มัน resume ต่อจากที่ค้างได้

ตรวจสอบตอนเช้า:
```bash
docker images | grep wazuh
# ต้องเห็น 3 images: wazuh-manager, wazuh-indexer, wazuh-dashboard
```

---

## → เตรียมสำหรับ Day 2

> ⚠️ อย่าปิด container — ต้องใช้ต่อ Day 2 ทันที

| สิ่งที่ติดตั้งวันนี้ | ใช้ใน Day 2 |
|-------------------|------------|
| `attacker_kali` container | Base สำหรับ Red Team |
| Aider ใน Kali | สร้าง Prompt Injection payloads |
| LLMGoat `localhost:5001` | เป้าหมายโจมตี |
| Ollama `localhost:11434` | Brain ของ Aider ตลอด workshop |
