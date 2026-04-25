# Day 1: Foundations & Dev Automation Setup
**ระยะเวลา:** 1.5 ชั่วโมง | **รูปแบบ:** Online — Screen share + Hands-on

> **Story Arc (3 วัน):**
> Day 1 = วางสนามรบ 🛠️ — ติดตั้ง Lab ให้พร้อม
> Day 2 = ลงสนามโจมตี 🔴 — Red Team กับ Kali + Aider
> Day 3 = สลับข้าง 🔵 — Blue Team กับ Wazuh + AI Report

---

## ⚙️ ตั้งค่าก่อนเริ่ม (ทุกคนทำก่อน session)

```bash
# เลือก MODEL ตามสเปคเครื่อง — เปลี่ยนแค่บรรทัดนี้ บรรทัดเดียว
export MODEL=qwen2.5-coder:3b      # RAM 8GB+  ⭐ แนะนำ
# export MODEL=qwen2.5-coder:1.5b  # RAM 4-8GB
# export MODEL=deepseek-coder:1.3b # RAM ≤4GB
```

---

## 👨‍🏫 Instructor Prep (30 นาทีก่อน session)

```bash
# ตรวจสอบทุกอย่างพร้อมก่อนสอน
ollama list                                   # ต้องเห็น model
docker ps                                     # Docker รัน
curl -s http://localhost:5001 | head -3       # LLMGoat พร้อม
docker exec attacker_kali aider --version     # Kali + Aider พร้อม
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
macOS:   https://ollama.com/download  → ลาก Ollama.app ไป Applications
Windows: https://ollama.com/download  → รัน OllamaSetup.exe
Linux:   curl -fsSL https://ollama.com/install.sh | sh
```

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
> Port mapping default: host **5001** → container 5000 (หลบ AirPlay บน macOS)

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

> AI จะแนะนำ `docker ps`, `lsof -ti:5001 | xargs kill -9` หรือ `docker stop` → นี่คือ Dev Automation จริงๆ

---

## Session 3 — Attacker Environment [30 นาที]

### 3.1 รัน Kali Linux Container

> 🖥️ **[HOST]**

**ขั้นตอนที่ 1 — Pull + Start Kali:**
```bash
docker run --name attacker_kali \
  -itd \
  --add-host=host.docker.internal:host-gateway \
  kalilinux/kali-rolling
```

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

**ขั้นตอนที่ 3 — ตั้งค่า Ollama endpoint:**
```bash
export OLLAMA_API_BASE=http://host.docker.internal:11434
export MODEL=qwen2.5-coder:3b   # เปลี่ยนตาม model ที่ pull มา
```

**ขั้นตอนที่ 4 — ทดสอบ:**
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

ollama list                                    # [1] เห็น model?
docker ps --format "{{.Names}}" | grep llmgoat # [2] LLMGoat รัน?
curl -s -o /dev/null -w "%{http_code}" http://localhost:5001  # [3] ได้ 200?
docker ps --format "{{.Names}}" | grep kali   # [4] Kali รัน?
docker exec attacker_kali aider --version      # [5] Aider พร้อม?
```

### Troubleshooting

| ปัญหา | แก้ไข |
|-------|-------|
| `ollama list` ว่างเปล่า | `ollama pull qwen2.5-coder:3b` |
| LLMGoat ไม่ขึ้น | `docker compose -f compose.local.yaml logs` หรือ `docker logs llmgoat-cpu` ดู error |
| `host.docker.internal` ใช้ไม่ได้ | ลอง `docker run` ใหม่พร้อม `--add-host` |
| `pip/pipx install aider-chat` fail (numpy 1.24.3 / audioop) | ใช้ `uv tool install aider-chat --with audioop-lts` แทน |
| Kali container หาย | `docker start attacker_kali` |

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
```bash
# 🐧 [KALI]
export PATH=$HOME/.local/bin:$PATH
export OLLAMA_API_BASE=http://host.docker.internal:11434
cd /tmp && mkdir -p hw && cd hw && git init -q
aider --model ollama/qwen2.5-coder:3b --no-auto-commits --no-stream --yes-always \
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

> 🖥️ **[HOST]** — เปิด terminal ใหม่แล้วรัน ปล่อยทิ้งไว้

```bash
git clone https://github.com/wazuh/wazuh-docker.git -b v4.11.0
cd wazuh-docker/single-node
docker compose pull
```

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
