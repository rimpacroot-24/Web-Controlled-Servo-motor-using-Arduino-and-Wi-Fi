#include <SoftwareSerial.h>       //Including the software serial library
#include <Servo.h>                //including the servo library
SoftwareSerial esp(4, 5);         
#define DEBUG true                //This will display the ESP8266 messages on Serial Monitor
#define servopin 9                //connect servo on pin 9
Servo ser;                        //variable for servo

int current_pos = 170;      
int v = 10;                 
int minpos = 20; 
int maxpos = 160;

void setup()
{
  ser.attach(servopin);
  ser.write(maxpos);
  ser.detach();
  Serial.begin(115200);
  esp.begin(115200);
  
  sendData("AT+RST\r\n", 2000, DEBUG);                      //This command will reset module
  sendData("AT+CWMODE=1\r\n", 1000, DEBUG);                 //This will set the esp mode as station mode
  sendData("AT+CWJAP=\"wifi-name\",\"wifi-password\"\r\n", 2000, DEBUG);   //This will connect to wifi network
  while(!esp.find("OK")) {                                  //this will wait for connection
  } 
  sendData("AT+CIFSR\r\n", 1000, DEBUG);          //This will show IP address on the serial monitor
  sendData("AT+CIPMUX=1\r\n", 1000, DEBUG);       //This will allow multiple connections
  sendData("AT+CIPSERVER=1,80\r\n", 1000, DEBUG); //This will start the web server on port 80
}

void loop()
{
  if (esp.available())     //check if there is data available on ESP8266
  {
    if (esp.find("+IPD,")) //if there is a new command
    {
      String msg;
      esp.find("?");                           //run cursor until command is found
      msg = esp.readStringUntil(' ');          //read the message
      String command = msg.substring(0, 3);    //command is informed in the first 3 characters "sr1"
      String valueStr = msg.substring(4);      //next 3 characters inform the desired angle
      int value = valueStr.toInt();            //convert to integer
      if (DEBUG) {
        Serial.println(command);
        Serial.println(value);
      }
      delay(100);
      //move servo to desired angle
      if(command == "sr1") {
         //limit input angle
         if (value >= maxpos) {
           value = maxpos;
         }
         if (value <= minpos) {
           value = minpos;
         }
         ser.attach(servopin); //attach servo
         while(current_pos != value) {
           if (current_pos > value) {
             current_pos -= 1;
             ser.write(current_pos);
             delay(100/v);
           }
           if (current_pos < value) {
             current_pos += 1;
             ser.write(current_pos);
             delay(100/v);
           }
         }
         ser.detach(); //dettach
      }
  }
  }
  }
String sendData(String command, const int timeout, boolean debug)
{
  String response = "";
  esp.print(command);
  long int time = millis();
  while ( (time + timeout) > millis())
  {
    while (esp.available())
    {
      char c = esp.read();
      response += c;
    }
  }
  if (debug)
  {
    Serial.print(response);
  }
  return response;
}
