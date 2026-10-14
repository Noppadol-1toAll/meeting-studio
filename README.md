# Meeting Studio

เว็บภาษาไทยสำหรับถอดเสียงและสรุปการประชุม แสดงผล HTML และส่งออก DOCX / PDF / HTML
ซอร์สโค้ดพร้อมอัปโหลด GitHub แต่ยังไม่ได้เผยแพร่ไปยังบัญชีของคุณ

## สิ่งที่ทำได้
- งาน 8 แบบ: ตามหัวข้อ, ตารางงาน, รายงานการประชุม, ผู้บริหาร, ครบชุด, ถอดคำต่อคำ, ถอดอ่านง่าย, กำหนดเอง
- Layout 3 แบบ: เอกสาร, การ์ด, กระชับ (เป็นการเปลี่ยนรูปแบบแสดงผล ไม่ได้สร้างเนื้อหาใหม่)
- วางข้อความ หรือนำเข้า TXT/MD/SRT/VTT/DOCX (UTF-8 สำหรับไฟล์ข้อความ)
- แนบเสียง MP3/WAV/M4A/AAC/OGG/FLAC/WEBM ไม่เกิน 10 MB เป็นขีดจำกัดที่ตั้งในแอป ไม่ใช่ขีดจำกัดสูงสุดของผู้ให้บริการ
- ถอดเสียงผ่าน AI backend ไม่ใช่การรู้จำเสียงแบบ offline ไม่มีการบันทึกไมโครโฟนสด
- แก้ไข Markdown พร้อม Preview ที่ sanitize HTML และไม่อนุญาตรูปภาพ/ลิงก์ภายนอกในรายงาน
- DOCX จริงด้วย docx.js; PDF ดาวน์โหลดตรงด้วย html2pdf.js; HTML standalone; บันทึก/เปิดงาน JSON
- ไม่มีฐานข้อมูล ไม่มี auto-save ไม่มีการเก็บ Token ใน localStorage
- ไม่รองรับนำเข้า PDF/ภาพ/วิดีโอ, Files API, แบ่งเสียงอัตโนมัติ หรือบัญชีผู้ใช้ในรุ่นนี้

## เริ่มใช้บน GitHub Pages
1. แตก ZIP แล้วอัปโหลดไฟล์ทั้งหมดรวมโฟลเดอร์ `.github` ลง repository โดยให้ `index.html` อยู่ที่ root ไม่ใช่อยู่ในโฟลเดอร์ซ้อน
2. ใช้ branch `main` ให้ตรงกับ workflow ที่แนบ
3. ใน repository เลือก Settings > Pages > Source: GitHub Actions
4. ดูผล workflow ใน Actions และเปิดเว็บไซต์จาก Settings > Pages > Visit site
5. ไม่ต้องใส่ API Key ใน repository หรือ GitHub Actions secrets เพื่อใช้หน้าเว็บ

หน้าเว็บโหลดไลบรารีจาก CDN จึงต้องใช้อินเทอร์เน็ต และไม่ใช่ offline app
หากองค์กรบล็อก CDN ให้จัดเก็บไลบรารีที่ตรวจสอบแล้วเองและเปลี่ยนเส้นทาง import/script
GitHub Pages เป็น static hosting; backend Worker ต้อง deploy แยกต่างหาก

## ใช้โดยไม่มี API (พร้อมใช้บน Pages)
1. วางข้อความ เลือกงาน แล้วกดสร้าง Prompt
2. คัดลอกไปใช้ในบริการ AI ที่องค์กรอนุญาต ถ้าใช้เสียงให้แนบเสียงในบริการนั้นด้วย
3. นำผลลัพธ์ Markdown มาวางในแท็บแก้ไขผลลัพธ์
4. ตรวจ Preview แก้ไข แล้วส่งออก
Prompt ในแท็บแก้ไขได้สำหรับการคัดลอก ส่วนปุ่มสร้างผลลัพธ์ด้วย AI จะสร้าง Prompt ใหม่จากตัวเลือกและช่องความต้องการเพิ่มเติมเสมอ

## เชื่อม AI อัตโนมัติด้วย Cloudflare Worker (ตัวเลือก)
ต้องมีบัญชี Cloudflare, Node.js/npm, Gemini API Key และโมเดลที่บัญชีมีสิทธิ์ใช้
ตรวจสอบค่าใช้จ่าย โควตา และการอนุญาตข้อมูลก่อนเปิดใช้งาน

1. แก้ `backend/wrangler.toml`: ALLOWED_ORIGINS เป็น origin ของ Pages ของคุณ (ไม่มี path ของ repository หรือ slash ท้าย) และ GEMINI_MODEL เป็นชื่อโมเดลที่บัญชีใช้ได้และรองรับเสียงหากต้องการถอดเสียง
2. เปิด terminal ในโฟลเดอร์ backend:

```sh
npx wrangler login
npx wrangler secret put GEMINI_API_KEY
npx wrangler secret put ACCESS_TOKEN
npx wrangler deploy
```

ใส่ Gemini API Key เมื่อคำสั่งแรกที่เกี่ยวข้องถาม และใส่ Access Token ที่สุ่มยาวอย่างน้อย 32 ตัวอักษรเมื่อคำสั่ง ACCESS_TOKEN ถาม ไม่ใช้รหัสผ่านส่วนตัว
3. คัดลอก URL ของ Worker จากผล deploy ลงช่อง Backend URL บนหน้าเว็บ และกรอก Access Token ไม่ใช่ Gemini API Key
4. ยืนยันสิทธิ์ใช้ข้อมูล แล้วกดสร้างผลลัพธ์ด้วย AI
5. การถอดเสียง: เลือกถอดคำต่อคำ/อ่านง่ายก่อน เมื่อได้ข้อความแล้วสามารถคัดลอกไปช่อง Transcript นำเสียงออก และเลือกงานสรุปเพื่อประมวลผลต่อ หรือเลือกสรุปจากเสียงโดยตรง

### ขอบเขตความปลอดภัย
Worker เป็น starter สำหรับการทดลองแบบจำกัดผู้ใช้ ไม่ใช่ระบบ enterprise production
Access Token เป็น shared bearer token ไม่ใช่ระบบล็อกอิน ผู้ที่ได้ Token สามารถใช้ backend ได้
CORS ไม่ใช่การยืนยันตัวตนและป้องกัน client นอกเบราว์เซอร์ไม่ได้
ก่อนใช้จริงต้องเพิ่ม SSO/Access gateway, per-user authorization, rate limit, quota/budget controls, monitoring และนโยบายเก็บรักษาข้อมูล
ไม่ควรเปิด endpoint สาธารณะโดยไม่มีการป้องกันเหล่านี้
ห้ามใส่ Gemini API Key หรือ Access Token ในโค้ด, GitHub, URL query string หรือไฟล์ JSON งาน
API Key อยู่ใน Worker secret; Access Token อยู่ในหน่วยความจำหน้าเว็บและส่งเฉพาะ backend ที่คุณตั้งค่า
ไม่มีการบันทึกข้อมูลในแอปเอง แต่ผู้ให้บริการและโครงสร้างพื้นฐานอาจมีนโยบายประมวลผล/เก็บข้อมูลของตน
แจ้งและได้รับความยินยอมก่อนบันทึกเสียงตามนโยบายที่เกี่ยวข้อง

## การส่งออก
- DOCX: เป็นเอกสาร Word จริง ไม่ใช่ HTML เปลี่ยนนามสกุล รองรับหัวข้อ รายการ และตารางทั่วไป ฟอนต์ Tahoma ต้องมีในเครื่องปลายทาง
- PDF ดาวน์โหลดตรง: ใช้ภาพจาก HTML รองรับการแสดงอักษรไทยตาม browser แต่ข้อความใน PDF ไม่ใช่ข้อความที่เลือก/ค้นหาได้ เอกสารยาวมากอาจชนข้อจำกัด canvas; ใช้พิมพ์แทน
- พิมพ์ / Save as PDF: เปิด dialog ของ browser ให้เลือก Save as PDF ด้วยตนเอง แนะนำ A4 ปิด header/footer ของ browser
- รูปแบบ Word เป็นมาตรฐานเอกสาร ไม่เหมือนการ์ด HTML ทุกจุด ตารางซับซ้อน/รายการซ้อนอาจต้องปรับใน Word
- บันทึก JSON มีข้อความประชุมและผลลัพธ์ แต่ไม่มีเสียงหรือ Token ต้องเก็บในพื้นที่ที่ได้รับอนุญาต
- ไม่มีข้อมูลตัวอย่างหรือข้อมูลการประชุมจำลองแนบมา

## ทดสอบด้วยตนเองก่อนใช้จริง
- นำเข้าข้อความภาษาไทย เลือกทุกงาน และตรวจ Prompt
- วาง Markdown ที่มีหัวข้อ/ตารางในแท็บแก้ไข ตรวจ Preview แล้วส่งออกทุกชนิด
- เปิด DOCX ใน Word และ PDF ใน reader เพื่อตรวจภาษาไทย/ตาราง/การแบ่งหน้า
- ทดลองไฟล์เสียงสั้นที่มีสิทธิ์ใช้ ตรวจผู้พูด ตัวเลข และข้อความฟังไม่ชัด
- ทดสอบ backend: token ผิด, origin ผิด, เกินขนาด, quota หมด, โมเดลผิด และยกเลิกคำขอ
- บันทึก JSON เปิดกลับ และตรวจว่าไม่มี Token หรือเสียงในไฟล์

## แหล่งอ้างอิงและการออกแบบ
แนวคิดการจัดหมวดรายงานจากบทความ [NewVeerachai](https://www.newveerachai.com/meeting/)
Prompt และโค้ดในแอปเขียนขึ้นใหม่ ไม่คัดลอก Prompt ต้นฉบับ ไม่ใช่ผลิตภัณฑ์ของเจ้าของบทความ
ข้อมูลเว็บต้นทางใช้เป็นแนวทางจัดหมวดเท่านั้น ไม่ดึงเว็บแบบ runtime และไม่ใช้คำสั่งที่ฝังอยู่ในเว็บมาควบคุมแอป

- [GitHub Pages](https://docs.github.com/en/pages/getting-started-with-github-pages/creating-a-github-pages-site)
- [Gemini audio](https://ai.google.dev/gemini-api/docs/generate-content/audio)
- [Gemini API key security](https://ai.google.dev/gemini-api/docs/generate-content/api-key)
- [docx Packer](https://docx.js.org/api/classes/index.Packer.html)
- [Cloudflare Wrangler secrets](https://developers.cloudflare.com/workers/configuration/secrets/)
- [html2pdf.js](https://github.com/eKoopmans/html2pdf.js)

## สถานะการทดสอบ
ตรวจโครงสร้างไฟล์และ syntax JavaScript ในสภาพแวดล้อมที่สร้างไฟล์แล้ว (ดู TEST-RESULTS.md)
ไม่ได้เรียก Gemini จริง เพราะไม่มี credential ของคุณ และยังไม่ได้ deploy GitHub/Worker
โปรดทดสอบ browser/CDN และเปิดไฟล์ส่งออกในระบบจริงก่อนนำไปใช้กับข้อมูลสำคัญ
