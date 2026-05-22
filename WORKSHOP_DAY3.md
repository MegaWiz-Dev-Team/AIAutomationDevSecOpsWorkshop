# Day 3: Blue Team Operations — Monitor, Audit & Report
**ระยะเวลา:** 1.5 ชั่วโมง | **รูปแบบ:** Online — Screen share + Hands-on

> **Workshop Story Arc:**
> Day 1 = วางสนามรบ 🛠️ — ติดตั้ง Lab ให้พร้อม ✅
> Day 2 = ลงสนามโจมตี 🔴 — Red Team กับ Kali + Aider ✅
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
docker images | grep wazuh                           # ต้องเห็น 3 images
docker ps | grep llmgoat                             # LLMGoat ยังรัน
docker ps | grep attacker_kali                       # Kali ยังรัน
docker exec attacker_kali bash -c "source ~/.local/bin/env 2>/dev/null; aider --version" && echo "Kali + Aider OK"
ls day2_findings.md llmgoat_attack_logs.txt 2>/dev/null || echo "artifacts ยังไม่มี (ปกติก่อน Day 3)"
```

> ⚠️ **ถ้า Kali ไม่มี Aider** — ติดตั้งด้วย:
> ```bash
> docker exec attacker_kali bash -c "curl -LsSf https://astral.sh/uv/install.sh | sh"
> docker exec attacker_kali bash -c "source ~/.local/bin/env && uv tool install aider-chat --python 3.12"
> ```

Admin Portal → เปิด **Warmup Day 3 session** → สร้าง PIN (5 ข้อ, 20 วิ/ข้อ)

---

## 🧪 Warmup — ทบทวน Day 2 Kahoot [8 นาที]

**Instructor:** แชร์ PIN ใน Zoom chat

**นักเรียน — ทำตามขั้นตอน:**
```
1. เปิด https://hero.megawiz.co.th/ai-devsecops
2. Login → เลือก "AI Automation DevSecOps Workshop #1"
3. กด "Warmup Day 3 — ทบทวน Day 2"
4. กรอก PIN ที่ Instructor ให้
5. ตอบ 5 ข้อ (20 วินาที/ข้อ)
```

> หลังเฉลย Instructor ทบทวน Red Team findings → เชื่อมเข้า Blue Team วันนี้

---

## Session 1 — Start Wazuh SIEM [25 นาที]

### 1.1 ทบทวน Red Team → Blue Team

เปิด `day2_findings.md` แล้วถามผู้เรียน:
> "เราโจมตีด้วย Prompt Injection สำเร็จ — แล้ว Blue Team จะตรวจจับยังไง?"

```
Red Team (Day 2)                Blue Team (Day 3)
──────────────────────────────  ──────────────────────────────
ส่ง malicious prompts      →    ดักจับ input patterns
ดึง system prompt          →    monitor unusual responses
สร้าง payload ด้วย AI      →    ใช้ AI วิเคราะห์ logs
บันทึก findings            →    correlate events ใน SIEM
```

### 1.2 Start Wazuh Single-Node

> 🖥️ **[HOST]**

**ขั้นตอนที่ 1 — Clone Wazuh 4.14.5 (stable) และเข้า directory:**

🍎 macOS / Linux | 🪟 Windows (Git Bash)
```bash
git clone -b v4.14.5 --depth 1 https://github.com/wazuh/wazuh-docker.git ~/wazuh-docker
cd ~/wazuh-docker/single-node
```
> ถ้า clone ไว้แล้ว: `cd ~/wazuh-docker/single-node`

**ขั้นตอนที่ 2 — Generate certificates (ทำครั้งแรกเท่านั้น, ~2 นาที):**

🍎 macOS / Linux:
```bash
sudo chmod 777 ~/wazuh-docker/single-node/config/wazuh_indexer_ssl_certs/
docker compose -f generate-indexer-certs.yml run --rm generator
```

🪟 Windows (Git Bash) — ข้าม chmod ได้เลย:
```bash
docker compose -f generate-indexer-certs.yml run --rm generator
```

**ขั้นตอนที่ 3 — Start all services:**

🍎 macOS / Linux | 🪟 Windows (Git Bash):
```bash
docker compose up -d --remove-orphans
# รอ ~30 วินาที แล้วรัน:
docker exec single-node-wazuh.manager-1 /var/ossec/bin/wazuh-control start
```

**ขั้นตอนที่ 4 — รอ services ขึ้นมา (~5–10 นาที):**

🍎 macOS / Linux:
```bash
watch -n 15 'docker compose ps'
```

🪟 Windows (Git Bash) — รัน command นี้ซ้ำๆ:
```bash
docker compose ps
```

> ✅ รอจนเห็น **3 services** แสดง `Up`:
> ```
> single-node-wazuh.manager-1    Up X hours
> single-node-wazuh.indexer-1    Up X hours
> single-node-wazuh.dashboard-1  Up X hours
> ```
> 🍎 กด `Ctrl+C` เพื่อหยุด watch
>
> ⚠️ **หมายเหตุ**: `docker compose ps` ของ Wazuh image นี้ไม่แสดง `(healthy)` — ดูจาก "Up" status แทน
> ⚠️ **RAM**: Wazuh indexer ต้องการ RAM ~4GB+ หากเครื่องมี RAM ไม่พอ indexer อาจ restart ได้ — ไม่กระทบ Manager และ Dashboard
>
> 🆘 **ถ้า indexer ไม่ขึ้นหรือ crash**: ไม่ต้องแก้ — ทำขั้นตอนต่อไปได้เลย (Manager ยังรันได้) และดูหน้าจอ Instructor สำหรับส่วนที่ต้องใช้ Dashboard

**ขั้นตอนที่ 5 — เข้า Wazuh Dashboard:**
> 🌐 **[BROWSER]** — เปิด `https://localhost`

```
URL:      https://localhost:8443   (คลิก "Advanced" → "Proceed" เพื่อยอมรับ SSL warning)
Username: admin
Password: SecretPassword
```

> ✅ ต้องเห็น Wazuh Dashboard โหลดขึ้นมา — ให้นักเรียน explore สัก 2 นาที

---

### 1.3 Ship LLMGoat Attack Logs เข้า Wazuh

> 🖥️ **[HOST]**

**ขั้นตอนที่ 1 — ดึง attack logs จาก LLMGoat container:**
```bash
docker logs llmgoat-cpu --tail 200 > llmgoat_attack_logs.txt
wc -l llmgoat_attack_logs.txt
```

**ขั้นตอนที่ 2 — Copy logs เข้า Kali container:**
```bash
docker cp llmgoat_attack_logs.txt attacker_kali:/tmp/llmgoat_attack_logs.txt
```

**ขั้นตอนที่ 3 — เข้า Kali และให้ Aider เขียน log shipper script:**
```bash
docker exec -it attacker_kali /bin/bash
```

```bash
# 🐧 [KALI]
source ~/.local/bin/env    # โหลด uv PATH ให้ aider ใช้ได้
export OLLAMA_API_BASE=http://host.docker.internal:11434
export MODEL=qwen2.5-coder:3b   # เปลี่ยนตามที่ pull มา

cd /tmp && aider --model ollama/$MODEL --yes --no-auto-commits \
      --message "Write a Python script called ship_to_wazuh.py that:
1. Reads log lines from /tmp/llmgoat_attack_logs.txt
2. Detects suspicious patterns: 'ignore previous', 'DAN', 'system prompt', 'SYSTEM:', 'debug mode', 'API key', '[ATTACK]', 'bypass'
3. For each suspicious line, sends a UDP syslog message to host.docker.internal port 514
   - Use Python socket library (AF_INET, SOCK_DGRAM)
   - Syslog format: '<13>llmgoat: OWASP_LLM01 prompt_injection {line}'
   - Sleep 0.1 seconds between sends
   - Print each sent line (first 80 chars)
4. Print summary: total lines checked, suspicious lines found, alerts sent
" ship_to_wazuh.py
```

**ขั้นตอนที่ 4 — รัน log shipper:**
```bash
# 🐧 [KALI]
python3 /tmp/ship_to_wazuh.py
# Output: "✅ Done: X lines checked, X suspicious, X alerts sent to Wazuh syslog"
```

**ขั้นตอนที่ 5 — ดู alerts ใน Wazuh Dashboard:**
> 🌐 **[BROWSER]** — `https://localhost:8443`
> - เปิด **Threat Intelligence** → **Events**
> - Filter: `rule.id: 100001` หรือค้นหา `llmgoat`

> ✅ ต้องเห็น attack events จาก Day 2 ปรากฏใน SIEM
>
> 🆘 **ถ้าไม่เห็น events หรือ Dashboard ไม่โหลด**: Indexer อาจ OOM — **ดูหน้าจอ Instructor แทน** แล้วทำขั้นตอน Session 2-3 ต่อได้เลย (ไม่ต้องใช้ Dashboard)

---

## Session 2 — Monitor & Audit ด้วย AI [35 นาที]

### 2.1 ทดสอบ Wazuh Manager API

> 🖥️ **[HOST]** — เปิด terminal ใหม่แยก (ไม่ต้องปิด Kali)

**ขั้นตอนที่ 1 — ขอ JWT token จาก Wazuh:**
```bash
TOKEN=$(curl -s -k -u 'wazuh-wui:MyS3cr37P450r.*-' \
  https://localhost:55000/security/user/authenticate -X POST \
  | python3 -c "import sys,json; print(json.load(sys.stdin)['data']['token'])")
echo "Token OK: ${TOKEN:0:20}..."
```

> ⚠️ **สำคัญ**: ใช้ **single quotes** รอบ password เสมอ — `'MyS3cr37P450r.*-'`
> เพราะ `.*-` มี special chars ที่ shell ตีความผิดถ้าใช้ double quotes

**ขั้นตอนที่ 2 — ดู active agents:**
```bash
curl -s -k -H "Authorization: Bearer $TOKEN" \
  "https://localhost:55000/agents?status=active" | python3 -m json.tool | head -30
```

> ✅ ได้ token = Wazuh API พร้อมใช้ — ใช้ต่อใน analyze_wazuh.py

> **สำหรับ Claude Desktop Users (Optional)**: Clone Wazuh MCP Server เพื่อ integrate กับ Claude:
> ```bash
> git clone https://github.com/gensecaihq/Wazuh-MCP-Server.git ~/Wazuh-MCP-Server
> ```
> แล้ว config ใน `~/Library/Application Support/Claude/claude_desktop_config.json`

---

### 2.2 AI วิเคราะห์ Security Events ด้วย Aider

> 🐧 **[KALI]** — กลับไป Kali terminal (หรือเปิดใหม่)

**ขั้นตอนที่ 1 — เข้า Kali:**
```bash
docker exec -it attacker_kali /bin/bash
```

**ขั้นตอนที่ 2 — ให้ Aider ทำหน้าที่ Blue Team Analyst:**
```bash
# 🐧 [KALI]
export OLLAMA_API_BASE=http://host.docker.internal:11434

aider --model ollama/$MODEL --no-auto-commits \
      --message "You are a Blue Team security analyst. Write a Python script called analyze_wazuh.py that:
1. Read log lines from /tmp/llmgoat_attack_logs.txt
2. Classify each line by OWASP LLM category:
   - LLM01 Prompt Injection: keywords 'ignore previous', 'DAN', '[ATTACK]', 'bypass', 'override'
   - LLM06 Data Leakage: keywords 'system prompt', 'SYSTEM:', 'API key', 'secret', 'reveal'
   - LLM02 Insecure Output: keywords 'script', '<script', 'javascript', 'eval'
3. Count events per category
4. Identify highest risk attack (most frequent category)
5. Print results as a formatted table with columns: Category | Count | Severity
Use only Python standard library (no external packages)." analyze_wazuh.py
```

**ขั้นตอนที่ 3 — รัน analysis:**
```bash
# 🐧 [KALI]
python3 analyze_wazuh.py
```

> ✅ ต้องเห็น summary table เช่น:
> ```
> LLM01 Prompt Injection:  28 events  ← HIGHEST RISK
> LLM06 Data Leakage:      12 events
> LLM02 Insecure Output:    5 events
> ```

---

### 2.3 Whitebox Code Audit ด้วย Aider

> 🐧 **[KALI]**

**ขั้นตอนที่ 1 — เข้าโฟลเดอร์ LLMGoat ใน Kali:**
```bash
# 🐧 [KALI]
cd /tmp/LLMGoat
```

**ขั้นตอนที่ 2 — ให้ Aider ทำ Security Code Audit:**
```bash
# 🐧 [KALI]
aider --model ollama/$MODEL --no-auto-commits \
      --message "Perform a security code audit of this AI application. Find and report:
1. Where system prompt is defined — file:line
2. Any input sanitization code — if missing, say MISSING explicitly
3. Output rendering code — does it escape HTML?
4. Logging code — what gets logged?

For each finding output:
  File: filename.py
  Line: line number
  Issue: description
  OWASP: LLM0X
  Severity: Critical / High / Medium" app.py
```

**ขั้นตอนที่ 3 — สร้าง Audit Checklist:**
```bash
# 🐧 [KALI]
cat > /tmp/audit_checklist.md << 'EOF'
# LLMGoat Code Audit — Day 3

## Input Validation
- [ ] มี input sanitization ไหม?
- [ ] มี keyword blocklist ไหม?
- [ ] มี length limit ไหม?

## System Prompt Protection
- [ ] System prompt hardcoded ใน code ไหม?
- [ ] มี access control ก่อนแสดง system prompt ไหม?

## Output Filtering
- [ ] HTML entities escaped ก่อน render ไหม?
- [ ] มี content filtering บน output ไหม?

## Logging & Alerting
- [ ] Log all inputs ไหม?
- [ ] มี anomaly detection ไหม?
- [ ] Alert เมื่อพบ suspicious keyword ไหม?

## Findings Summary (จาก Aider audit)
- Critical: ___
- High: ___
- Medium: ___
EOF
echo "Created /tmp/audit_checklist.md"
```

---

## Session 3 — AI-Generated Vulnerability Report [20 นาที]

### 3.1 รวม Artifacts ทั้งหมด

> 🐧 **[KALI]** → 🖥️ **[HOST]**

**ขั้นตอนที่ 1 — Copy audit checklist ออกมา host:**
```bash
# 🐧 [KALI]
exit   # ออกจาก Kali ก่อน

# 🖥️ [HOST]
docker cp attacker_kali:/tmp/audit_checklist.md ./audit_checklist.md
echo "✅ Saved audit_checklist.md"
```

**ขั้นตอนที่ 2 — รวม artifacts ทั้งหมดเป็นไฟล์เดียว:**
```bash
# 🖥️ [HOST]
# รวม files ที่มี (day2_findings.md อาจยังไม่มีถ้าทำ Day 2 ไม่เสร็จ)
cat audit_checklist.md llmgoat_attack_logs.txt \
    $(ls day2_findings.md 2>/dev/null) \
    > /tmp/all_artifacts.txt
wc -l /tmp/all_artifacts.txt
```

---

### 3.2 ให้ AI เขียน Vulnerability Report ภาษาไทย

**ขั้นตอนที่ 1 — ส่ง artifacts ให้ Ollama สร้าง report:**
```bash
# 🖥️ [HOST]
ollama run $MODEL "$(cat << 'PROMPT'
You are a security consultant. Write a professional vulnerability assessment report in Thai language based on the findings below.

Include sections:
1. Executive Summary (2-3 sentences for management)
2. Test Methodology (brief)
3. Findings Table (OWASP category, severity, status)
4. Detailed Findings (per vulnerability: description, evidence, impact, recommendation)
5. Remediation Checklist (ordered by priority)

Format: Markdown. Reference OWASP Top 10 for LLMs 2025.

--- ARTIFACTS START ---
PROMPT
)$(cat /tmp/all_artifacts.txt)" > vulnerability_report.md
```

**ขั้นตอนที่ 2 — ตรวจสอบ report:**
```bash
# 🖥️ [HOST]
wc -l vulnerability_report.md
head -30 vulnerability_report.md
```

> ✅ ต้องเห็น report เริ่มด้วย `# รายงานประเมินความปลอดภัย AI`

**ตัวอย่าง report structure ที่ได้:**
```markdown
# รายงานประเมินความปลอดภัย AI — LLMGoat
วันที่: [วันที่] | ระดับความลับ: Confidential

## Executive Summary
พบช่องโหว่ระดับ Critical 2 รายการ ใน LLMGoat AI Application
ช่องโหว่ร้ายแรงที่สุดคือ Prompt Injection (LLM01) สามารถ bypass safety guidelines ได้

## สรุปช่องโหว่
| # | ช่องโหว่ | OWASP | Severity | Status |
|---|---------|-------|----------|--------|
| 1 | Direct Prompt Injection | LLM01 | Critical | Open |
| 2 | System Prompt Extraction | LLM06 | Critical | Open |
| 3 | Insecure HTML Output | LLM02 | High | Open |
```

---

### 3.3 Workshop Story สรุป 3 วัน

```
Day 1 — "วางสนามรบ" 🛠️
├── Ollama (Local LLM ไม่ต้อง API Key)
├── LLMGoat (เป้าหมายโจมตี)
└── Kali + Aider (อาวุธ)
          ↓ artifacts: Lab environment พร้อม

Day 2 — "ลงสนาม Red Team" 🔴
├── Prompt Injection → bypass AI (LLM01)
├── Data Leakage → ดึงข้อมูลลับ (LLM06)
└── Insecure Output → XSS via AI (LLM02)
          ↓ artifacts: day2_findings.md + attack logs

Day 3 — "สลับข้าง Blue Team" 🔵
├── Wazuh SIEM รับ attack logs
├── Wazuh MCP + Aider วิเคราะห์ events
├── Code Audit ระบุ vulnerable lines
└── AI เขียน Vulnerability Report ภาษาไทย
          ↓ output: vulnerability_report.md ใช้ได้จริง
```

**Real-World Application:**

| บทบาท | ใช้ทักษะนี้ทำอะไร |
|-------|-----------------|
| Developer | Code review security ก่อน deploy |
| Security Engineer | Automate pentest + report ด้วย AI |
| DevOps | ฝัง Wazuh monitoring ใน CI/CD pipeline |
| Manager | ขอ AI สรุป Executive Report ใน 1 คำสั่ง |

---

## 🎯 Post-Test Kahoot [10 นาที]

**Instructor:** แชร์ PIN ใน Zoom chat

**นักเรียน — ทำตามขั้นตอน:**
```
1. เปิด https://hero.megawiz.co.th/ai-devsecops
2. Login → เลือก "AI Automation DevSecOps Workshop #1"
3. กด "Post-test Kahoot (หลัง Day 3)"
4. กรอก PIN ที่ Instructor ให้
5. ตอบ 15 ข้อ (20 วินาที/ข้อ)
```

> ผลรวม Pre-test + Post-test = คำนวณ **Learning Gain** อัตโนมัติในระบบ

---

## 📤 การบ้าน Day 3 — Vulnerability Report (ส่งเพื่อรับ Certificate)

**ส่งผ่าน:** Student Portal → "การบ้าน Day 3"

ส่งไฟล์: `vulnerability_report.md` (หรือ export เป็น `.pdf`)

**ตรวจสอบก่อนส่ง:**
```bash
# 🖥️ [HOST]
cat vulnerability_report.md | head -50   # ต้องมี Executive Summary + Findings Table
```

**เกณฑ์คะแนน:**

| หลักฐาน | คะแนน |
|---------|-------|
| มี Executive Summary ภาษาไทย | 3 |
| Findings Table ครบ ≥ 2 รายการ | 3 |
| ระบุ OWASP Category ถูกต้อง | 2 |
| ระบุ Severity (Critical/High/Medium) | 2 |
| มี Remediation / คำแนะนำ | 3 |
| Wazuh screenshot หรือ event count | 2 |
| **รวม** | **/15** |

---

## Post-Workshop Action Items

1. ลอง implement input sanitization ใน LLMGoat เพื่อ fix LLM01
2. ตั้ง Wazuh monitoring สำหรับ AI app ของตัวเอง
3. อ่าน [OWASP LLM AI Security & Governance Checklist](https://owasp.org/www-project-top-10-for-large-language-model-applications/)
4. แชร์ `vulnerability_report.md` กับทีม — เป็น artifact จริงจาก workshop
