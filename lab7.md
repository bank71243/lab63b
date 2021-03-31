# การทดลองที่ 7 การเปิดปิดไฟ LED ผ่าน WiFi โดยใช้ เว็บบราวเซอร์
### วัตถุประสงค์
1. เพื่อคิดค้นโปรแกรมใหม่ๆโดยผสมผสานระหว่างความคิดสร้างสรรค์กับความรู้พื้นฐานจากแลปครั้งก่อน
2. เพื่อศึกษาวิธีการเขียนโปรแกรมที่ถูกต้อง
3. เพื่อศึกษาการแสดงผลของINPUTต่างๆในรูปแบบของการเชื่อมต่อสัญญาณwifi

### อุปกรณ์ที่ใช้
1. Goouuu ESP32 Development Board WiFi+Bluetooth
2. Micro USB Cable Wire 1m for NodeMCU
3. โฟโต้บอร์ด
4. Jumper (M2M) cable 10 cm
5. หลอดไฟทดลอง

### ศึกษาข้อมูลเบื้องต้น
-  https://www.youtube.com/watch?v=T26DVHePlTs
-  https://www.youtube.com/watch?v=VX-QNQcO-b4
-  แหล่งข้อมูลเพิ่มเติม https://www.robotsiam.com/article/36/%E0%B8%81%E0%B8%B2%E0%B8%A3%E0%B9%83%E0%B8%8A%E0%B9%89-esp32-%E0%B9%80%E0%B8%9B%E0%B8%B4%E0%B8%94%E0%B8%9B%E0%B8%B4%E0%B8%94%E0%B9%84%E0%B8%9F-led-%E0%B8%9C%E0%B9%88%E0%B8%B2%E0%B8%99-wifi-%E0%B9%82%E0%B8%94%E0%B8%A2%E0%B9%83%E0%B8%8A%E0%B9%89-%E0%B9%80%E0%B8%A7%E0%B9%87%E0%B8%9A%E0%B8%9A%E0%B8%A3%E0%B8%B2%E0%B8%A7%E0%B9%80%E0%B8%8B%E0%B8%AD%E0%B8%A3%E0%B9%8C

### วิธีการทำการทดลอง
1. ติดตั้ง Arduino core for ESP32
- ติดตั้ง Arduino core for ESP32    
  - ลิงค์ดาวโหลด Arduino (IDE) https://www.arduino.cc/en/Main/Software
  - ติดตั้ง แพลตฟอร์ม ESP32
  - เปิด Arduino IDE และไปที่ File > Preferences
         
![1](https://user-images.githubusercontent.com/80879395/113191407-b5025000-9287-11eb-97fa-cee7329f9573.jpg)
       
  - คัดลอก URL ( https://dl.espressif.com/dl/package_esp32_index.json ) Additional Board Manager URLs: แล้ว คลิก OK
        
![2](https://user-images.githubusercontent.com/80879395/113191472-ca777a00-9287-11eb-8283-244456f8aee6.jpg)
                  
  - จากนั้นไปที่ตัวจัดการบอร์ดโดยไปที่ Tools > Board: > Boards Manager...
  - ที่ช่องค้นหา พิมพ์ esp32 จะพบ esp32 by Espressif Systems แล้วคลิก Install
         
![image](https://user-images.githubusercontent.com/80879395/113188778-acf4e100-9284-11eb-8d40-d79cdcc61aad.png)
        
2. ประกอบวงจร Goouuu ESP32 ลงบน Breadboard
  - ต่อ Jumper (M2M) จากขา GND ของ ESP32 เข้ากับ ไฟ- Breadboard
  - ต่อ LED ตัวที่1 ขาที่ยาวกว่า เข้าที่ขา G12 ขาที่สั้น เข้าที่ ไฟ- Breadboard
  - ต่อ LED ตัวที่2 ขาที่ยาวกว่า เข้าที่ขา G27 ขาที่สั้น เข้าที่ ไฟ- Breadboard
  - ต่อ LED ตัวที่3 ขาที่ยาวกว่า เข้าที่ขา G25 ขาที่สั้น เข้าที่ ไฟ- Breadboard
  - ต่อ LED ตัวที่4 ขาที่ยาวกว่า เข้าที่ขา G32 ขาที่สั้น เข้าที่ ไฟ- Breadboard
  
  ![image](https://user-images.githubusercontent.com/80879395/113189177-1aa10d00-9285-11eb-8d97-d10092ebb1e0.png)

3. เชื่อมต่อสาย Micro USB ระหว่าง คอมพิวเตอร์ กับ ESP32
  - เปิดโปรแกรม Arduino IDE ขึ้นมา เขียนโปรแกรม หรือ  Sketch

```javascript
#include <WiFi.h>
 
const char* ssid     = "YOUR_NETWORK_NAME";
const char* password = "YOUR_NETWORK_PASSWORD";
 
WiFiServer server(80);
 
void setup()
{
  Serial.begin(115200);
 
  pinMode(12, OUTPUT);      // set the LED pin mode
  pinMode(27, OUTPUT);      // set the LED pin mode
  pinMode(25, OUTPUT);      // set the LED pin mode
  pinMode(32, OUTPUT);      // set the LED pin mode
 
  delay(10);
 
  // We start by connecting to a WiFi network
 
  Serial.println();
  Serial.println();
  Serial.print("Connecting to ");
  Serial.println(ssid);
 
  WiFi.begin(ssid, password);
 
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
 
  Serial.println("");
  Serial.println("WiFi connected.");
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
 
  server.begin();
 
}
 
int value = 0;
 
void loop() {
  WiFiClient client = server.available();   // listen for incoming clients
 
  if (client) {                             // if you get a client,
    Serial.println("New Client.");           // print a message out the serial port
    String currentLine = "";                // make a String to hold incoming data from the client
    while (client.connected()) {            // loop while the client's connected
      if (client.available()) {             // if there's bytes to read from the client,
        char c = client.read();             // read a byte, then
        Serial.write(c);                    // print it out the serial monitor
        if (c == '\n') {                    // if the byte is a newline character
 
          // if the current line is blank, you got two newline characters in a row.
          // that's the end of the client HTTP request, so send a response:
          if (currentLine.length() == 0) {
            // HTTP headers always start with a response code (e.g. HTTP/1.1 200 OK)
            // and a content-type so the client knows what's coming, then a blank line:
            client.println("HTTP/1.1 200 OK");
            client.println("Content-type:text/html");
            client.println();
 
            // the content of the HTTP response follows the header:
            client.print("Click <a href=\"/12H\">here</a> to turn the LED on pin 12 on.<br>");
            client.print("Click <a href=\"/12L\">here</a> to turn the LED on pin 12 off.<br>");
            client.print("<br>");
 
            client.print("Click <a href=\"/27H\">here</a> to turn the LED on pin 27 on.<br>");
            client.print("Click <a href=\"/27L\">here</a> to turn the LED on pin 27 off.<br>");
            client.print("<br>");
 
            client.print("Click <a href=\"/25H\">here</a> to turn the LED on pin 25 on.<br>");
            client.print("Click <a href=\"/25L\">here</a> to turn the LED on pin 25 off.<br>");
            client.print("<br>");
 
            client.print("Click <a href=\"/32H\">here</a> to turn the LED on pin 32 on.<br>");
            client.print("Click <a href=\"/32L\">here</a> to turn the LED on pin 32 off.<br>");
            client.print("<br>");
 
            // The HTTP response ends with another blank line:
            client.println();
            // break out of the while loop:
            break;
          } else {    // if you got a newline, then clear currentLine:
            currentLine = "";
          }
        } else if (c != '\r') {  // if you got anything else but a carriage return character,
          currentLine += c;      // add it to the end of the currentLine
        }
 
        // Check to see if the client request was "GET /xH" or "GET /xL":
        if (currentLine.endsWith("GET /12H")) {
          digitalWrite(12, HIGH);               // GET /H turns the LED on
        }
        if (currentLine.endsWith("GET /12L")) {
          digitalWrite(12, LOW);                // GET /L turns the LED off
        }
 
        if (currentLine.endsWith("GET /27H")) {
          digitalWrite(27, HIGH);               // GET /H turns the LED on
        }
 
        if (currentLine.endsWith("GET /27L")) {
          digitalWrite(27, LOW);                // GET /L turns the LED off
        }
 
        if (currentLine.endsWith("GET /25H")) {
          digitalWrite(25, HIGH);               // GET /H turns the LED on
        }
        if (currentLine.endsWith("GET /25L")) {
          digitalWrite(25, LOW);                // GET /L turns the LED off
        }
 
        if (currentLine.endsWith("GET /32H")) {
          digitalWrite(32, HIGH);               // GET /H turns the LED on
        }
        if (currentLine.endsWith("GET /32L")) {
          digitalWrite(32, LOW);                // GET /L turns the LED off
        }
 
      }
    }
    // close the connection:
    client.stop();
    Serial.println("Client Disconnected.");
  }
}
```

![image](https://user-images.githubusercontent.com/80879395/113189997-0ad5f880-9286-11eb-82a3-2c7276939515.png)

4. ทำการอัพโหลดโปรแกรมลงบอร์ด
   - กดปุ่มลูกศรชี้ไปทางขวาเพื่ออัพโหลด

![image](https://user-images.githubusercontent.com/80879395/113190451-7f109c00-9286-11eb-9b8c-a683f43af590.png)

   - เมื่อสำเร็จจะขึ้นว่า Done uploading. ที่แถบด้านล่าง
   - ไปที่เมนู Tool > Serial Monitor
   - เลือก Both NL & CR และ เลือก 115200 baud
   - Serial Monitor จะแสดง ไอพี ของ ESP32 ในตัวอย่างคือ 192.168.1.38
   
![image](https://user-images.githubusercontent.com/80879395/113190674-c860eb80-9286-11eb-8223-c12cf69f0066.png)

5. ทดสอบการทำงาน
   - เปิดเว็บบราวเซอร์ ที่ URL ป้อนไอพีที่ได้จาก Serial Monitor ในตัวอย่างคือ 192.168.1.38 แล้วคลิกที่ here เพื่อทดสอบการ เปิดปิดไฟ LED

![image](https://user-images.githubusercontent.com/80879395/113190832-fd6d3e00-9286-11eb-9c56-39365fb6941a.png)

![image](https://user-images.githubusercontent.com/80879395/113190868-0bbb5a00-9287-11eb-8f96-ac22d6044953.png)


### การบันทึกผลการทดลอง

จากการทดลอง
-  การกดปุ่ม on นั้นจะทำให้ตัว relay ที่ทำงานเป็นสวิตซ์ติดทำให้กระแสไฟบ้านไหลผ่านวงจรและทำให้ไฟติด
-  การกดปุ่ม off ก็จะทำให้ relay ปิดสวิตซ์ กระแสจึงไม่ไหลผ่านวงจรไปยังหลอดไฟทดลองได้ ไฟจึงดับ

### อภิปรายผลการทดลอง

  จากการทดลองนี้ได้นำตัวอย่างการเขียนโปรแกรมลงบน microcontroller มาจากอินเทอร์เน็ต ทำให้ code program หลายตัวที่ใช้ในการทดลองนี้มีความซับซ้อนมมากกว่าเนื้อหาที่เรียน ทำให้ไม่สามารถอธิบายได้ทั้งหมดว่ามันทำงานได้อย่างไร ผู้เรียนจึงอธิบายถึงหลักการทำงานของการทดลองได้ไม่ละเอียด และสรุปผลการทดลองตามที่เห็น output 
  
### คำถามหลังการทดลอง

  หากไม่มี relay จะสามารถเปิดปิดหลอดไฟผ่านมือถือได้หรือไม่
