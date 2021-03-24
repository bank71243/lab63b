# การทดลองที่ 1 การเขียนโปรแกรมเพื่อรันบนไมโครคอนโทรเลอร์
### วัตถุประสงค์
1. เพื่อเข้าใจคำสั่งต่างๆของโค้ดโปรแกรมที่ใช้รัน
2. เพื่อศึกษาองค์ประกอบ ข้อมูลพื้นฐานของ microcontroller
3. เพื่อการเขียนและรันโปรแกรมจาก microcontroller

### อุปกรณ์ที่ใช้
1. microcontroller ESP-01 ที่ประกอบไปด้วย CPU เสาอากาศสำหรับ WIFI
2. อุปกรณ์เชื่อมต่อ USB เข้าไปยัง serial

### แหล่งข้อมูลที่ศึกษา
https://www.youtube.com/watch?v=NLIUsWLEpmg

### วิธีการทำการทดลอง
  1. เขียนโปรแกรมบน microcontroller โดยทำการเสียบ microcontroller เข้าทาง serial port ของ USB
  
  ![1](https://user-images.githubusercontent.com/80879395/112295388-70dee080-8cc6-11eb-9900-b4feaf9b75dc.jpg) (microcontroller ESP-01)
  
  ![2](https://user-images.githubusercontent.com/80879395/112296456-856fa880-8cc7-11eb-9de8-14188c0ad68a.jpg) (อุปกรณ์เชื่อมต่อ USB และ การเสียบเข้าทาง serial port)
  
  2. เปิดไปที่โปรเเกรมตัวอย่าง pattani โดยเมื่อเปิดจะมีตัวอย่างทั้งหมด 9 ตัวอย่าง
      * ไปที่ตัวอย่างที่1
        * โดย พิมพ์ cd 01_Serial Monitor > vi src/main.cpp
  3. เเสดงผลโปรเเกรม 
      * โดยจะมีการเเสดงผล set up 1 ครั้ง โดยการ set up serial port ที่ความเร็ว 115200
      * ใน loop แสดงให้เห็นถึงการวนไปเรื่อยๆ
        * cnt++ หมายถึง การนับเพิ่มเรื่อยๆ
        * Serial.printf("PATTANI :%d\n",cnt) แทนคำสั่ง การแสดงผลตัวแปล count
        * delay(1000) หมายถึง ช่วงเวลา 1000 ms หรือ 1 วินาที
  ```javascript
#include <Arduino.h>

int cnt = 0;

void setup()
{
	Serial.begin(115200);
}

void loop()
{
	cnt++;
	Serial.printf("PATTANI :%d\n",cnt);
	delay(1000);
}
```
  4. เข้าไปที่ configuration file ใน program
      * พิมพ์ vi platformio.ini เพื่อแสดงข้อมูล
      * platform แสดงถึง เทคโนโลยีของบริษัทผู้ผลิต
      * board แสดงถึง ชื่อบอร์ด
      * framwork แสดงถึง วิธีการเขียนโปรแกรม
      * upload_port แสดงถึง portที่ใช้ติดต่อ
  ```javascript
; IOT for KIDS
;
; By Dr.Choompol Boonmee
; 

[env:exercise01]
platform = espressif8266
board = esp01_1m
framework = arduino
board_build.flash_mode = dout

upload_port = /dev/cu.usbserial-1420
;upload_port = COM3

monitor_port = /dev/cu.usbserial-1420
;monitor_port = COM3
monitor_speed = 115200
```

   5. อัพโหลดโปรแกรม 01_Serial Monitor เข้าไปยัง microcontroller โดยใช้คำสั่ง upload
        * พิมพ์ pio run -t upload
        * ในขณะที่ program กำลังรันข้อมูล เพื่อให้ microcontroller รับโปรแกรมใหม่
           * กดปุ่มสีดำ เพื่อทำให้เกิดการ load
           * กดปุ่มสีแดง เพื่อให้เกิดการ reset
           * ![3 1](https://user-images.githubusercontent.com/80879395/112302245-6ffd7d00-8ccd-11eb-80a0-14b1018c79a1.jpg) ![3 2](https://user-images.githubusercontent.com/80879395/112302319-87d50100-8ccd-11eb-8a4d-af120bd66de4.jpg)

        * อัพโหลดเข้า microcontroller เสร็จสิ้น
           ![4](https://user-images.githubusercontent.com/80879395/112302718-f7e38700-8ccd-11eb-9718-53f58a1cc659.jpg) 
        * สังเกตผลลัพธ์ที่แสดงผลผ่านคอมพิวเตอร์
            * พิมพ์ pio device monitor
            * ![5](https://user-images.githubusercontent.com/80879395/112302961-4b55d500-8cce-11eb-8c85-ac93a6f76bc1.jpg) 
        * กดปุ่มสีแดง เพื่อทำการ reset โปรแกรม โดยโปรแกรมจะหยุดทำงานและเริ่มนับ 1 ใหม่ 
            * ![6](https://user-images.githubusercontent.com/80879395/112303022-645e8600-8cce-11eb-87ff-0d811380b532.jpg)

### การบันทึกผลการทดลอง
คำสั่ง | ผลลัพธ์ที่แสดง
------------ | -------------
src/main.cpp | ผลลัพธ์ของโปรแกรมส่วน set up & loop
platformio.ini | ข้อมูลใน configuration file
pio run -t upload | รันข้อมูลในตัวอย่าง
pio device monitor | PATTANIที่เพิ่มขึ้นใน 1 วินาที
การกดปุ่มสีดำ | โปรแกรมถูกโหลด
การกดปุ่มสีแดง | โปรแกรมถูกรีเซ็ต


### อภิปรายผลการทดลอง
* platformio นั้น สามารถใช้เขียนโปรแกรมจาก microcontroller หลายชนิดที่มีบริษัทต่างกันได้ โดย คำสั่ง platformio.ini เป็นเหมือนตัวแสดงผลว่าการเขียนโปรแกรมครั้งนี้เราจะเขียนให้กับ microcontroller ตัวไหน
* pio run -t upload นั้นใช้ในการอัพโหลดข้อมูลไปยัง microcontroller โดยสามารถกดปุ่มซึ่งอยู่ภายนอก microcontroller เพื่อทำการโหลดและรีเซ็ตการรันโปรแกรมได้


### คำถามหลังการทดลอง
หากต้องการให้โปรแกรมเริ่มนับใหม่อีกครั้ง ปุ่มใดบน microcontroller ที่สามารถใช้ได้
* ตอบ ปุ่มสีเเดง เพื่อทำการ reset โปรเเกรม
