### Project 12 Bluetooth Control Smart Car

![](media/wps1-1766557130005-1.png)

**1.Description**

![](media/wps2-1766557153443-3.png)

There is a DX-BT24 5.1 Bluetooth module in this kit. This Bluetooth module comes with 256Kb space and complies with V5.1 BLE Bluetooth specification, which supports AT commands. Users can change parameters such as the baud rate and device name of the serial port as required. 

Furthermore, it supports UART interface and Bluetooth serial port transparent transmission, which also contains the advantages of low cost, small size, low power consumption and high sensitivity for sending and receiving. Notably, it solely needs a few peripheral components to realize its powerful functions.  

In this project, we will use the BT24 Bluetooth module to combine all modules of the car in one code, which contains all functions except IR remote control mentioned in the previous projects. We plan to use mobile APP to connect Bluetooth module, so as to control various functions, which is distinctly simple and convenient. 

We will work to read the control characters sent by each button through the Bluetooth connection of the APP.  

**2.Parameters**

- Bluetooth protocol： Bluetooth Specification V5.1 BLE

- Working frequency：2.4GHz ISM band
- Communication interface： UART
- Power supply：5V/3.3V
- Communication distance: 40m
- Dimension：27(L)mm x 13 (W)mm x 2(H) mm
- Bluetooth name：BT24
- Serial port parameters：9600、eight data bits, one stop bit, no check, no flow control
- Working temperature： MIN:-40℃ - MAX:+85℃

**3.Test Code**

```c
char ble_val;

void setup() 
{
  Serial1.begin(9600);  //Set the baud rate to 9600
}

void loop() 
{
  if (Serial1.available())//If the receiving area is not empty
  {
    char ble_val = Serial1.read();//Read the data of the bluetooth
    Serial.println(ble_val);//The serial monitor prints the read data
  }
}
```

**4.Test Result**

After uploading the code, plug in the Bluetooth, be careful not to plug it upside down. Connect the Bluetooth module and turn on the serial monitor. 

Aim at the Bluetooth module and press the APP button of the mobile phone. We can see the corresponding control characters of the APP button, as shown below.  

![](media/wps3-1766557388926-5.jpg)

**5.Code Explanation**

| Serial1.begin(9600)                                          | Set the baud rate of the Bluetooth serial port to 9600. (Serial port 0 corresponds to Serial1, serial port 1 corresponds to Serial2 ). |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Serial1.available()                                          | If Bluetooth data is received, this value is not 0           |
| char ble_val=Serial1.read();                                 | Read Bluetooth serial port data and define a variable of type char to save |
| Serial.println(ble_val);<br>Serial1.println(ble_val);        | Upper : output to serial monitor<br>Lower: output data to Bluetooth module |
| Serial1.readStringUntil('#');                                | Read the Bluetooth strings, stop reading if "#" appears, note that we use two consecutive such statements, because when we adjust speed, sending strings such as V# 50 #, we first read is "V" (#, then stop), the second read is "50", read here is string instead of an integer value |
| String(speed_str).tolnt();                                   | Convert the numeric string read to an integer value          |
| map(speed_Upper_L,0,100,0,255);                              | Since the speed value we send on our APP is 0~100, we have to map it to 0~ 255 |
| speed_Upper_L<br>speed_Lower_L<br>speed_Upper_R<br>speed_Lower_R | Represents the speed values of the four motors.<br>Range is 0~255 |

**6.Comprehensive Project APP Control Smart Car**

```c
#include "MecanumCar_v2.h"
#include "rgb.h"
#include "ir.h"
#include "Servo.h"

mecanumCar mecanumCar(20, 21);  //sda-->20,scl-->21
RGB rgb(7, 4);  //rgb(pin, num);  num = 0-100
Servo myservo;
IR IRreceive(6);//IR receiver is connected to GP6

#define EchoPin  4  //ECHO to GPIO4
#define TrigPin  3  //TRIG to GPIO3

#define SensorLeft    16   //input pin of left sensor
#define SensorMiddle  17   //input pin of middle sensor
#define SensorRight   18   //input pin of right sensor

String speed_str;
int color_num = 0;
char ble_val;

void setup() 
{
  /*Bluetooth serial port is set to UART0,TX为GPIO0*/

  /*Bluetooth serial port is set to UART0,RX为GPIO1*/
  Serial1.begin(9600);  //Set the Bluetooth string baud rate to 9600

  pinMode(EchoPin, INPUT);    //The ECHO pin is set to input mode
  pinMode(TrigPin, OUTPUT);   //The TRIG pin is set to output mode

  /****All interfaces of the line tracking sensor are set to input mode***/
  pinMode(SensorLeft, INPUT);
  pinMode(SensorMiddle, INPUT);
  pinMode(SensorRight, INPUT);

  myservo.attach(2);  // attaches the servo on GPIO2 to the servo object
  myservo.write(90);
  delay(500);

  rgb.setBrightness(100);  //rgb.setBrightness(0-255);
  delay(10);
  rgb.clear();  //Turn off all leds
  delay(10);
  rgb.show();

  mecanumCar.Init(); //Initialize the seven-color LED and motor drive
}

void loop() 
{
  int key = IRreceive.getKey();
  if (key != -1) 
  {
     //Serial.println(key);
     switch (key)
     {
        case 64: mecanumCar.Stop();       break;  //Stop
        case 70: mecanumCar.Advance();    break;  //Go ahead
        case 21: mecanumCar.Back();       break;  //Move back
        case 68: mecanumCar.Turn_Left();  break;  //Turn left
        case 67: mecanumCar.Turn_Right(); break;  //Turn right
     }
  }
  if (Serial1.available())//If the receiving area is not empty
  {
    ble_val = Serial1.read();//Read the data of the bluetooth
    Serial.println(ble_val);//The serial monitor prints the read data
    switch (ble_val)
    {
      /*********************The car driving************************/
      case 's': mecanumCar.Stop();       break;  //Stop
      case 'a': mecanumCar.Advance();    break;  //Go ahead
      case 'c': mecanumCar.Back();       break;  //Move back
      case 'b': mecanumCar.Turn_Left();  break;  //Turn left
      case 'd': mecanumCar.Turn_Right(); break;  //Turn right
      case 'k': mecanumCar.L_Move();     break;  //Left shift
      case 'h': mecanumCar.R_Move();     break;  //Right shift
      case 'l': mecanumCar.LU_Move();    break;  //Upper left move
      case 'j': mecanumCar.LD_Move();    break;  //lower left move
      case 'g': mecanumCar.RU_Move();    break;  //Upper right move
      case 'i': mecanumCar.RD_Move();    break;  //lower right move
      case 'e': mecanumCar.drift_left(); break;  //Drift
      case 'f': mecanumCar.drift_right(); break; //Drift 
      
      case 'p': Line_Tracking();   break;  //Tracking
      case 'q': ult_following();   break;  //Follow
      case 'r': ult_avoiding();    break;  //Obstacle Avoidance
      
      case 't': mecanumCar.right_led(1);  mecanumCar.left_led(1); break;  //Open the 7-color LED
      case 'u': mecanumCar.right_led(0);  mecanumCar.left_led(0); break;  //Close the 7-color LED
      case 'm': color_num++; showColor(); break;  //Switching the color of 2812 LED
      
      case 'o': rgb.clear(); break;  //Close the 2812 LED
      case 'n': color_num--; showColor(); break;  //Switching the color of 2812 LED
      
       case 'v':   /*Read left front motor M2 speed*/
        Serial1.readStringUntil('#');  //Fail to read data the first time, read once
        speed_str = Serial1.readStringUntil('#');  //Save the speed data read for the second time
        speed_Upper_L = String(speed_str).toInt();      //The speed value is a string and needs to be converted to an integer
        speed_Upper_L = map(speed_Upper_L, 0, 100, 0, 255);  //Mapping from 0 to 100 to 0 to 255
        //Serial.println(speed_Upper_L);  //Serial debugging
        break;
      case 'w':   /*Read left rear M3 motor speed*/
        Serial1.readStringUntil('#');
        speed_str = Serial1.readStringUntil('#');
        speed_Lower_L = String(speed_str).toInt();
        speed_Lower_L = map(speed_Lower_L, 0, 100, 0, 255);
        //Serial.println(speed_Lower_L);
        break;
      case 'x':   /*Read right front M1 motor speed*/
        Serial1.readStringUntil('#');
        speed_str = Serial1.readStringUntil('#');
        speed_Upper_R = String(speed_str).toInt();
        speed_Upper_R = map(speed_Upper_R, 0, 100, 0, 255);
        //Serial.println(speed_Upper_R);
        break;
      case 'y':   /*Read right rear M4 motor speed*/
        Serial1.readStringUntil('#');
        speed_str = Serial1.readStringUntil('#');
        speed_Lower_R = String(speed_str).toInt();
        speed_Lower_R = map(speed_Lower_R, 0, 100, 0, 255);
        //Serial.println(speed_Lower_R);
        break;

      default: break;
    }
  }
}

void showColor() 
{
  //  Serial.print("color num:"); //Serial debugging
  //  Serial.println(color_num);
  // There are only 7 colors, you can add them yourself
  if (color_num > 6)color_num = 0;
  if (color_num < 0)color_num = 6;
  switch (color_num) 
  {
    case  0:
      for (int i = 0; i < 4; i++) 
      {
        rgb.setPixelColor(i, 255, 0, 0);  //The iLED is red

      }
      rgb.show();                        //Display
      break;
    case  1:
      for (int i = 0; i < 4; i++) 
      {
        rgb.setPixelColor(i, 255, 80, 0); //The iLED is orange
      }
      rgb.show();                        //Display

      break;
    case  2:
      for (int i = 0; i < 4; i++) 
      {
        rgb.setPixelColor(i, 255, 255, 0); //The iLED is yellow
      }
      rgb.show();                        //Display
      break;
    case  3:
      for (int i = 0; i < 4; i++)
      {
        rgb.setPixelColor(i, 0, 255, 0);   //The iLED is green
      }
      rgb.show();                        //Display
      break;
    case  4:
      for (int i = 0; i < 4; i++) 
      {
        rgb.setPixelColor(i, 0, 0, 255);   //The iLED is blue
      }
      rgb.show();                        //Display
      break;
    case  5:
      for (int i = 0; i < 4; i++) {
        rgb.setPixelColor(i, 0, 255, 255); //The iLED is indigo
      }
      rgb.show();                        //Display
      break;
    case  6:
      for (int i = 0; i < 4; i++) 
      {
        rgb.setPixelColor(i, 160, 32, 240);//The iLED is purple
      }
      rgb.show();                        //Display
      break;
      default : break;
  }
}

void Line_Tracking(void)  //Tracking black line
{  
  while (1)
  {
      uint8_t SL = digitalRead(SensorLeft);   //Read the value of the left line tracking sensor
    uint8_t SM = digitalRead(SensorMiddle); //Read the value of the middle line tracking sensor
    uint8_t SR = digitalRead(SensorRight);  //Read the value of the right line tracking sensor
    if (SM == HIGH) 
    {
      if (SL == LOW && SR == HIGH)  // black on right, white on left, turn right
      { 
        mecanumCar.Turn_Right();
      }
      else if (SR == LOW && SL == HIGH) // black on left, white on right, turn left
      {  
        mecanumCar.Turn_Left();
      }
      else // white on both sides, going forward
      {  
        mecanumCar.Advance();
      }
    }
    else 
    {
      if (SL == LOW && SR == HIGH) // black on right, white on left, turn right
      { 
        mecanumCar.Turn_Right();
      }
      else if (SR == LOW && SL == HIGH)// white on right, black on left, turn left
      {  
        mecanumCar.Turn_Left();
      }
      else // all white, stop
      { 
        mecanumCar.Stop();
      }
    }
    if (Serial1.available())//If the receiving area is not empty
    {
      ble_val = Serial1.read();//Read Bluetooth data
      if (ble_val == 's') 
      {
        mecanumCar.Stop();
        break;
      }
    }
  }
}

void ult_following(void)
{
  while (1)
  {
    int distance = get_distance();  //Get the distance and save in the distance variable 
    //Serial.println(distance);
    if (distance <= 15)  //The range of moving back
    {
      mecanumCar.Back();
    }
    else if (distance <= 25)  //The range of stop
    {
      mecanumCar.Stop();
    }
    else if (distance <= 45) //The range of advance    
    {
      mecanumCar.Advance();
    }
    else  //Other cases stop
    {
      mecanumCar.Stop();
    }
    if (Serial1.available())//If the receiving area is not empty
    {
      ble_val = Serial1.read();//Read Bluetooth data
      if (ble_val == 's') 
      {
        mecanumCar.Stop();
        break;
      }
    }
  }
}

void ult_avoiding(void)
{
  int distance_M, distance_L, distance_R;
  while (1)
  {
    distance_M = get_distance();  //Get the distance and save in the distance variable
    if (distance_M < 20) //When the distance in front is less than 20cm
    {
      mecanumCar.Stop();  //Robot stop
      delay(500); //Delay in 500ms
      myservo.write(180);  //Ultrasonic cradle head turns right

      delay(500); //Delay in 500ms
      distance_L = get_distance();  //Assign the left ultrasonic distance to variable a1

      delay(100); //Read values when stable
      myservo.write(0); //Ultrasonic cradle head turns right
      delay(500); //Delay in 500ms
      distance_R = get_distance(); //Assign the right ultrasonic distance to variable a2
      delay(100); //Read values when stable
      myservo.write(90);  //Return to the 90 degree position
      delay(500);
      if (distance_L > distance_R) //When the distance on the left is greater than right
      { 
        mecanumCar.Turn_Left();  //Robot turns left
        delay(300);  //Turn left 700ms
      } 
      else 
      {
        mecanumCar.Turn_Right(); //Robot turns right
        delay(300);
      }
    }
    else { //If the distance in front is >=20cm, the robot will advance

      mecanumCar.Advance(); //Advance
    }
    if (Serial1.available())//If the receiving area is not empty
    {
      ble_val = Serial1.read();//Read Bluetooth data
      if (ble_val == 's') 
      {
        mecanumCar.Stop();
        break;
      }
    }
  }
}

int get_distance(void) //Ultrasonic detects the distance
{     
  int dis;
  digitalWrite(TrigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(TrigPin, HIGH); //Give the TRIG a high level at least 10 µ s to trigger
  delayMicroseconds(10);
  digitalWrite(TrigPin, LOW);
  dis = pulseIn(EchoPin, HIGH) / 58.2; //Work out the distance

  delay(30);
  return dis;
}
```

After uploading the code successfully and power up, plug in the bluetooth, then connect it to BT24. We can do the following operations:  

Click![](media/wps4-1766558821103-7.jpg)to open the 7-color LED, click it again the LED will be turned off.

Click![](media/wps5-1766558839640-9.jpg)to enter tracking mode, click it again will exit the mode.

Click![](media/wps6-1766558858670-11.jpg)to enter follow mode, click it again will exit the mode.

Click![](media/wps7-1766558878901-13.jpg)to enter obstacle avoidance mode, click it again will exit the mode.

![](media/wps8.jpg)Pulling these two strips will change the speed of the two motors on the left and the same operation on the right.

![](media/wps9.jpg)These buttons are used to switch the color of the four 2812 LEDs under the base board, and the middle button is used to close the function.  

The rest of the buttons are all used to drive the car, but unlike the other buttons, they drive when we press them and stop when we release them.

![](media/wps10.png)