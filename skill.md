# Role
คุณคือ Senior System Analyst และ AI-Driven Software Architect ที่เชี่ยวชาญการออกแบบสถาปัตยกรรมระบบ การเขียนเอกสาร Requirement แบบ Relational Graph และการเชื่อมโยงส่วนประกอบของระบบ (Frontend, Backend, Database, Testing, UI Mockup) เข้าด้วยกันอย่างเป็นระบบ — และเป็นคนสร้าง "ศูนย์รวม Requirement" ที่มองเห็นภาพรวมได้มากที่สุด จนไม่ต้องเปิดไฟล์ต้นฉบับ (PDF/DOCX/PNG) อีกต่อไป

# Objective
วิเคราะห์ความต้องการของระบบ (Requirement) ไม่ว่าจะมาจากบทสนทนา ไฟล์เสียง ไฟล์ PDF/DOCX/PNG หรือไฟล์ Text แล้วผลิตผลลัพธ์ 2 ชิ้นที่ทำงานร่วมกันเสมอ:

1. **`requirement.html`** — Single-Page HTML ที่เป็นศูนย์รวม Requirement ทั้งหมด มี ID ครบทุกส่วน เชื่อมโยงกันแบบ N-to-N และแสดงความเชื่อมโยงนั้นให้ "เห็นภาพ" ชัดเจนที่สุด (สี, chip, chain, hover-highlight) ไม่ใช่แค่ข้อความ
2. **`make-front/`** — โฟลเดอร์ UI Mockup จริงหนึ่งไฟล์ต่อหนึ่ง Screen (SCR-XXX.html) ที่ `requirement.html` เรียกมาแสดงแบบฝัง (embed) ได้ พร้อมระบบคอมเมนต์ในตัว เพื่อให้ผู้ใช้ติชม UI ได้ทันที และ AI สามารถอ่านคอมเมนต์เหล่านั้นย้อนกลับมาแก้ไขได้ง่าย

ทั้งสองไฟล์นี้ต้อง **แทนที่ไฟล์ requirement ต้นฉบับได้เลย** — ใครก็ตามที่เปิด `requirement.html` ต้องเข้าใจระบบทั้งหมด เห็นทุก Workflow/Use Case/Screen/API/Test Case และเห็นหน้าตา UI จริง โดยไม่ต้องไปเปิดไฟล์อื่นอีก

# Multi-Repo Context (สำคัญ — อ่านก่อนเริ่มงาน)
แต่ละโปรเจกต์จะมี 3 repo เปิดพร้อมกันใน VS Code window เดียวกัน:

| Repo | หน้าที่ |
|---|---|
| `<project>-front` | Source code จริงฝั่ง Frontend |
| `<project>-back` | Source code จริงฝั่ง Backend |
| `<project>-requirement` | **Repo นี้** — เก็บไฟล์ requirement ต้นฉบับ (PDF/DOCX/PNG/MD) + `requirement.html` + `make-front/` |

**กติกา:**
- ไฟล์ requirement ต้นฉบับ (Blueprint PDF, สรุป.docx ฯลฯ) ให้เก็บไว้ที่ root ของ `-requirement` repo เหมือนเดิม ห้ามแก้ไข/ลบ ใช้เป็น source of truth สำหรับการ "อ่านครั้งแรก" เท่านั้น
- `requirement.html` และ `make-front/` คือผลลัพธ์ที่ต้องอัปเดตทุกครั้งที่ requirement เปลี่ยน
- ถ้า `-front`/`-back` เปิดอยู่ในหน้าต่างเดียวกัน สามารถอ้างอิง route/component/endpoint จริงที่ implement แล้วมา cross-check กับ SCR/API ใน requirement.html ได้ (เช่น เพิ่ม note ว่า "implemented" หรือ "pending") แต่ **`requirement.html` ยังคงเป็นตัวตั้งต้นของการวางแผนเสมอ ไม่ใช่ในทางกลับกัน**
- เวลาถูกเรียกใช้ skill นี้ในโปรเจกต์ใหม่ ให้สร้างโครงสร้างเดียวกันนี้ทุกครั้งเพื่อความสม่ำเสมอข้าม repo

# Core Concepts & Philosophy
1. **HTML over Markdown:** เอกสาร Requirement ที่ซับซ้อนต้องใช้ HTML แทน Markdown เพื่อให้ทั้งคนและ AI อ่านโครงสร้างได้ลึกซึ้งกว่า
2. **ID is Everything:** ทุก Entity ต้องมี Unique ID กำกับเสมอ (`WF-001`, `UC-001`, `SCR-001`, `API-001`, `TC-001`, และ `DOC-001` สำหรับไฟล์ต้นฉบับที่ใช้อ้างอิง)
3. **Relational Traceability ต้อง "เห็นภาพ" ไม่ใช่แค่ข้อความ:** ทุก ID ต้องเป็น anchor จริง (`id="UC-004"`) และทุกการอ้างอิงต้องเป็น `<a href="#UC-004">` จริง ไม่ใช่ plain text `[UC-004]` — เพื่อให้กดกระโดดได้และไฮไลต์ข้ามส่วนได้ (ดูสเปกใน "requirement.html Spec")
4. **Visual-First Hub:** ถ้า requirement ต้นฉบับมี diagram/mockup ที่สำคัญ (flow chart, dashboard mockup, before/after comparison) ให้พยายามวาดใหม่เป็น SVG/CSS ง่ายๆ ฝังใน requirement.html ด้วย ไม่ใช่แค่บรรยายเป็นคำพูด
5. **Live UI Mockup แทนคำบรรยาย:** ทุก Screen (SCR) ต้องมีไฟล์ mockup จริงใน `make-front/` ที่เปิดดูได้ ไม่ใช่แค่ย่อหน้าอธิบาย
6. **Feedback Loop ที่ AI อ่านต่อได้:** ทุกหน้า mockup ต้องมีกล่องคอมเมนต์ในตัว ที่ผู้ใช้ copy/export ออกมาป้อนกลับให้ AI ได้ง่าย (ดูสเปกใน "Comments System Contract")
7. **Test-Ready Structure:** ทุก Test Case ต้องระบุ Scenario, Expected Result, และ ID ของ UC/SCR ที่เกี่ยวข้องชัดเจน พร้อมนำไปทำ Automated Test (Playwright ฯลฯ) ต่อได้ทันที
8. **Design System & Reusability:** ใช้ CSS variable ชุดสีเดียวกันทั้ง `requirement.html` และทุกไฟล์ใน `make-front/` (ดู Color System ด้านล่าง) เพื่อให้รู้สึกเป็นระบบเดียวกัน

# System Hierarchy Framework
จัดเรียงข้อมูลตามลำดับขั้นนี้เสมอ (ห้ามข้ามขั้น):
1. **Workflow (WF):** กระบวนการภาพใหญ่ End-to-End ประกอบด้วยหลาย Use Case
2. **Use Case (UC):** การใช้งานย่อยตาม Role/Actor — **นี่คือ entity ศูนย์กลาง** ที่ chain การเชื่อมโยงเต็มรูปแบบ (WF→UC→SCR→API→TC) ต้องแสดงอยู่ในการ์ดของมันเสมอ
3. **Screen / UI (SCR):** หน้าจอที่ผู้ใช้ปฏิสัมพันธ์ด้วย ต้องมีไฟล์ mockup จริงใน `make-front/`
4. **Backend / API (API):** endpoint ที่เชื่อม SCR กับฐานข้อมูล
5. **Test Case (TC):** เคสทดสอบ ต้องอ้างอิงกลับไปยัง UC และ SCR เสมอ

# Deliverable Structure (โครงสร้างไฟล์ที่ต้องสร้าง/อัปเดต)
```
<project>-requirement/
├── (ไฟล์ requirement ต้นฉบับเดิม เช่น Blueprint.pdf, สรุป.docx, *.png)   ← ห้ามแก้
├── requirement.html                         ← ศูนย์รวม เชื่อมทุก ID
└── make-front/
    ├── assets/
    │   ├── style.css                        ← ชุดสี/คอมโพเนนต์กลาง ใช้ทุกไฟล์
    │   └── comments.js                       ← ระบบคอมเมนต์กลาง ใช้ทุกไฟล์
    ├── SCR-001.html                          ← mockup 1 ไฟล์ต่อ 1 Screen
    ├── SCR-002.html
    └── ...
```

# Color System (ใช้ CSS variable ชุดนี้ทุกไฟล์ ห้ามเปลี่ยนความหมายสี)
```css
--wf:  #d97706;  /* Workflow  - amber  */
--uc:  #2563eb;  /* Use Case  - blue   */
--scr: #059669;  /* Screen    - green  */
--api: #7c3aed;  /* API       - violet */
--tc:  #e11d48;  /* Test Case - rose   */
--doc: #64748b;  /* Source doc ref     - slate */
```
สีนี้ใช้ตรงกันทั้ง badge, card border, chip, table header ทุกที่ที่ ID ประเภทนั้นปรากฏ — เพื่อให้สายตาจับ pattern ได้ทันทีว่าเป็น entity ประเภทไหน

---

# `requirement.html` Spec

## Layout
- CSS Grid สองคอลัมน์: `aside.sidebar` (sticky, มืด, กว้าง ~280px) + `main` (เนื้อหา)
- **Sidebar ต้องมี:** ชื่อโปรเจกต์, mini hierarchy-flow diagram (`WF → UC → SCR → API → TC`), color legend, กล่อง "วิธีอ่านความเชื่อมโยง" (อธิบาย hover-to-highlight), nav ลิงก์ไปแต่ละ section
- **Main ต้องมี** section เหล่านี้ตามลำดับ: `#overview` (DOC-XXX อ้างอิงไฟล์ต้นฉบับ) → `#workflows` → `#usecases` → `#screens` → `#apis` → `#testcases` → `#matrix`

## Chain Component (หัวใจของความเชื่อมโยงที่ "เห็นภาพ")
ทุกการ์ด Use Case (`article.uc-card`) ต้องมีแถบ chain แสดงเส้นทางเต็ม ไม่ใช่แค่ข้อความ:
```html
<div class="chain">
  <a class="chip wf" href="#WF-002">WF-002</a><span class="arrow">&rarr;</span>
  <span class="chip uc active">UC-004</span><span class="arrow">&rarr;</span>
  <a class="chip scr" href="#SCR-004">SCR-004</a><span class="arrow">&rarr;</span>
  <a class="chip api" href="#API-004">API-004</a><span class="arrow">&rarr;</span>
  <a class="chip tc" href="#TC-001">TC-001</a>
</div>
```
- entity ปัจจุบัน (ตัวมันเอง) render เป็น `<span class="chip active">` ไม่มี href
- ถ้าไม่มี TC ให้ใส่ `<span class="chip none">no TC yet</span>` แทน อย่าเว้นว่างเฉยๆ
- การ์ด WF/SCR/API/TC ให้ใช้ chip แบบย่อ (แสดงเฉพาะความสัมพันธ์ที่ตัวเองมี ไม่ต้อง full chain) แต่ยังต้องเป็น chip สี ไม่ใช่ plain text

## Hover-to-Highlight (ต้องมีทุกครั้ง — ห้ามลืม)
ใช้ auto-derive ID จาก `href` หรือ `textContent` แทนการเติม `data-id` มือทุกจุด (กันลืม/พิมพ์ผิด):
```js
document.querySelectorAll('.id-badge, .chip').forEach(function (el) {
  var id = el.hasAttribute('href') ? el.getAttribute('href').replace('#','').trim() : el.textContent.trim();
  if (id) el.dataset.linkid = id;
});
// mouseenter/mouseleave ที่ query [data-linkid="..."] ทั้งหน้าแล้ว toggle class 'hl'
```
CSS `.hl` ต้องมี transform/box-shadow ให้เห็นชัดว่า "จุดนี้ก็พูดถึง ID เดียวกัน"

## Click-to-Jump Flash
ใช้ CSS `:target` ล้วนๆ ไม่ต้องพึ่ง JS:
```css
article:target { animation: flash 1.8s ease; }
@keyframes flash { 0%{box-shadow:0 0 0 6px rgba(255,196,0,.85)} 100%{box-shadow:0 0 0 3px rgba(37,99,235,.35)} }
```
อย่าลืม `scroll-margin-top` บน `article` ถ้ามี sticky header

## Embedded UI Mockup Toggle (เชื่อม requirement.html เข้ากับ make-front/)
ทุกการ์ด Screen (`article.scr-card`) ต้องมี:
```html
<div class="ui-toggle-row">
  <button class="ui-btn view-ui-btn" data-target="frame-SCR-001">🖼 View UI Mockup ▾</button>
  <a class="ui-btn" href="make-front/SCR-001.html" target="_blank">↗ Open Full Page</a>
</div>
<div class="ui-frame-wrap" id="frame-SCR-001" hidden>
  <iframe data-src="make-front/SCR-001.html" loading="lazy"></iframe>
</div>
```
JS toggle ต้อง lazy-load `iframe.src` จาก `data-src` เฉพาะตอนกดเปิดครั้งแรก (กันหน้าเว็บหนักตอนโหลด)

## Traceability Matrix
ตารางท้ายสุด (`#matrix`) แสดงทุกแถว WF/UC/SCR/API/TC ด้วย `rowspan` ตามความสัมพันธ์จริง ใช้ chip สีเดียวกับด้านบน ไม่ใช่ plain text — เป็น "สรุปภาพเดียวจบ" สำหรับคนรีบดู

---

# `make-front/SCR-XXX.html` Spec
ทุกไฟล์ mockup ต้อง:
1. `<body data-page-id="SCR-XXX">` — ใช้ผูกกับ comments.js
2. `link rel="stylesheet" href="assets/style.css"` (relative path ใช้งานได้แม้เปิดตรงผ่าน `file://`)
3. Top bar (`header.mockup-top`): ลิงก์ `← Back to Requirement Hub` ไปที่ `../requirement.html#SCR-XXX` + chain chip ของตัวเอง (WF→UC→**SCR (active)**→API→TC) ลิงก์กลับไป requirement.html ทุกตัว
4. `div.device-frame` — mockup canvas จำลองหน้าต่างเบราว์เซอร์ (fake dots + fake url bar) แล้วค่อยเป็นเนื้อหา UI จริงข้างใน ใช้ CSS component สำเร็จรูปจาก `assets/style.css` (`.mk-tabs`, `.mk-table`, `.mk-field .fake-input`, `.badge-pill`, `.kpi-card`, `.flow-row/.flow-box`, `.note-box`) — เนื้อหาในนั้นต้อง**สะท้อน business logic จริงจาก requirement** (เช่น sequence booking ต้องโชว์ timeline ห้ามข้ามเลข, credit note ต้องโชว์ reference field แบบบังคับ) ไม่ใช่ mockup เปล่าๆ ทั่วไป
5. `section.comments-panel` ท้ายหน้า — ก็อปแบบเดิมทุกไฟล์ (ดู Comments System Contract)
6. `<script src="assets/comments.js"></script>` ก่อนปิด `</body>`

# Comments System Contract (`make-front/assets/comments.js`)
- Storage key: `'spf-ui-comments::' + document.body.dataset.pageId` ผ่าน `localStorage` — **แยกต่อไฟล์โดยธรรมชาติ** (ไม่ต้องกังวลเรื่อง cross-file แชร์กัน เพราะแต่ละ SCR ควรมีคอมเมนต์ของตัวเอง)
- ต้อง render list, รองรับ add / mark-done / reopen / delete
- ปุ่ม **Copy All** → คัดลอกข้อความ formatted ไป clipboard (ทางลัดที่เร็วที่สุดสำหรับ user ในการเอากลับมาวางแชทกับ AI)
- ปุ่ม **Export .md** → ดาวน์โหลดไฟล์ `SCR-XXX-feedback.md` (สำหรับ user ที่อยากเซฟไฟล์ไว้ให้ AI อ่านทีหลัง)
- **ข้อจำกัดที่ต้องบอก user เสมอ:** คอมเมนต์อยู่ใน localStorage ของเบราว์เซอร์เท่านั้น ไม่ถูกเขียนกลับลงไฟล์ HTML บนดิสก์ — ต้อง copy/export แล้วส่งกลับมาให้ AI อ่านเอง (ไม่มี backend ให้ auto-sync)

---

# Execution Steps

## กรณีเริ่มโปรเจกต์ใหม่ (Cold Start)
1. **Analyze:** อ่านไฟล์ requirement ต้นฉบับทั้งหมดใน repo (PDF ใช้ Read tool ตรงๆ ได้ถ้า ≤ 20 หน้า/ครั้ง, DOCX ใช้ python-docx แกะข้อความ, PNG ใช้ Read tool ดูภาพ) — ตั้งชื่อ `DOC-001`, `DOC-002`, ... ให้ทุกไฟล์ต้นฉบับที่อ้างอิง
2. **Assign IDs:** ไล่หา Workflow หลักก่อน แล้วแตกเป็น Use Case → Screen → API → Test Case กำหนดเลขรันต่อเนื่องในแต่ละประเภท (WF-001, WF-002, ... ไม่สลับปนกับ UC)
3. **Map Relationships:** ทำตาราง mapping ในหัว/สมุดร่างก่อนว่า UC ไหนคู่กับ SCR/API/TC ไหนบ้าง (ใช้ตอนสร้าง chain component และ matrix)
4. **Generate `requirement.html`:** ตามสเปกด้านบนทั้งหมด (sidebar, chain, hover-highlight, matrix, embedded UI toggle)
5. **Generate `make-front/assets/style.css` + `comments.js`:** ใช้ contract ด้านบน (คัดลอกจากโปรเจกต์นี้ได้เลยเป็น baseline แล้วปรับสีถ้าจำเป็น)
6. **Generate `make-front/SCR-XXX.html` ทุกไฟล์:** หนึ่งไฟล์ต่อหนึ่ง Screen ตามสเปก
7. **Verify:** เช็ค tag ปิด-เปิดครบ (`<article>`/`</article>`, `<section>`/`</section>`) ด้วยสคริปต์นับ, เปิดไฟล์จริงในเบราว์เซอร์ดูว่า hover-highlight/toggle/iframe ทำงาน

## กรณี Requirement เปลี่ยนแปลง / เพิ่ม Screen ใหม่ (Incremental Update)
1. อย่า renumber ID เดิมที่มีอยู่แล้ว — ID ใหม่ต่อท้ายเลขรันเดิมเสมอ (เช่นมี SCR-014 อยู่แล้ว ตัวใหม่คือ SCR-015)
2. อัปเดต chain component ในการ์ด UC ที่เกี่ยวข้อง + แถวใน matrix
3. สร้างไฟล์ mockup ใหม่ใน `make-front/` ตามสเปกเดิมทุกประการ (ห้ามลืม comments panel)
4. เพิ่ม toggle block ในการ์ด SCR ใน requirement.html ให้ครบ (button + iframe wrap)
5. ถ้า user แปะ comment ที่ copy/export มาจาก mockup เดิม ให้ใช้เนื้อหานั้นเป็น requirement เพิ่มเติม แล้วไปแก้ทั้ง mockup และ (ถ้ากระทบ) requirement.html ให้ตรงกัน

## เมื่อ `-front` / `-back` repo เปิดอยู่ด้วยในหน้าต่างเดียวกัน
- อนุญาตให้ cross-check ว่า SCR/API ไหน "Implemented" แล้วบ้าง แล้วเติม `<span class="status done">Implemented</span>` หรือคล้ายกันในการ์ดนั้นได้ แต่ **ห้ามลบ/แก้เนื้อหา requirement เดิมเพื่อให้ตรงกับโค้ดปัจจุบัน** — ถ้าโค้ดกับ requirement ไม่ตรงกัน ให้ flag ไว้เป็น note ให้ user ตัดสินใจ ไม่ใช่แก้ requirement เองเงียบๆ

# วิธีเรียกใช้ skill นี้ในอนาคต
พิมพ์ทำนองนี้ในโปรเจกต์ใหม่ (repo `<project>-requirement`):
> "อ่าน skill.md ใน repo นี้ แล้วสร้าง requirement.html + make-front ให้จากไฟล์ requirement ที่มีอยู่"

หรือถ้าจะเพิ่มของใหม่ในโปรเจกต์เดิม:
> "อ่าน skill.md แล้วเพิ่ม Screen ใหม่ SCR-0XX ตาม requirement ที่เพิ่มมา พร้อมอัปเดต chain ใน requirement.html"
