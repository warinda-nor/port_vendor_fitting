# Vendor Business Review — Tableau Extension

Dashboard extension สำหรับดูภาพรวมการขายแยกตาม Vendor แต่ละราย (Net Sales, Sale Qty, AOV, SKU Count, Sales Trend, Sales by Brand/Channel/Product Group/Universe/Sales Office, Top 10 Products) เทียบปีปัจจุบันกับปีก่อนหน้า

## โครงสร้างไฟล์

```
vendor-overview/
  index.html           ไฟล์หลักของ extension (HTML + CSS + JS ในไฟล์เดียว)
  VendorOverview.trex   ไฟล์ manifest สำหรับให้ Tableau รู้จัก extension นี้
tableau.extensions.1.latest.js   Tableau Extensions API (index.html เรียกใช้ไฟล์นี้)
Dashboard Vendor Fitting.png     ภาพ mockup อ้างอิงสำหรับ layout
```

> **หมายเหตุ:** ไฟล์ `data form ds new.xlsx` และ `Data mapping.xlsx` (ข้อมูลขายจริง) ถูกกันไว้ใน `.gitignore` ไม่ได้ขึ้น GitHub เพราะเป็นข้อมูลภายในและไม่จำเป็นต่อการรันตัว extension

> **`index.html` ไม่มีข้อมูลตัวอย่าง/mock data ฝังอยู่เลย** — ไฟล์นี้จะแสดงผลได้ก็ต่อเมื่อรันอยู่ภายใน Tableau dashboard จริงเท่านั้น (ดึงข้อมูลสดจาก worksheet ตามสเปกในข้อ 3) ถ้าเปิดไฟล์ตรงๆด้วยเบราว์เซอร์จะเห็นแค่กรอบ layout เปล่าๆ กับข้อความแจ้งว่าต้องเปิดผ่าน Tableau — ใช้เช็คแค่โครงสร้าง/การจัดวางเท่านั้น ไม่มีตัวเลขให้ดู

---

## 1) เช็คโครงสร้าง Layout (ยังไม่ต้องต่อ Tableau)

เปิดไฟล์ `vendor-overview/index.html` ตรงๆ ด้วยเบราว์เซอร์ (ดับเบิลคลิก หรือลากไฟล์เข้าเบราว์เซอร์) — จะเห็นกรอบการ์ดเปล่าๆ ตามตำแหน่ง/ขนาดจริงของ layout พร้อมข้อความ "This extension only renders inside a Tableau dashboard." เพื่อยืนยันว่าไฟล์โหลดไม่มี error

ใช้หน้านี้เช็คแค่การจัดวาง/สัดส่วนของ card เท่านั้น — ถ้าต้องการดูข้อมูลจริงต้องเปิดผ่าน Tableau ตามข้อ 3

---

## 2) Deploy ขึ้น GitHub Pages

ขั้นตอนนี้ทำครั้งเดียวเพื่อให้ Tableau (ซึ่งต้องโหลด extension จาก URL แบบ `https://`) เข้าถึงไฟล์ `index.html` ได้

1. เข้า repo บน GitHub: `https://github.com/warinda-nor/port_vendor_fitting`
2. ไปที่ **Settings → Pages**
3. ที่ **Source** เลือก **Deploy from a branch**
4. เลือก Branch เป็น **main** และ Folder เป็น **/ (root)** แล้วกด **Save**
5. รอ 1–2 นาที ให้ GitHub Pages build เสร็จ แล้วเข้าไปเช็คที่:
   ```
   https://warinda-nor.github.io/port_vendor_fitting/vendor-overview/index.html
   ```
   ถ้าเห็น dashboard ตัวอย่างขึ้นมา แปลว่า deploy สำเร็จ

> URL ด้านบนต้องตรงกับค่าที่อยู่ใน `vendor-overview/VendorOverview.trex` (แท็ก `<source-location><url>`) เป๊ะๆ — ถ้าเปลี่ยนชื่อ repo หรือ path ต้องแก้ในไฟล์ `.trex` ให้ตรงกันด้วย

---

## 3) ติดตั้งใช้งานใน Tableau Desktop

1. เปิด Tableau Desktop แล้วเปิด Dashboard ที่ต้องการใส่ extension
2. ลาก object **Extension** จากแผง Objects มาวางในตำแหน่งที่ต้องการ
3. เลือก **My Extensions → Access Local Extensions** แล้วเลือกไฟล์ `vendor-overview/VendorOverview.trex`
   (หรือถ้า deploy ผ่าน GitHub Pages แล้ว จะสามารถแชร์ไฟล์ `.trex` นี้ให้คนอื่นใช้ได้เลยโดยไม่ต้องมีไฟล์ index.html อยู่ในเครื่อง เพราะ extension จะไปโหลดจาก URL บน GitHub Pages โดยตรง)
4. Dashboard ต้องมี **Parameter 2 ตัว** ชื่อ **`Start Date`** และ **`End Date`** (type: Date) วางเป็นตัวควบคุมช่วงวันที่บน Dashboard แทนการใช้ Filter รายเดือนแบบเดิม — extension จะอ่านค่าทั้งสองนี้ผ่าน Tableau Parameters API ตรงๆ (ไม่ได้อ่านจาก field ใน worksheet) เพื่อเอาไปคำนวณปี CY/LY และ label ของช่วงวันที่ที่แสดงบนหน้าจอ
5. Dashboard ต้องมี Worksheet ทั้งหมด **3 ตัว** ตามสเปกด้านล่าง — **ตั้งชื่อ Worksheet เป็นอะไรก็ได้ตามใจ** เพราะ extension จะดูจาก **field ที่มีอยู่ใน worksheet นั้นๆ** ไม่ได้ดูจากชื่อ — สำคัญคือ field ต้องครบตามสเปกของแต่ละ worksheet ด้านล่าง จะมี worksheet อื่นอยู่ในดาชบอร์ดเพิ่มเติม (เช่น sheet ไว้ทำ filter action) ก็ไม่กระทบ extension จะข้ามไปเอง
6. ใส่ Quick Filter สำหรับ field **Vendor Name** ไว้บน Dashboard (ตัวกรอง Vendor ต้องกรองแค่ worksheet "Trend" และ "Detail" เท่านั้น — **ห้ามกรอง** worksheet "AllVendors" เพราะต้องใช้ข้อมูลของ vendor ทุกรายเพื่อคำนวณ Rank/Share)

### Calculated Field ที่ต้องสร้างใน Tableau (CY/LY คำนวณสำเร็จรูปมาให้ extension เลย)

Net Inc Tax, Sales Qty และ %Margin ทุกตัวต้อง split เป็นคอลัมน์ CY (Current Year ตาม `Start Date`–`End Date`) กับ LY (ช่วงเดียวกันย้อนไป 1 ปี) แยกกัน แล้ว extension จะ sum แต่ละคอลัมน์ตรงๆ ไม่มีการคำนวณปีเองอีกต่อไป ต้องสร้าง field ชื่อตรงตัวดังนี้:

| Field ที่ต้องสร้าง | แนวคิดสูตร (ตัวอย่าง) |
|---|---|
| `Net Inc Tax - CY` | `IF [Time Date] >= [Start Date] AND [Time Date] <= [End Date] THEN [Net Inc Tax] END` |
| `Net Inc Tax - LY` | เหมือนกันแต่ใช้ `DATEADD('year', -1, [Start Date])` / `DATEADD('year', -1, [End Date])` |
| `Sales Qty - CY` / `Sales Qty - LY` | สูตรแบบเดียวกัน ใช้ `[Sale Qty]` |
| `%Margin - CY` / `%Margin - LY` | สูตรแบบเดียวกัน ใช้ field margin ที่มี (ตัว extension เก็บ field นี้ไว้เผื่อใช้ แต่ยังไม่มี UI แสดงในรอบนี้) |

**`Day Month`** — calculated field ใหม่ ใส่ใน worksheet "Trend" เท่านั้น เป็นวันที่แสดง format **`DD-DATENAME('month')`** (เช่น `1-April`) ใช้เป็นแกนเวลาของกราฟ Trend (extension จะ group ให้เป็นรายเดือนเองจากชื่อเดือน) — worksheet "Detail" กับ "AllVendors" **ไม่ต้องมี field วันที่เลย** เพราะ CY/LY คำนวณสำเร็จรูปมาในคอลัมน์แล้ว

### สเปก field ที่แต่ละ Worksheet ต้องมี

**Worksheet "Trend"** — grain รายวัน, กรองเหลือ Vendor เดียว (ไม่ต้องมี Article Id)

| Field ใน Tableau | ใช้ทำอะไร |
|---|---|
| `Day Month` | แกนเวลาของกราฟ Trend (format `DD-DATENAME('month')` เช่น `1-April`) |
| `Net Inc Tax - CY`, `Net Inc Tax - LY` | ยอดขาย (Net Sales) ปีปัจจุบัน/ปีก่อน |
| `Sales Qty - CY`, `Sales Qty - LY` | จำนวนขาย ปีปัจจุบัน/ปีก่อน |
| `Sls Ofc Desc` | Sales Office |

**Worksheet "Detail"** — grain รายปี (ต่อ Article), กรองเหลือ Vendor เดียว, ต้องมี Article Id — **ไม่ต้องมี field วันที่**

| Field ใน Tableau | ใช้ทำอะไร |
|---|---|
| `Article Id` | นับจำนวน SKU |
| `Article Name Th` | ชื่อสินค้าในตาราง Top 10 |
| `Product Group` | Sales by Product Group (อ่านค่าจาก field จริงตรงๆ ไม่ได้ derive จาก Mch1 Desc แล้ว) |
| `Product Group Mer` | อีก breakdown หนึ่งของ Product Group — สลับดูได้ผ่าน Toggle บนการ์ดเดียวกัน (ค่า default ตอนเปิดการ์ดคือ Product Group Mer) |
| `Mch2 Desc` | Sales by MCH2 (แสดงในการ์ด Vendor) |
| `Mc Desc` | คอลัมน์ "MC Desc" ในตาราง Top 10 |
| `Brand` | Sales by Brand |
| `Vendor Name` | ใช้แสดงชื่อ vendor ที่กำลังดู |
| `Universe` | Sales by Universe (เรียงลำดับคงที่ ECO > MASS > PREMIUM > LUXURY เสมอ ไม่เรียงตามยอดขาย) |
| `Sls Grp Desc` | Sales by Channel |
| `Net Inc Tax - CY`, `Net Inc Tax - LY` | ยอดขาย ปีปัจจุบัน/ปีก่อน |
| `Sales Qty - CY`, `Sales Qty - LY` | จำนวนขาย ปีปัจจุบัน/ปีก่อน |

**Worksheet "AllVendors"** — grain รายปี (ต่อ Vendor+Article), **ไม่กรอง** Vendor (ต้องเห็นทุก vendor) — **ไม่ต้องมี field วันที่**

| Field ใน Tableau | ใช้ทำอะไร |
|---|---|
| `Vendor Name` | คำนวณ Rank/Share ของ vendor ที่เลือก เทียบทุก vendor |
| `Article Id` | นับ SKU ของแต่ละ vendor เพื่อจัด Rank (SKU) |
| `Net Inc Tax - CY`, `Net Inc Tax - LY` | ยอดขาย ปีปัจจุบัน/ปีก่อน ของทุก vendor |
| `Sales Qty - CY`, `Sales Qty - LY` | จำนวนขาย ปีปัจจุบัน/ปีก่อน ของทุก vendor |

> ชื่อ field และชื่อ Parameter ต้องตรงกับในตาราง **เป๊ะๆ** (ตรงตามที่ประกาศไว้ในตัวแปร `FIELD`/`PARAM_START_DATE`/`PARAM_END_DATE` ท้ายไฟล์ `index.html`) ถ้าใน data source ใช้ชื่อคอลัมน์ต่างจากนี้ ให้แก้ค่าในตัวแปรเหล่านั้นให้ตรงกับ data source จริง

---

## ข้อจำกัดที่ควรรู้

- ช่วงวันที่ที่แสดง (เช่น "05 Jan–20 Jun") มาจากค่า Parameter `Start Date`/`End Date` ที่เลือกบน Dashboard ตรงๆ ไม่ได้อนุมานจากข้อมูล — กราฟ Trend เองยัง group แสดงผลเป็นรายเดือนเสมอ (roll up จาก `Day Month`) แม้ Parameter จะเลือกวันแบบไม่เต็มเดือนก็ตาม
- ข้อมูลต้นทาง (`data form ds new.xlsx`) ไม่มีฟิลด์ "สี" (Col Name) จึงไม่มีการ์ด "Sales by Color Group" ใน dashboard นี้
- ทุกครั้งที่แก้ `vendor-overview/index.html` แล้ว push ขึ้น GitHub ต้องรอ GitHub Pages build ใหม่ (ปกติ 1–2 นาที) ก่อนที่ Tableau จะเห็นเวอร์ชันล่าสุด — ถ้าไม่เห็นการเปลี่ยนแปลง ให้ลอง hard refresh หรือปิด-เปิด dashboard ใหม่
