# ANSWER — นายธนวรรธน์ บุญรอด 671540005024-8

- Platform: Vercel
- Production URL: https://ksu-coffee-671540005024-8.vercel.app

## คำตอบ

1. เลือก Vercel ซึ่งรันโค้ดแบบ Serverless (ไม่มี server ที่เปิดค้างไว้ตลอด แต่รันเป็น function เมื่อมี request เข้ามา) บน production ไม่ได้ใช้ `server.js` เพราะ Vercel เสิร์ฟไฟล์ใน `public/` เป็น static ตาม `vercel.json` (`outputDirectory: public`) และเรียก `api/info.js` เป็น function ให้เองที่ `/api/info` ส่วน `server.js` มีไว้รันในเครื่อง (หรือแพลตฟอร์มแบบ Container เช่น Render) เท่านั้น
2. ตั้ง `STUDENT_ID` ที่หน้า Environment Variables ของโปรเจกต์บน Vercel (ตอน import project หรือที่ Settings → Environment Variables) ห้าม commit `.env` เพราะอาจมีข้อมูลลับ และเมื่อขึ้น GitHub สาธารณะแล้วจะถูกคนอื่นเห็นและลบออกจากประวัติ git ยาก (repo มี `.gitignore` กันไว้ให้) ถ้า `/api/info` ขึ้น NOT SET อาจเกิดจากยังไม่ได้ตั้งตัวแปร, พิมพ์ชื่อผิด (ต้องเป็น `STUDENT_ID` เป๊ะ), ตั้งหลัง deploy แล้วยังไม่ได้ deploy ใหม่ หรือตั้งไว้แค่ใน `.env` ในเครื่องซึ่งไม่ขึ้น production
3. ครั้งแรก 0.680 วินาที / ครั้งที่สอง 0.694 วินาที — แทบไม่ต่างกัน เพราะตอนวัดเพิ่ง deploy เสร็จและ function ยังอุ่น (warm) อยู่จากการเรียกก่อนหน้า จึงไม่เจอ cold start เวลาส่วนใหญ่เป็นเวลาเครือข่าย (DNS, TLS, ระยะทางไป server) ถ้าปล่อย function ว่างนานแล้วค่อยเรียก ครั้งแรกอาจช้ากว่าเพราะ Serverless ต้องสร้าง instance ใหม่ (cold start) ส่วนครั้งถัดไปจะเร็วเพราะใช้ instance เดิมซ้ำ
4. เมื่อ `git push` ไป GitHub แล้ว Vercel (ที่ผูกกับ repo ไว้) ได้รับแจ้งว่ามี commit ใหม่ที่ branch main จึงดึงโค้ดล่าสุดมา build และ deploy เวอร์ชันใหม่โดยอัตโนมัติ เมื่อเสร็จแล้วสลับ production ให้ชี้ไปเวอร์ชันนั้น หน้าเว็บจึงเปลี่ยนเป็น Menu v2 ค่า commit ใน `/api/info` มาจาก env ของแพลตฟอร์ม (`VERCEL_GIT_COMMIT_SHA`) และตรงกับ `git log -1 --format=%H` คือ `fef7f1a41bb2e0f72057f681bd0b3906f9f3bb4a` (ตรวจแล้ว)
5. ตรง ๆ ไม่ได้ เพราะ Vercel ไม่รัน Docker Compose และไม่รัน container แบบหลาย service ได้เอง (รันได้แค่ static + serverless function) ระบบข้อ 1 เป็น 2 service คือเมนู (build เอง) กับโปรโมชั่น (image จาก Docker Hub) ต้องแก้เป็นคนละโปรเจกต์ หรือใช้แพลตฟอร์มที่รับ container เช่น Render ที่ deploy ทีละ service แล้วเชื่อมกันด้วย URL สาธารณะ
