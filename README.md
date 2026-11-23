# PSD_eNGINNo 3D-iFM · Summer LaSalle (V2.0)

ระบบ IFM แบบ 3D สำหรับกลุ่มธุรกิจ **Workplace (BHIRAJ BURI)** เริ่มจากโครงการแรก **SLS – Summer LaSalle**
(5 อาคารหลัก: Campus A · B · C · Carpark P1 · P2 → อาคารย่อย A1–A6 · B1–B6 · C1 C5 C6 = 17 อาคาร · 60 ชั้น · 318 พื้นที่/Unit)

| ไฟล์ | ใช้ทำอะไร |
|---|---|
| `index.html` | เว็บแอป Desktop (หลังบ้าน) — Campus 3D, 3D-iFM Workplace, IoT, ทุก Service |
| `PSD_eNGINNo_3D-iFM_Mobile.html` | แอปมือถือ (ช่าง / แม่บ้าน / รปภ. / คนสวน / งานผ้า) — บัญชีเดียวกัน |
| `plans/SLS/*.png` | ผังพื้น 60 ชั้น (แปลงจากแบบ XREF) — **ต้องอยู่โฟลเดอร์เดียวกับ index.html** |
| `Code.gs` | Google Apps Script V2.0 (แทนไฟล์เดิมทั้งหมด) |
| `PSD_eNGINNo_3D-iFM_SLS_Database_Template.xlsx` | แม่แบบฐานข้อมูล Excel ทุกตาราง (กรอก Unit SLS 318 รายการ + IoT 5 จุดไว้แล้ว) |
| `iot/ESP32_3D-iFM_IoT.ino` | ตัวอย่างโค้ดอุปกรณ์ IoT (ESP32) ทั้ง 5 ประเภท |

---

## 1. ติดตั้ง (ครั้งเดียว)

**Google Apps Script**
1. เปิด Apps Script เดิม → วาง `Code.gs` ใหม่แทนทั้งไฟล์ → บันทึก
2. เลือกฟังก์ชัน `setup` → Run (รันซ้ำได้ ไม่ลบข้อมูลเดิม) → อนุญาตสิทธิ์ (Sheets / Drive / ส่งอีเมล)
   - Execution log จะแสดง: API Key, รหัส admin (ครั้งแรก), Spreadsheet ของ SLS และ **IoT Key (SLS)**
   - ถ้าเคยติดตั้ง V1 แล้ว ให้รัน `setupWorkplace` เพิ่มอีกครั้งเดียว
3. แก้อีเมลใน `setAdminEmail()` เป็นอีเมล @bhirajburi.co.th ของผู้ดูแล แล้วรัน (admin จะใช้ OTP ได้ด้วย)
4. Deploy → Manage deployments → Edit → **New version** (ครั้งแรก: New deployment · Web app · Execute as **Me** · Who has access **Anyone**)
5. (แนะนำ) Project Settings → Script properties: `ALERT_EMAILS` = อีเมลรับแจ้งเตือน IoT (คั่นด้วย ,)

**เว็บ (GitHub Pages)** — อัปโหลด `index.html`, `PSD_eNGINNo_3D-iFM_Mobile.html` และโฟลเดอร์ `plans/` ไว้ที่เดียวกัน
แล้วส่งลิงก์ `https://<user>.github.io/<repo>/?srv=<Web App URL>` ให้ผู้ใช้ เปิดครั้งแรกระบบเชื่อมเซิร์ฟเวอร์ให้อัตโนมัติ

---

## 2. การเข้าใช้งาน — ระบบเชิญเท่านั้น (Invite-only, V2.11 · Code.gs V2.1)

1. **Admin เพิ่มอีเมลก่อน**: เมนู Management › Users & Roles
   - **＋ เพิ่มผู้ใช้ (อีเมล)**: ใส่ชื่อ อีเมล @bhirajburi.co.th ตำแหน่ง อาคาร และ Module (ไม่ต้องตั้ง Username / Password)
   - **📋 เพิ่มหลายอีเมล**: วางรายชื่อจาก Excel ได้ บรรทัดละ 1 คน
2. ระบบส่งอีเมลแจ้งสิทธิ์ให้ผู้ใช้อัตโนมัติ (ตั้งลิงก์แอปในอีเมลด้วยฟังก์ชัน `setAppUrl`)
3. **ผู้ใช้เข้าสู่ระบบ**: ใส่อีเมล → รับรหัส 6 หลักทางอีเมล → ติ๊ก “จำเครื่องนี้” = ไม่ต้อง Login ใหม่ 1 ปี
4. อีเมลที่ไม่มีในรายชื่อ หรือถูกระงับ ระบบจะ**ไม่ส่งรหัส** และตอบข้อความเดียวกันทุกกรณี จึงเดาไม่ได้ว่าอีเมลไหนมีบัญชี
5. ตั้งผู้ใช้เป็น **ระงับ** หรือ **ลบ** = ตัดสิทธิ์ทุกเครื่องทันที (มีผลในคำขอถัดไปไปยังเซิร์ฟเวอร์)
6. ปิดการขอสิทธิ์เองและการสมัครด้วยรหัสผ่านแล้ว (เปิดคืนได้ด้วย Script property `ALLOW_SIGNUP=true`)
7. API Key **ไม่ได้ให้สิทธิ์ Admin แล้ว** (เปิดคืนได้ด้วย `ALLOW_API_KEY=true`)
8. แนะนำเมื่อ Admin ตั้งอีเมลด้วย `setAdminEmail` แล้ว:
   - รัน `lockPasswordLogin` เพื่อปิด Login รหัสผ่านของ admin ให้เหลือแต่อีเมล + OTP
   - รัน `securityStatus` เพื่อตรวจการตั้งค่าความปลอดภัย

**ตรวจว่า Web App เป็นโค้ดใหม่หรือไม่:** เปิดลิงก์ `/exec` ใน browser ต้องเห็น `"version":"2.1"`
ถ้าแอปขึ้น “ประเภทธุรกิจไม่ถูกต้อง: workplace” แปลว่าลิงก์นั้นยังเป็น Code.gs รุ่นเก่า

---

## 3. หน้าหลักที่เพิ่มใหม่

- **🌐 หน้า 3D-iFM (หน้าหลัก)** — เปิดแอปมาเป็นหน้า 3D เต็มจอ (Campus 3D + ผังชั้น + Work Request / IoT / แจ้งเตือนด้านขวา) · ปุ่ม **🗂 หลังบ้าน** สลับไปหน้าจัดการ · ในหลังบ้านกดปุ่ม **🌐 3D** (แถบบน) เพื่อกลับหน้า 3D
- แอปนี้ใช้เฉพาะกลุ่มธุรกิจ **Workplace** (ตัดกลุ่ม Hospital / Factory / Hotel / Exhibition / Office / Restaurant ออก)
- **🌐 3D-iFM Workplace** — แผนที่ 3D กลุ่มอาคาร SLS · BTB · BTE · CLS · UBC-II · **＋ Add Building** (Admin) กำหนดชื่อ ประเภท จำนวนชั้น ตำแหน่ง สี และติ๊กบริการของอาคาร พร้อมสร้าง Spreadsheet ให้ทันที
- **🏢 Building Critical Status · Campus 3D** — 17 อาคารของ SLS สีตามสถานะ 4-Ring รายชั้น · คลิกชั้น → ผังจริงจากแบบ (3D/2D) + Unit + หมุด 4-Ring + หมุด IoT
- **📡 IoT Monitoring** — ระดับน้ำประปา · IAQ (Temp/Humi/PM2.5/CO₂) · มิเตอร์ไฟ MDB · มิเตอร์ AC · น้ำมัน Generator · กราฟ 24 ชม. · เกินเกณฑ์ = ขึ้นวง HARD ในผัง 4-Ring + **สร้าง Work Request P1/P2 อัตโนมัติ** + อีเมลแจ้งเตือน
- **📊 Excel Database** (Reports & Data / Settings ในมือถือ) — แม่แบบว่าง · Export ทั้งหมด · Import (รวมตาม id หรือแทนที่ตาราง) → ซิงค์ขึ้น Google Sheets อัตโนมัติ

---

## 4. IoT — ติดตั้ง 5 ชุดใน Hard Service

| ID | อุปกรณ์ | ค่าที่ส่ง | เกณฑ์เริ่มต้น |
|---|---|---|---|
| IOT-001 | 💧 ระดับน้ำประปา | `level` %, `vol` m³ | < 30% เตือน · ≤ 15% วิกฤต |
| IOT-002 | 🌡 IAQ | `temp`, `humi`, `pm25`, `co2` | CO₂ > 1000 / ≥ 1500 · PM2.5 > 37.5 / ≥ 75 · Temp > 27 / ≥ 30 |
| IOT-003 | ⚡ มิเตอร์ MDB | `kw`, `kwh`, `v`, `a`, `pf` | V 370–420 · PF ≥ 0.85 |
| IOT-004 | ❄ มิเตอร์ AC | `kw`, `kwh`, `a`, `run` | — |
| IOT-005 | 🛢 น้ำมัน Generator | `fuel` %, `liters`, `run`, `batt`, `hours` | < 40% เตือน · ≤ 25% วิกฤต · แบต < 24 V |

ปรับเกณฑ์รายเครื่องได้ที่คอลัมน์ `th` เช่น `{"co2":{"hi":900,"hihi":1400}}` · ย้ายตำแหน่งหมุดด้วยค่า `x`,`y` (พิกัดบนผัง 900 × สูงผัง) และ `floor` (เช่น `A1F2`)
อุปกรณ์ส่ง HTTPS POST ไปที่ Web App URL (ดู `iot/ESP32_3D-iFM_IoT.ino`) · หลายจุดผ่าน Gateway ส่ง `readings:[…]` ได้ครั้งละ 20 รายการ

---

## 5. ความปลอดภัย (ทำแล้วใน Code.gs V2.0)

- OTP 6 หลัก: เก็บเป็น HMAC ใน Cache 10 นาที · ใช้ครั้งเดียว · ผิดได้ 5 ครั้ง · ขอได้ 3 ครั้ง/15 นาที/อีเมล · เพดาน 400 ฉบับ/วัน
- จำกัดเฉพาะโดเมน `@bhirajburi.co.th` (เปลี่ยนได้ที่ Script property `ALLOWED_DOMAIN`)
- Token แบบ HMAC-SHA256 · ถอนทุกเครื่องได้ (เปลี่ยนรหัส / ระงับบัญชี / `logoutAll`) · ผู้ใช้ทั่วไปใช้รหัสผ่านไม่ได้ (เปิดได้ด้วย `ALLOW_PW_LOGIN=true`)
- สิทธิ์แยกตามบทบาท / อาคาร / Module ตรวจที่ฝั่งเซิร์ฟเวอร์ทุกคำขอ · Audit log ทุกการกระทำ
- IoT Key แยกต่ออาคาร (หมุนเปลี่ยนได้) · จำกัด 1 ค่า/8 วินาที/อุปกรณ์ · รับเฉพาะตัวเลข · ใช้ Lock กันเขียนชนกัน
- จำกัดขนาดคำขอ 8 MB · กัน Formula injection ใน Google Sheets (`= + - @`)

**แนะนำเพิ่มเติม:** ไม่แชร์ Spreadsheet / โฟลเดอร์ Drive ให้ผู้ใช้โดยตรง (ให้เข้าผ่านแอปเท่านั้น) · ใช้บัญชี Google Workspace ขององค์กรเป็นเจ้าของ Script · หมุน API Key / IoT Key ทุก 6 เดือนหรือเมื่อพนักงานลาออก · ใช้ Root CA ในอุปกรณ์ IoT แทน `setInsecure()` · ถ้าต้องการกันบอทระดับสูง ให้วาง Cloudflare Worker เป็น proxy หน้า Web App (Rate-limit ตาม IP)

---

## 6. ข้อมูลตั้งต้นที่ต้องตรวจ / ปรับ

- **ชื่อ Unit** สร้างอัตโนมัติจากเส้นผนังในแบบ (`A1-101` = อาคาร A1 ชั้น 1 ห้องที่ 01) — แก้เป็นเลขห้องจริง / ชื่อผู้เช่า ได้ใน Excel ชีต `areas` แล้ว Import
- **พื้นที่ (ตร.ม.) เป็นค่าประมาณ** (แบบ PDF ไม่มีมาตราส่วน) — กรอกค่าจริงใน Excel
- A6 ชั้น 1 แบ่ง Unit ด้วยเส้นประ ระบบจึงรวมเป็นห้องเดียว — แยกได้ที่ “✏ แก้ไขผังห้อง”
- ตำแหน่ง **MDB Room / Generator Room / Water Tank & Pump Room** วางไว้ที่ Carpark P1-G / P2-G เป็นค่าสมมติ — แก้ใน Area และย้ายหมุด IoT ให้ตรงหน้างาน
- ตำแหน่งอาคารในผัง Campus 3D เป็นค่าจัดวางเบื้องต้น (ไม่มีผัง Site plan) · จำนวนชั้นของ BTB / BTE / BCS / UBC-II เป็นค่าตั้งต้น — แก้ที่ ✏ ในหน้า 3D-iFM Workplace
- โหมด **🧪 ข้อมูลตัวอย่าง** มีงาน/เหตุการณ์จำลองไว้สาธิต — เปลี่ยนเป็น **✅ ข้อมูลจริง** ที่ Settings ก่อนใช้งานจริง
