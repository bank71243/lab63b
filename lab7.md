# การทดลองที่ 7 การเขียนโปรแกรมเพื่อสร้างwireless swicthes and control ผ่านเว็บบราวเซอร์
## วัตถุประสงค์
1. เพื่อเข้าใจการทำงานบนโปรแกรมของ microcontroller ESP01
2. เพื่อให้สามารถนำมาใช้ประโยชน์ได้ในชีวิตประจำวัน
3. เพื่อนำการทดลองที่ 5 และ 6 มาพัฒนาต่อ

## อุปกรณ์ที่ใช้
1. microcontroller ESP01
2. relay
3. วงจรแปลงแรงดัน 220 v เป็น 5 v 
4. USB to serial

![image](https://user-images.githubusercontent.com/80879942/112823930-183b8900-90b4-11eb-9452-42dda0b5de24.jpg)

5. หลอดไฟทดลอง

## ศึกษาข้อมูลเบื้องต้น
-  แหล่งข้อมูล https://www.youtube.com/watch?v=T26DVHePlTs
         และ   https://www.youtube.com/watch?v=VX-QNQcO-b4
-  แหล่งข้อมูลเพิ่มเติม https://www.youtube.com/watch?v=oyncRBYtKrw

## วิธีการทำการทดลอง
1. นำ microcontroller ต่อเข้ากับตัว relay และเอาตัววงจรแปลงแรงดันต่อเข้ากับ relay และ ไฟบ้าน 220 v
2. นำหลอดไฟทดลองต่อเข้ากับ relay และกระแสไฟบ้าน 220 v 

![image](https://user-images.githubusercontent.com/80879942/112825745-56d24300-90b6-11eb-9176-d2cc31933d49.jpg)

3. นำ microcontroller เสียบไปที่ USB to serial เพื่อลงโปรแกรมตามนี้

```javascript
#include "ESP8266WiFi.h"
#define RELAY 0
#define LED 2
#ifndef APSSID
#define APSSID "WiFiesp8266" //กำหนดชื่อ wifi ที่ต้องการปล่อย
#define APPSK  "00000000" //กำหนด Password ของ wifi 
#endif   // เข้าใช้งาน Go to http://192.168.4.1 in a web browser
const char* ssid = APSSID; 
const char* password = APPSK;
unsigned char status_RELAY = 0;
WiFiServer server(80);
String header;
String RELAYState = "off";


void setup() {
Serial.begin(115200);
pinMode(RELAY, OUTPUT);
pinMode(LED, OUTPUT);
digitalWrite(RELAY, !status_RELAY);
digitalWrite(LED, HIGH);
Serial.println();
Serial.print("Configuring access point...");
Serial.println(ssid);
WiFi.softAP(ssid, password);
IPAddress myIP = WiFi.softAPIP();
  Serial.print("AP IP address: ");
  Serial.println(myIP);
  server.begin();
  Serial.println("HTTP server started");
digitalWrite(LED, LOW);
}


void loop(){
  WiFiClient client = server.available();   // Listen for incoming clients
  if (client) {                             // If a new client connects,
    Serial.println("New Client.");          // print a message out in the serial port
    String currentLine = "";                // make a String to hold incoming data from the client
    while (client.connected()) {            // loop while the client's connected
      if (client.available()) {             // if there's bytes to read from the client,
        char c = client.read();             // read a byte, then
        Serial.write(c);                    // print it out the serial monitor
        header += c;
        if (c == '\n') {                    // if the byte is a newline character
          // if the current line is blank, you got two newline characters in a row.
          // that's the end of the client HTTP request, so send a response:
          if (currentLine.length() == 0) {
            // HTTP headers always start with a response code (e.g. HTTP/1.1 200 OK)
            // and a content-type so the client knows what's coming, then a blank line:
            client.println("HTTP/1.1 200 OK");
            client.println("Content-type:text/html");
            client.println("Connection: close");
            client.println();
            
            // turns the GPIOs on and off
            if (header.indexOf("GET /on") >= 0) {
              Serial.println("GPIO /on");
              RELAYState = "on";
              digitalWrite(RELAY, LOW);
            } else if (header.indexOf("GET /off") >= 0) {
              Serial.println("GPIO /off");
              RELAYState = "off";
              digitalWrite(RELAY, HIGH);
            }            
            // Display the HTML web page
            client.println("<!DOCTYPE html><html>");
            client.println("<head><meta name=\"viewport\" content=\"width=device-width, initial-scale=1\">");
            client.println("<link rel=\"icon\" href=\"data:,\">");
            // CSS to style the on/off buttons 
            // Feel free to change the background-color and font-size attributes to fit your preferences
            client.println("<style>html { font-family: Helvetica; display: inline-block; margin: 0px auto; text-align: center;}");
            client.println(".button { background-color: #7ad226; border: none; color: white; padding: 16px 40px; border-radius: 31px;");
            client.println("text-decoration: none; font-size: 30px; margin: 2px; cursor: pointer;}");
            client.println(".button2 {background-color: #000000;}</style></head>");
            
            // Web Page Heading
            client.println("<body><h1>ESP8266 Web Server</h1>");
            
            // Display current state, and ON/OFF buttons for GPIO 5  
            client.println("<p>RELAY - State " + RELAYState + "</p>");
            // If the output5State is off, it displays the ON button       
            if (RELAYState=="off") {
              client.println("<p><a href=\"/on\"><button class=\"button\">ON</button></a></p>");
            } else {
              client.println("<p><a href=\"/off\"><button class=\"button button2\">OFF</button></a></p>");
            } 
               
            client.println("</body></html>");
            
            // The HTTP response ends with another blank line
            client.println();
            // Break out of the while loop
            break;
          } else { // if you got a newline, then clear currentLine
            currentLine = "";
          }
        } else if (c != '\r') {  // if you got anything else but a carriage return character,
          currentLine += c;      // add it to the end of the currentLine
        }
      }
    }
    // Clear the header variable
    header = "";
    // Close the connection
    client.stop();
    Serial.println("Client disconnected.");
    Serial.println("");
  }
}
```
4. ทำการ upload program เพื่อให้ได้เลข ip address มา
5. นำ microcontroller มาเสียบกลับที่ relay แล้วทำการจ่ายไฟ
6. นำ ip address ที่ได้ไปใส่ที่ URL ของ web browser บนมือถือ

![image](https://user-images.githubusercontent.com/80879942/112827830-132d0880-90b9-11eb-811d-4fd36f628cb9.jpg)

7 เมื่อทำการกดปุ่ม on 

![image](https://user-images.githubusercontent.com/80879942/112828030-5e471b80-90b9-11eb-9a11-563e7604754f.jpg)

8 หากกดปุ่ม off

![image](https://user-images.githubusercontent.com/80879942/112827830-132d0880-90b9-11eb-811d-4fd36f628cb9.jpg)

## การบันทึกผลการทดลอง

จากการทดลอง
-  การกดปุ่ม on นั้นจะทำให้ตัว relay ที่ทำงานเป็นสวิตซ์ติดทำให้กระแสไฟบ้านไหลผ่านวงจรและทำให้ไฟติด
-  การกดปุ่ม off ก็จะทำให้ relay ปิดสวิตซ์ กระแสจึงไม่ไหลผ่านวงจรไปยังหลอดไฟทดลองได้ ไฟจึงดับ

## อภิปรายผลการทดลอง

  จากการทดลองนี้ได้นำตัวอย่างการเขียนโปรแกรมลงบน microcontroller มาจากอินเทอร์เน็ต ทำให้ code program หลายตัวที่ใช้ในการทดลองนี้มีความซับซ้อนมมากกว่าเนื้อหาที่เรียน ทำให้ไม่สามารถอธิบายได้ทั้งหมดว่ามันทำงานได้อย่างไร ผู้เรียนจึงอธิบายถึงหลักการทำงานของการทดลองได้ไม่ละเอียด และสรุปผลการทดลองตามที่เห็น output 
  
## คำถามหลังการทดลอง

  หากไม่มี relay จะสามารถเปิดปิดหลอดไฟผ่านมือถือได้หรือไม่
