# controlling-LED-via-voice

in this project LED will be controlled via voice by using esp32

'make sure to install the esp32 library'

first I'm going to use a website to convert voice into text 

https://s-m.com.sa/r2/test/i.html

![image](https://github.com/user-attachments/assets/47dc77f9-3a2c-45c2-88ec-77f6d1aa6d0f)


if we say open it will send a number 1 to this website https://s-m.com.sa/r2/test/retrieve.php and we say close it will send 0 

![Screenshot 2024-07-27 115812](https://github.com/user-attachments/assets/2c40f150-93e2-47bb-8270-d23d38e2e4a3)


then i'm going to use this website to control the LED if the output of the website is 1 the LED will be open and if the output is 0 the LED will be closed 





this is a basic circuit 

![Screenshot 2024-07-27 120634](https://github.com/user-attachments/assets/b7218ed0-01ca-406b-ad07-703f254d7db5)







the Arduino code 

```
#include <Arduino.h>
#include <WiFi.h>
#include <WiFiMulti.h>
#include <HTTPClient.h>
#define USE_SERIAL Serial
WiFiMulti wifiMulti;
const int ledPin = 17;


void setup() {

  USE_SERIAL.begin(115200);

  USE_SERIAL.println();
  USE_SERIAL.println();
  USE_SERIAL.println();

  for (uint8_t t = 4; t > 0; t--) {
    USE_SERIAL.printf("[SETUP] WAIT %d...\n", t);
    USE_SERIAL.flush();
    delay(1000);
  }

  wifiMulti.addAP("Your wifi name", "your wife password");

  pinMode(ledPin, OUTPUT);
  digitalWrite(ledPin, LOW);
}

void loop() {
  // wait for WiFi connection
  if ((wifiMulti.run() == WL_CONNECTED)) {

    HTTPClient http;

    USE_SERIAL.print("[HTTP] begin...\n");
    // configure traged server and url
    http.begin("https://s-m.com.sa/r2/test/retrieve.php");  //HTTP

    USE_SERIAL.print("[HTTP] GET...\n");
    // start connection and send HTTP header
    int httpCode = http.GET();

    // httpCode will be negative on error
    if (httpCode > 0) {
      // HTTP header has been send and Server response header has been handled
      USE_SERIAL.printf("[HTTP] GET... code: %d\n", httpCode);

      // file found at server
      if (httpCode == HTTP_CODE_OK) {
        String payload = http.getString();
        USE_SERIAL.println(payload);

         if (payload == "1") {
          digitalWrite(ledPin, HIGH);  // Turn LED on
        } else if (payload == "0") {
          digitalWrite(ledPin, LOW);   // Turn LED off
        }
      }
    } else {
      USE_SERIAL.printf("[HTTP] GET... failed, error: %s\n", http.errorToString(httpCode).c_str());
    }

    http.end();
  }

  delay(5000);
}
```






## Outputs 


if we say close 


![Image (10)](https://github.com/user-attachments/assets/6cf44f9e-1637-4834-b965-72c37d5ae519)



if we say open 


![Image (11)](https://github.com/user-attachments/assets/e6eb9587-eae5-4152-9dba-de9b9a1ce33e)
