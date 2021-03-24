![3](https://user-images.githubusercontent.com/80879395/112315126-63345580-8cdc-11eb-83df-5a639e95cf30.jpg)
# การทดลองที่ 3 การเขียนโปรแกรมเอ้าพุทสัญญาณดิจิทัล

### วัตถุประสงค์
1. เพื่อศึกษาหน้าที่ของ Relay
2. เพื่อศึกษาและทำความเข้าใจ output ที่แสดงผลออกมา
3. เพื่อให้สามารถเขียนโปรแกรมและรัน microcontroller ซึ่งต่อกับ adapter

### อุปกรณ์ที่ใช้
1. Relay
2. adapter
3. หลอด LED เปล่งแสง
4. microcontroller ESP-01
5. อุปกรณ์เชื่อมต่อ USB เข้าไปยัง serial

### แหล่งข้อมูลที่ศึกษา
- https://www.youtube.com/watch?v=CCnN1WJsXQY
- https://www.youtube.com/watch?v=6JnhaUILGuw


### วิธีการทำการทดลอง
1. นำ adapter(ต่อกับLED) ต่อเข้ากับ USB 
2. ทำการเสียบ microcontroller เข้าทาง serial port ของ USB 
3. ดูที่ตัวอย่างโปรแกรม ที่โฟลเดอร์ pattani  
* พิมพ์ cd pattani เพื่อไปยังโฟลเดอร์
* แสดงโฟลเดอร์ ซึ่งมีโปรแกรมตัวอย่าง 9 โปรแกรม
  * ไปที่ตัวอย่างที่ 3
    * พิมพ์ cd 03_Output-Port
![1](https://user-images.githubusercontent.com/80879395/112313327-4f87ef80-8cda-11eb-8498-e22779eefe92.jpg)

4. ดู source code program 
* พิมพ์ vi src/main.cpp
* โปรแกรม นี้ set up serial port ที่ port 0 (output)
* ใน loop แสดงให้เห็นถึงการวนไปเรื่อยๆ
  * cnt++ หมายถึง การนับเพิ่มเรื่อยๆ 
    * หาก cnt%2 แปลว่า ตัวเลขเป็น odd ให้ดำเนินการ on
    * หากไม่ใช่ แปลว่า ตัวเลขเป็นเลข even ให้ดำเนินการ off
  * delay(500) หมายถึง ช่วงเวลา 500 ms
```javascript
#include <Arduino.h>
#include <ESP8266WiFi.h>

int cnt = 0;

void setup()
{
	Serial.begin(115200);
	pinMode(0, OUTPUT);
	Serial.println("\n\n\n");
}

void loop()
{
	cnt++;
	if(cnt % 2) {
		Serial.println("========== ON ===========");
		digitalWrite(0, HIGH);
	} else {
		Serial.println("========== OFF ===========");
		digitalWrite(0, LOW);
	}
	delay(500);
}
```
5. อัพโหลดโปรแกรม 03_Output-Port เข้าไปยัง microcontroller โดยใช้คำสั่ง upload
   * พิมพ์ pio run -t upload
   * ในขณะที่ program กำลังรันข้อมูล เพื่อให้ microcontroller รับโปรแกรมใหม่เข้าไป
     * กดปุ่มสีแดง เพื่อให้เกิดการ reset

![2](https://user-images.githubusercontent.com/80879395/112314092-329fec00-8cdb-11eb-8655-8925675a2ea2.jpg)

   * สังเกตผลลัพธ์ที่แสดงผลผ่านคอมพิวเตอร์
     * พิมพ์ pio device monitor
6. นำ microcontroller ต่อเข้ากับ Relay

![3](https://user-images.githubusercontent.com/80879395/112315150-6a5b6380-8cdc-11eb-88e5-54a47bcc855f.jpg)


7. นำแหล่งจ่ายไฟมาต่อเข้ากับตัว Relay เพื่อจ่ายไฟให้ Relay ทำงานได้
   * ผลลัพธ์ออกมาเป็น on off สลับกันไปเรื่อยๆ ดังภาพ
     * ไฟสีเขียวติดเเละดับ สลับกัไป
     * ไฟสีน้ำเงิน ไม่ติดเลย

### การบันทึกผลการทดลอง
count | ลักษณะสีของหลอดไฟ | ความสว่าง
------------ | ------------- | -------------
เลขคี่ | ไฟสีเขียว | ติด
เลขคู่ | ไฟสีเขียว | ดับ

### อภิปรายผลการทดลอง
* pio run -t upload นั้นใช้ในการอัพโหลดข้อมูลจาก 03_Output-Port ไปยัง microcontroller โดยคำสั่ง pio device monitor ที่ใช้ดูผลลัพธ์ของโปรแกรมที่ถูกอัพโหลดเข้าไป โดย output ที่ได้ออกมาจะสังเกตว่า ขึ้น on-off สลับกันไปในทุกๆ 500 ms 
* ในขณะเดียวกันหลอด LED เปล่งแสง ที่ port 0 ซึ่ง ติดและดับ ตามคำสั่ง on-off
* เมื่อนำ microcontroller ต่อเข้ากับ Relay จะสังเกตได้ว่า Relay มีลักษณะการเปิด-ปิดสวิตซ์สอดคล้องกับ code ของโปรแกรมที่เราใส่

### คำถามหลังการทดลอง
หากเปลี่ยน delay เป็น 1000 ms สำหรับโจทย์ข้อนี้ หมายถึงอะไร
* ตอบ output จะแสดง on-off สลับกันไปในทุกๆ 1000 ms
