### Project 2 WS2812 RGB

**1.Description**

![](media/wps4.png)

In this project, we will work to use WS2812 LED to display different colors. Its principle is different from the seven-color LED, we need a pin to control it. This is an intelligent external controlled LED light source integrating control circuit and light emitting circuit. The appearance of each LED is the same as that of a 5050 LED and each component is a pixel. There are four LEDs on our motor driver board, namely four pixels. Let's learn how to control it to display any color. 

**2.Component Knowledge**

![](media/wps5.jpg)、

The four pixel LEDs are connected in series, and we can control any one of the LEDs with a pin and make it display any color. The pixel contains an intelligent digital interface data locking signal and amplifying driver circuit as well as a high-precision internal oscillator and a 12V high voltage programmable constant current control part, which effectively ensures the color consistency of the pixel light.  

The data protocol adopts the communication mode of single-line return to zero code. After the pixel is powered on and reset, the S terminal receives the data transmitted from the controller. 

When the first 24bit data extracted by the first pixel, which will be sent to the data latch inside the pixel. This 2812RGB communication protocol and driver has been encapsulated in the bottom layer, we can directly call the interface function to use.  

**3.Test Code** 

```c
#include "rgb.h"

//Create a class called rgb to control rgb, there are four LEDs, and pins are connected to GP7 

RGB rgb(7, 4);  //rgb(pin, num);  num = 0-100

void setup() 
{
  rgb.setBrightness(100);  //rgb.setBrightness(0-255);
  delay(10);
  rgb.clear();  //Turn off all leds
  delay(10);

  rgb.setPixelColor(0, 255, 0, 0);//The first LED is red
  rgb.setPixelColor(1, 0, 255, 0);//The second LED is green
  rgb.setPixelColor(2, 0, 0, 255);//The third LED is blue
  rgb.setPixelColor(3, 255, 255, 255);//The fourth LED is white
  rgb.show();    //Refresh display
}

void loop() 
{
}
```

**4.Test Result**

First, make sure the library files are installed, otherwise the code will fail to compile. Here we use the library file rgb, please refer to the develop environment for installation. After compiling and uploading the code, turn on the switch and power on, and we can see that the 4 LEDs will respectively be red, green, blue and white. 

**5.Code Explanation**

| include "rgb.h"                  | Import the rgb file                                          |
| -------------------------------- | ------------------------------------------------------------ |
| RGB rgb(pin, num);               | Create an instance to drive the 2812 LEDs, pin = pin number, num = LED number. Here we connect to GPIO7 , 4 LED, that is(7， 4) |
| rgb.setBrightness(100);          | Set the brightness of the LED to 100 and the range is 0 to 255, the larger the value, the brighter the LED will be. |
| rgb.clear();                     | Close all the LEDs                                           |
| rgb.setPixelColor(num, r, g, b); | Set the color of the LED. Num is the LED number and the four LED numbers are 0, 1, 2, and 3 respectively. r, g, and b are the colors of the LEDs. |
| rgb.show();                      | Refresh display                                              |
| random(0, 255);                  | Select a random number from 0 to 255                         |

**6.Expanded Project Flowing Water Light** 

```
#include "rgb.h"
//Create a class called rgb to control rgb, there are four LEDs, and pins are connected to GP7 
RGB rgb(7, 4);  //rgb(pin, num);  num = 0-100

void setup() 
{
  rgb.setBrightness(100);  //rgb.setBrightness(0-255);
  delay(10);
  rgb.clear();  //Turn off all leds
  delay(10);
}

void loop() 
{
  uint8_t r = random(0, 255);
  uint8_t g = random(0, 255);
  uint8_t b = random(0, 255);
  for (uint8_t i = 0; i < 4; i++)
  {
    rgb.setPixelColor(i, r, g, b);//The color of the i +1 LED is random(r,g,b)
    rgb.show();    //Refresh display
    delay(100); //Wait for a while
  }
}
```

Once the code has been uploaded successfully, we will see four LEDs showing the flowing light in a random color. 