# การทดลองที่ 6 การเขียนโปรแกรมสร้างไวไฟแอคเซสพอยต์ (Wifi AP)

### วัตถุประสงค์
1. เพื่อเข้าใจการเขียนโปรแกรมที่จะสร้าง Wifi Access Point ขึ้นมาเอง 
2. เพื่อสามารถนำโปรแกรมไปใช้ในการเชื่อม wifi ของตัวเองกับอุปกรณ์อื่น

### อุปกรณ์ที่ใช้
1. CPU
2. microcontroller ESP-01
3. อุปกรณ์เชื่อมต่อ USB เข้าไปยัง serial

### แหล่งข้อมูลที่ศึกษา
https://www.youtube.com/watch?v=T26DVHePlTs

### วิธีการทำการทดลอง
1. ทำการเสียบ microcontroller เข้าทาง serial port ของ USB 
2. ดูตัวอย่างโปรแกรม ที่โฟลเดอร์ pattani
* พิมพ์ cd pattani เพื่อไปยังโฟลเดอร์
  * ไปที่ตัวอย่างที่ 6
    * พิมพ์ cd 06_Wifi-AP-Web-Server
3. ดู source code program 
* พิมพ์ vi src/main.cpp  
```javascript
#include <ESP8266WiFi.h>
//#include <WiFiClient.h>
#include <ESP8266WebServer.h>

const char* ssid = "MY-ESP8266";
const char* password = "choompol";

IPAddress local_ip(192, 168, 1, 1);
IPAddress gateway(192, 168, 1, 1);
IPAddress subnet(255, 255, 255, 0);

ESP8266WebServer server(80);

int cnt = 0;

void setup(void){
	Serial.begin(115200);

	WiFi.softAP(ssid, password);
	WiFi.softAPConfig(local_ip, gateway, subnet);
	delay(100);

	server.onNotFound([]() {
		server.send(404, "text/plain", "Path Not Found");
	});

	server.on("/", []() {
		cnt++;
		String msg = "Hello cnt: ";
		msg += cnt;
		server.send(200, "text/plain", msg);
	});

	server.begin();
	Serial.println("HTTP server started");
}

void loop(void){
  server.handleClient();
}
```
จาก code ดังกล่าว เเสดงผลได้ว่า
* กำหนด ชื่อ และ ตั้งpassword (line 4, 5)
* กำหนด IPAdress, gateway, subnet (line 7, 8, 9)
* เตรียม web server (line 26-30)
* รันคำสั่ง softAP แล้วกำหนด ssiad กับ password (line 18)

![1](https://user-images.githubusercontent.com/80879395/112323325-43a12b00-8ce4-11eb-852f-11fffbdda294.jpg)

4. อัปโหลดโปรแกรม 06_Wifi-AP-Web-Server เข้าไปยัง microcontroller โดยใช้คำสั่ง upload
  * พิมพ์ pio run -t upload
  * ในขณะที่ program กำลังรันข้อมูล เพื่อให้ microcontroller รับโปรแกรมใหม่เข้าไป
    * กดปุ่มสีแดง เพื่อให้เกิดการ reset
5. ตรวจสอบว่า wifi ที่สร้างขึ้นนั้นแสดงผลหรือยัง 
    * พิมพ์ pio device monitor

![2](https://user-images.githubusercontent.com/80879395/112326044-c32ff980-8ce6-11eb-96ca-cc95cc848bb7.jpg)

6. ใช้โทรศัพท์โทรศัพท์มือถือค้นหา wifi

### การบันทึกผลการทดลอง
* คำสั่ง pio device monitor ใช้ในการดูผลลัพธ์ของการรันโปรแกรมใน microcontroller ซึ่งแดสงผลออกมาว่า ver started และ ตรวจสอบการแสดงผลของ wifi โดยทดลองค้นหาด้วยโทรศัพท์มือถือ 

### อภิปรายผลการทดลอง
* จากการทดลองดังกล่าว โปรแกรม 06_Wifi-AP-Web-Server ทำให้เราสามารถสร้าง Wifi Access Point ขึ้นมาเองได้ และตรวจสอบพบว่ามีอยู่จริง ดังภาพ

![3](https://user-images.githubusercontent.com/80879395/112326403-12762a00-8ce7-11eb-84f2-49ba58d05b0b.jpg)

### คำถามหลังการทดลอง
หากทำตามขั้นตอนดังกล่าว จะสามารถสร้าง Wifi Access Point ได้เองหรือไม่
* ตอบ ทำได้
