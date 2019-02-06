﻿## Stateful Sateless

        Stateful หมายถึง การจดจำสถานะ (state)
        Stateless  หมายถึง การไม่จดจำสถานะ (state)

#ปัญหา

        HTTP (HyperText Transfer Protocol) เป็น Protocol ที่ใช้ในการสื่อสาร / รับส่งข้อมูลทางด้าน Web Application การทำงานของ HTTP จะเป็นการสร้างแล้วส่ง Request ไปยัง Web Server เพื่อร้องขอ Resources 
เมื่อ Web Server ได้รับ Request  ก็จะทำการตรวจสอบสิทธิ์ว่า Request นั้นมีสิทธิ์เข้าถึง Resource ดังกล่าวหรือไม่ หากมีสิทธิ์ Web Server ก็จะจัดสรร Resource ให้ตามที่ Request นั้นร้องขอมา 

        HTTP เป็น Protocol ที่ไม่มีการจดจำสถานะการทำงาน  ทุกครั้งที่ Request ไป จะเป็นการสร้าง Request ขึ้นมาใหม่  โดยไม่มีการจัดเก็บ / จดจำข้อมูลใดๆ ไว้ที่ Web Server  ทุก Request ที่ส่งไป จะถูก Web Server ตรวจสอบสิทธิ์ใหม่ทุกครั้ง  เราเรียกการทำงานของ HTTP ว่าเป็นการทำงานแบบ Stateless   

        จากปัญหาดังกล่าว  เขาจึงได้คิดค้นวิธีการที่ทำให้ Web Server สามารถจดจำสถานะการทำงาน  หรือสถานะของ Client ได้โดยไม่จำเป็นต้องตรวจสอบสิทธิ์ใหม่ทุกครั้ง  วิธีการที่นำมาใช้เรียกว่า Session 

        Session เป็นวิธีการที่ Web Server ใช้สำหรับจดจำสถานะการทำงาน / ระบุตัวตนของ Client
เมื่อ Client ทำการ Log in เข้าสู่ระบบ Web Server จะสร้างรหัสชุดนึงขึ้นมา เรียกว่า Session Id  แล้วทำการส่ง Session Id กลับไปพร้อมกับ Request ของ Client 

        Client จะเก็บ Session Id ไว้ในรูปแบบที่เรียกว่า Cookies  เมื่อมีการส่ง HTTP Request ไปยัง Web Server อีกครั้ง  Client จะต้องส่ง Session Id กลับไปให้ Web Server ด้วย  เพื่อบอกกับ Web Server ว่า ตนเคย Log in เข้ามาในระบบแล้ว  (ด้วย Session Id ดังกล่าว) จะได้ไม่ต้องตรวจสอบสิทธิ์ใหม่อีกครั้ง  เราเรียกการทำงานแบบนี้ว่า Stateful

        ปัจจุบัน  มี  Application ที่ทำงานทั้งที่เป็นแบบ Stateful และ Stateless
Stateful คือ Application ที่ใช้ Session
Stateless คือ Application ที่ไม่ใช้ Session  แต่จะใช้อย่างอื่นแทน  ที่เรียกว่า Token



Token

        เป็นรหัสชุดนึงที่เอามาแทน Session Id เอาไว้ระบุตัวตนของ Client  ว่า Client นั้นเป็นใคร
ไม่มีรูปแบบตายตัว  แล้วแต่กรรมวิธีที่นำมาใช้  มีทั้งที่เป็น Standard และบางคนก็คิดวิธีการสร้าง Token ขึ้นมาใช้งานเอง  แต่สำหรับบทความนี้  เราใช้ Standard Token ที่มีชื่อว่า JWT (Json Web Token) คือเอา Json data ของผู้ใช้มาสร้างเป็นรหัส (Token)



ข้อดีข้อเสีย ของ Stateful และ Stateless

Stateful
    
ข้อดี 
ง่าย  สะดวก เพราะสามารถจดจำสถานะของ Client ได้  โดยที่เราไม่ต้องตรวจสอบสิทธิ์ใหม่ทุกครั้ง
เร็ว  เพราะไม่ต้องตรวจสอบสิทธิ์  และยังสามารถเก็บข้อมูลอื่นๆ ของ Client ไว้บน Memory ได้  โดยที่เราไม่ต้อง query ข้อมูลจาก Database ใหม่ทุกครั้ง
ข้อเสีย
ไม่เหมาะกับระบบที่ต้องการขยายขนาด (scale) ในอนาคต  เพราะ Session มีไว้ใช้เฉพาะภายใน Cluster นั้นๆ เท่านั้น ไม่สามารถเอาไปใช้กับระบบอื่นได้   และ Session Id ก็มีขนาดความยาวจำกัด นั่นหมายความว่า รองรับ Client ได้จำนวนจำกัด
สิ้นเปลือง Memory ถ้าโปรแกรมเมอร์เขียนโปรแกรม ไม่ดี  อาจใช้ Session นั้นในการเก็บข้อมูล
ไม่เหมาะกับการใช้งานร่วมกับ Native Mobile  เพราะ Native Mobile ไม่ใช่ browser จึงไม่ค่อยเหมาะกับการทำงานร่วมกับ Cookies
การเขียนโปรแกรมมีความซ้ำซ้อน  รวม Service ไม่ได้ ในกรณีที่โปรเจ็คเรามีทั้ง Web App และ Mobile App เพราะเราไม่สามารถแยก Front end (html, css, js) ที่เป็นส่วนแสดงผล ออกจาก Back end (java, php, node.js, asp) ที่เป็น Business Logic ได้  
เรื่อง Session Expire หรือ Session หมดอายุ ซึ่งเราจะต้องทำการ Log in ใหม่ทุกครั้ง 
ฯลฯ

Stateless

ข้อดี
ในเรื่องของการขยายระบบ  เพราะมันไม่ต้องใช้ Session  ทำให้เราไม่ต้องทำ Cluster  ก็สามารถทำระบบ HA (High Ability System) ได้  ถึงแม้ว่า Server เครื่องใดเครื่องหนึ่งจะ down ไปและไม่ได้อยู่ใน Cluster เดียวกัน  เราก็สามารถใช้งาน Application นั้นได้ตามปกติ  โดยไม่ต้อง Log in ใหม่
สามารถเขียน Front end และ Back end แยกออกจากกันได้
ใช้ได้กับ Application ทุกรูปแบบไม่ว่าจะเป็น Web Desktop Mobile เพราะเราไม่ใช้ Session
ฯลฯ
ข้อเสีย
ต้องตรวจสอบสิทธิ์ทุกครั้ง  ทำให้ทำงานช้ากว่าแบบ Stateful
เขียนโปรแกรม + วางระบบยุ่งยากกว่า