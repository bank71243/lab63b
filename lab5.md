# การทดลองที่ 5 การเขียนโปรแกรมเชื่อมต่อไวไฟและเว็บเซอร์เวอร์
### วัตถุประสงค์ 
1. เพื่อเข้าใจการเขียนโปรแกรมเชื่อมต่อ wifi และ web server

### อุปกรณ์ที่ใช้
1. CPU
2. microcontroller ESP-01
3. อุปกรณ์เชื่อมต่อ USB เข้าไปยัง serial
4. wifi ที่ต้องการเชื่อมกับ microcontroller

### แหล่งข้อมูลที่ศึกษา
https://www.youtube.com/watch?v=VX-QNQcO-b4

### วิธีการทำการทดลอง
1. ดูที่ตัวอย่างโปรแกรม ที่โฟลเดอร์ pattani
* พิมพ์ cd pattani เพื่อไปยังโฟลเดอร์
  * ไปที่ตัวอย่างที่ 5
    * พิมพ์ cd 05_Wifi-Web-Server
2. ดู source code program 
* พิมพ์ vi src/main.cpp
* โปรแกรมนี้เป็นการเชื่อมต่อ wifi จึงต้องใส่ชื่อ wifi และ password
* ส่วนของ set up เป็นการเชื่อมต่อ wifi ที่ใส่ชื่อตอนแรก
  * set up webserver แสดงผลเป็น Hello cnt
  * cnt++ หมายถึง การนับเพิ่มเรื่อยๆ 
* ส่วนของ loop  
```javascript
#include <ESP8266WiFi.h>
//#include <WiFiClient.h>
#include <ESP8266WebServer.h>

const char* ssid = "HI_BMFWIFI_2.4G";
const char* password = "0819110933";

ESP8266WebServer server(80);

int cnt = 0;

void setup(void){
	Serial.begin(115200);

	WiFi.mode(WIFI_STA);
	WiFi.begin(ssid, password);
	while (WiFi.status() != WL_CONNECTED) {
		delay(500);
		Serial.print(".");
	}
	Serial.print("\n\nIP address: ");
	Serial.println(WiFi.localIP());

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

3. อัพโหลดโปรแกรม 05_Wifi-Web-Server เข้าไปยัง microcontroller
   * พิมพ์ pio run -t upload
   
4. ทำการเสียบ microcontroller เข้าทาง serial port ของ USB 
   * ในขณะที่ program กำลังรันข้อมูล
     * กดปุ่มสีแดง เพื่อให้เกิดการ reset 
   * สังเกตผลลัพธ์ที่แสดงผลผ่านคอมพิวเตอร์
     * พิมพ์ pio device monitor 
     * ผลลัพธ์ที่แสดง คือ ip address
     * copy ip adress ไปที่ browser สำหรับทดสอบ

### การบันทึกผลการทดลอง
* การใช้คำสั่ง pio device monitor เพื่อดูผลลัพธ์ของการรันโปรแกรมในไมโครคอนโทรเลอร์ ได้แสดงที่อยู่ไอพีขึ้นมา 
และเมื่อทำการคัดลอกไปที่บราวเซอร์ ผลลัพธ์ที่แสดงจะขึ้นว่า Hello 1 โดย cnt เปลี่ยนตัวแปรไปเรื่อยๆ

### อภิปรายผลการทดลอง
* pio run -t upload นั้นใช้ในการอัพโหลดข้อมูลจาก 05_Wifi-Web-Server ไปยัง microcontroller โดยใช้คำสั่ง pio device monitor ที่ใช้ดูผลลัพธ์ของโปรแกรมที่ถูกอัพโหลด ซึ่งก็คือ ip address แล้วจึงทำการคัดลอกเพื่อไปที่บราวเซอร์สำหรับทดสอบ

### คำถามหลังการทดลอง
จากโค้ดของโปรแกรมดังกล่าว บรรทัดใดใช้แสดงชื่อของ wifi
* ตอบ const char* ssid
