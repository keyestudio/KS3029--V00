### Project 8 Ultrasonic Following Smart Car

![](media/wps2-1766472760717-1.png)

**1.Description**

In this project, we will work to combine an ultrasonic sensor with motors to make an automatic follow smart car.

The ultrasonic sensor detects the smart car and the obstacle distance to control the motion status of car.

**2.Flow Diagram**

![](media/wps3.png)

**3.Test Code**

```c
#include "MecanumCar_v2.h"
#include "Servo.h"

mecanumCar mecanumCar(20, 21);  //sda-->20,scl-->21
Servo myservo;    //Define an instance of a servo

#define EchoPin  4  //ECHO to GPIO4
#define TrigPin  3  //TRIG to GPIO3

void setup() 
{
  pinMode(EchoPin, INPUT);    //The ECHO pin is set to input mode
  pinMode(TrigPin, OUTPUT);   //The TRIG pin is set to output mode
  myservo.attach(2);  // attaches the servo on GPIO2 to the servo object
  myservo.write(90); //Rotate to 90 degrees
  mecanumCar.Init(); //Initialize the seven-color LED and the motor drive
}

void loop() 
{
  int distance = get_distance();  //Get the distance and save in the distance variable  
  Serial.println(distance);
  if (distance <= 15)  //Receding range
  {
    mecanumCar.Back();
  }
  else if (distance <= 25)  //Stop range
  {
    mecanumCar.Stop();
  }
  else if (distance <= 45) //Range of advance
  {
    mecanumCar.Advance();
  }
  else  //Other cases stop
  {
    mecanumCar.Stop();
  }
}

int get_distance(void) //Ultrasonic detects the distance
{    
  int dis;
  digitalWrite(TrigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(TrigPin, HIGH); //Give the TRIG a high level of at least 10 µ s to trigger
  delayMicroseconds(10);
  digitalWrite(TrigPin, LOW);
  dis = pulseIn(EchoPin, HIGH) / 58.2; //Work out the distance
  delay(30);
  return dis;
}
```

**4.Test Result**

After uploading the code successfully, turn on the switch and the car will follow in a straight line. We put the palm of our hand in front of the ultrasonic, slowly forward, the car will follow our palm to move.

**5.Code Explanation**

| myservo.write(90);                                           | Make the servo rotate to 90 degrees                          |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| int distance = get_distance();                               | Define an integer variable to store the measured distance, and then control the car driving according to it . |
| if (distance <= 15)<br>{<br>     mecanumCar.Back();<br>}     | When the front distance measured is less than 15cm, the car moves back. |
| else if (distance <= 25)<br>{<br>     mecanumCar.Stop();<br>} | Otherwise, the car will stop when the front distance is less than 25cm |
| else if (distance <= 45)<br/>{<br/>     mecanumCar.Advance();<br/>} | The car will go forward when the front distance is less than 45cm |
| else if (distance <= 45)<br/>{<br/>     mecanumCar.Stop();<br/>} | The car will stop when the front distance is bigger than 45cm |

