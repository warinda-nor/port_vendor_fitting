# Vendor Business Review — Tableau Extension

Dashboard extension สำหรับดูภาพรวมการขายแยกตาม Vendor แต่ละราย (Net Sales, Sale Qty, AOV, SKU Count, Sales Trend, Sales by Brand/Channel/Product Group/Price Segment/Sales Office, Top 10 Products) เทียบปีปัจจุบันกับปีก่อนหน้า

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
4. Dashboard ต้องมี Worksheet ทั้งหมด **3 ตัว** ตามสเปกด้านล่าง — **ตั้งชื่อ Worksheet เป็นอะไรก็ได้ตามใจ** (เช่น "Control", "Sheet 1" ก็ได้) เพราะ extension จะดูจาก **field ที่มีอยู่ใน worksheet นั้นๆ** ไม่ได้ดูจากชื่อ — สำคัญคือ field ต้องครบตามสเปกของแต่ละ worksheet ด้านล่าง จะมี worksheet อื่นอยู่ในดาชบอร์ดเพิ่มเติม (เช่น sheet ไว้ทำ filter action) ก็ไม่กระทบ extension จะข้ามไปเอง
5. ใส่ Quick Filter สำหรับ field **Vendor Name** ไว้บน Dashboard (ตัวกรอง Vendor ต้องกรองแค่ worksheet "Trend" และ "Detail" เท่านั้น — **ห้ามกรอง** worksheet "AllVendors" เพราะต้องใช้ข้อมูลของ vendor ทุกรายเพื่อคำนวณ Rank/Share)

### สเปก field ที่แต่ละ Worksheet ต้องมี

**Worksheet "Trend"** — grain รายเดือน, กรองเหลือ Vendor เดียว (ไม่ต้องมี Article Id)

| Field ใน Tableau | ใช้ทำอะไร |
|---|---|
| `Month, Year of Time Date` | แกนเวลาของกราฟ Trend |
| `Net Inc Tax` | ยอดขาย (Net Sales) |
| `Sale Qty` | จำนวนขาย |
| `Sls Ofc Desc` | Sales Office |

**Worksheet "Detail"** — grain รายปี, กรองเหลือ Vendor เดียว, ต้องมี Article Id

| Field ใน Tableau | ใช้ทำอะไร |
|---|---|
| `Month, Year of Time Date` | ใช้แยกปีปัจจุบัน/ปีก่อน |
| `Article Id` | นับจำนวน SKU |
| `Article Name Th` | ชื่อสินค้าในตาราง Top 10 |
| `Mch1 Desc` | ใช้ regroup เป็น Product Group (Faucet/Shower/Accessories/Spare Parts) |
| `MCH2_Desc` | Sales by MCH2 (แสดงในการ์ด Vendor) |
| `Mc Desc` | คอลัมน์ "MC Desc" ในตาราง Top 10 |
| `Brand` | Sales by Brand |
| `Vendor Name` | ใช้แสดงชื่อ vendor ที่กำลังดู |
| `Universe` | Sales by Price Segment |
| `Sls Grp Desc` | Sales by Channel |
| `Net Inc Tax`, `Sale Qty` | ยอดขาย/จำนวนขาย |

**Worksheet "AllVendors"** — grain รายปี, **ไม่กรอง** Vendor (ต้องเห็นทุก vendor)

| Field ใน Tableau | ใช้ทำอะไร |
|---|---|
| `Month, Year of Time Date` | ใช้แยกปีปัจจุบัน/ปีก่อน |
| `Vendor Name` | คำนวณ Rank/Share ของ vendor ที่เลือก เทียบทุก vendor |
| `Article Id` | นับ SKU ของแต่ละ vendor เพื่อจัด Rank (SKU) |
| `Net Inc Tax`, `Sale Qty` | ยอดขาย/จำนวนขาย ของทุก vendor |

> ชื่อ field ต้องตรงกับในตาราง **เป๊ะๆ** (ตรงตามที่ประกาศไว้ในตัวแปร `FIELD` ท้ายไฟล์ `index.html`) ถ้าใน data source ใช้ชื่อคอลัมน์ต่างจากนี้ ให้แก้ค่าในตัวแปร `FIELD` ให้ตรงกับ data source จริง

---

## ข้อจำกัดที่ควรรู้

- ช่วงเดือนที่แสดง (เช่น "Jan–Mar") คำนวณจากข้อมูลจริงที่มีใน worksheet "Trend" ของ Tableau ตอนนั้นโดยอัตโนมัติ ไม่ได้ fix ไว้ตายตัว
- ข้อมูลต้นทาง (`data form ds new.xlsx`) ไม่มีฟิลด์ "สี" (Col Name) จึงไม่มีการ์ด "Sales by Color Group" ใน dashboard นี้
- ทุกครั้งที่แก้ `vendor-overview/index.html` แล้ว push ขึ้น GitHub ต้องรอ GitHub Pages build ใหม่ (ปกติ 1–2 นาที) ก่อนที่ Tableau จะเห็นเวอร์ชันล่าสุด — ถ้าไม่เห็นการเปลี่ยนแปลง ให้ลอง hard refresh หรือปิด-เปิด dashboard ใหม่
