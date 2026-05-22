# Email Announcement — Day 1 Update + Aider Fix

**To:** AIDevSecOps01 students
**From:** Paripol — Instructor
**Sent:** [กรอกวันที่ส่ง]
**Subject:** [AIDevSecOps01] อัปเดต Day 1: Aider install fix + การบ้าน

---

สวัสดีครับทุกคน

ขอบคุณที่ร่วม Day 1 ของ AI Automation DevSecOps Workshop วันนี้ — ทุกคนทำดีมากครับ 👏

## 🐐 LLMGoat — เปลี่ยนเป็น clone จาก workshop fork

ในวัน Day 1 เรา clone จาก **upstream SECFORCE/LLMGoat** ซึ่งฝัง `llama-cpp-python` + ดาวน์โหลด `gemma-2.gguf` (~5.5 GB) ในตัว — กิน RAM 6-7 GB และเปลือง bandwidth (ไม่ได้ใช้ Ollama ที่เราเตรียมไว้!)

ผมเลย fork + patch ให้เรียก **Ollama** ที่ host แทน → ใช้ model ตัวเดียวกับ Aider, ประหยัด RAM, ไม่ต้องโหลด gguf

### วิธีอัปเดต — ลบ clone เดิม + clone fork ใหม่

```bash
# 🖥️ HOST — หยุด + ลบ container + folder เก่า
docker stop llmgoat-cpu 2>/dev/null
docker rm llmgoat-cpu 2>/dev/null
cd ~ && rm -rf LLMGoat

# Clone จาก workshop fork (มี patch ครบแล้ว)
git clone https://github.com/MegaWiz-Dev-Team/LLMGoat.git
cd LLMGoat
docker compose -f compose.local.yaml up -d --build
# Build ครั้งแรก ~80 วิ — ครั้งถัดไปใช้ cache ~5 วิ

# Verify เชื่อม Ollama ได้
docker logs llmgoat-cpu | grep -E "Ollama host|Selecting Ollama"
# ต้องเห็น:
#   Ollama host: http://host.docker.internal:11434
#   Selecting Ollama model: qwen2.5-coder:3b

# เปิด browser → http://localhost:5001
```

> **Port:** ใช้ `5001` (เพราะ macOS port 5000 ชน AirPlay Receiver) — compose.local.yaml ตั้ง mapping `5001:5000` ไว้แล้ว ไม่ต้องเซ็ตเอง
> **Repo URL ใหม่:** `https://github.com/MegaWiz-Dev-Team/LLMGoat.git` (parent: SECFORCE/LLMGoat, GPL-3.0)

---

## 🐛 Bug Report — สาเหตุที่ aider ติดตั้งไม่ผ่าน

หลัง session จบ ผมไป test environment ของ Kali rolling เวอร์ชันปัจจุบัน เจอว่ามี 2 issues ที่ทำให้ command ใน slide เดิมพังครับ:

1. **numpy build error** — Kali rolling มาพร้อม Python 3.13 ซึ่ง numpy 1.24.3 (dep ของ aider เก่า) ไม่มี prebuilt wheel ต้อง compile from source บน aarch64 → fail
2. **audioop missing** — Python 3.13 ลบ `audioop` จาก stdlib แต่ pydub (dep ของ aider) ยังเรียกใช้ → ImportError

ทั้ง `pip install aider-chat --break-system-packages` และ `pipx install aider-chat` พังจาก issue นี้ทั้งคู่ครับ

## ✅ Fix Aider — ใช้ uv แทน (แนะนำใหม่ใน slide แล้ว)

```bash
docker exec -it attacker_kali bash

# ภายใน Kali:
curl -LsSf https://astral.sh/uv/install.sh | sh
export PATH="$HOME/.local/bin:$PATH"
echo 'export PATH=$HOME/.local/bin:$PATH' >> ~/.bashrc

uv tool install aider-chat --with audioop-lts
aider --version    # ต้องเห็น aider 0.86.x
```

## 📚 สไลด์ Day 1 อัปเดตแล้ว

👉 **https://csh-portal.web.app/slides-day1**

สไลด์ใหม่ครอบคลุม:

- **Slide 9-10** Setup Kali ละเอียดทุก step + verify network
- **Slide 11** ติดตั้ง Aider ด้วย `uv` (พร้อมอธิบายทำไม pip/pipx พัง)
- **Slide 12** Live test — ให้ Aider เขียน Python file ผ่าน Ollama ใน 12 วินาที
- **Slide 13** Verify Checklist 5 layers (Ollama → LLMGoat → Kali network → Aider → end-to-end)
- **Slide 14** การบ้าน + Git workflow 5 ขั้นตอน

## 📤 การบ้าน Day 1 (15 คะแนน) — Lab Portfolio Repo

สร้าง GitHub repo ชื่อ `my-ai-devsecops-lab` (ใช้ทั้ง 3 วัน — Day 2/3 commit เพิ่มเข้า repo เดียวกัน) ส่ง:

| ไฟล์ | คะแนน | เกณฑ์ผ่าน |
|------|-------|-----------|
| `screenshots/01-ollama-run.png` | 3 | เห็น `ollama run` + คำตอบจริงจาก model |
| `screenshots/02-llmgoat-chat.png` | 3 | เห็น `localhost:5001` + Billy ตอบ chat ≥ 1 รอบ |
| `screenshots/03-aider-via-ollama.png` ⭐ | 4 | Aider create ไฟล์จริงผ่าน Ollama (`Applied edit`) |
| `owasp-notes.md` | 2 | 3 ข้อจาก OWASP LLM Top 10 + อธิบาย ≥ 1 ประโยค/ข้อ ว่าเกี่ยวกับงานคุณยังไง |
| `troubleshooting.md` | 2 | ระบุปัญหา ≥ 2 อย่างที่เจอ + วิธีแก้ |
| `questions.md` | 1 | ≥ 2 คำถามค้างใจสำหรับ Day 2 |
| **รวม** | **15** | |

⏰ **Deadline:** ศุกร์ 1 พ.ค. 2569 23:59
📨 **ส่งที่:** hero.megawiz.co.th/ai-devsecops → การบ้าน Day 1

ถ้าติดตรงไหน อย่าลังเลที่จะถามใน Discord/Line group ได้ตลอดครับ

## 🤝 ชวนเพื่อนเข้าร่วม Day 2 + Day 3 ได้นะครับ

Workshop นี้ออกแบบให้คนที่เข้ามาช่วงกลางคันก็ตามทันได้ — Day 2 (Red Team) และ Day 3 (Blue Team) เป็นเนื้อหาแยกค่อนข้างชัด ไม่ได้พึ่ง Day 1 มาก

ถ้ามีเพื่อนสนใจด้าน:
- **Pentest / AI Red Teaming** → แนะนำ **Day 2** (Prompt Injection, Data Leakage, Insecure Output Handling)
- **SOC / SIEM / Incident Response** → แนะนำ **Day 3** (Wazuh + AI-assisted threat detection + auto-report)

ลงทะเบียนได้ที่ → **https://hero.megawiz.co.th/ai-devsecops**

(ฟรี — ไม่ต้องใช้ AI API key เพราะใช้ Ollama รันในเครื่องตัวเอง)

เจอกัน Day 2 — เสาร์ 2 พ.ค. 2569 🔴 Red Team Operations

---

Paripol
Instructor — AI Automation DevSecOps Workshop
paripol@megawiz.co
