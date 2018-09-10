# Make a Digital Timer!
 
## Overview
For this assignment, you are going to 

A) [Solder your LCD panel](#part-a-solder-your-lcd-panel)

B) [Write text to an LCD Panel](#part-b-writing-to-the-lcd) 

c) [Using a time-based digital sensor!](#part-c-using-a-time-based-digital-sensor)

D) [Make your Arduino sing!](#part-d-make-your-arduino-sing)

E) [Make your own timer](#part-e-make-your-own-timer) 


## Part A. Solder your LCD panel

**Take a picture of your soldered panel and add it here!**

![soldr pic](https://github.com/Yiyuan7/IDD-Fa18-Lab2/blob/master/solder.JPG)

## Part B. Writing to the LCD
 
**a. What voltage level do you need to power your display?**

I used 5 voltage.

**b. What voltage level do you need to power the display backlight?**

I used 3.3 voltage.
   
**c. What was one mistake you made when wiring up the display? How did you fix it?**

I didn't have any problem. :p

**d. What line of code do you need to change to make it flash your name instead of "Hello World"?**

I changed 'hello world' to 'yf323' 
 
**e. Include a copy of your Lowly Multimeter code in your lab write-up.**

#include <LiquidCrystal.h>

#define ENC_A 6 //these need to be digital input pins
#define ENC_B 7

const int rs = 12, en = 11, d4 = 5, d5 = 4, d6 = 3, d7 = 2;
LiquidCrystal lcd(rs, en, d4, d5, d6, d7);

void setup()
{
  /* Setup encoder pins as inputs */
  pinMode(ENC_A, INPUT_PULLUP);
  pinMode(ENC_B, INPUT_PULLUP);
  pinMode(8, OUTPUT);
  lcd.begin(16, 2);
  
  Serial.begin (9600);
  Serial.println("Start");
}
 
void loop()
{
  static unsigned int counter4x = 0;      //the SparkFun encoders jump by 4 states from detent to detent
  static unsigned int counter = 0;
  static unsigned int prevCounter = 0;
  int tmpdata;
  tmpdata = read_encoder();
  if(tmpdata) {
    counter4x += tmpdata;
    counter = counter4x/4;
    if (prevCounter != counter){
      Serial.print("Counter value: ");
      Serial.println(counter);
    }
    prevCounter = counter;
  }
}
 
/* returns change in encoder state (-1,0,1) */
int read_encoder()
{
  static int enc_states[] = {
    0,-1,1,0,1,0,0,-1,-1,0,0,1,0,1,-1,0  };
  static byte ABab = 0;
  ABab *= 4;                   //shift the old values over 2 bits
  ABab = ABab%16;      //keeps only bits 0-3
  ABab += 2*digitalRead(ENC_A)+digitalRead(ENC_B); //adds enc_a and enc_b values to bits 1 and 0
  return ( enc_states[ABab]);
}

## Part C. Using a time-based digital sensor

**Upload a video of your working rotary encoder here.**

[video](https://drive.google.com/file/d/14t0U258xU2mb8yPvFbiq_QKBAJNW1NoM/view?usp=sharing)

## Part D. Make your Arduino sing!

**a. How would you change the code to make the song play twice as fast?**

I multiplied duration with 0.5
 
**b. What song is playing?**

Star War theme song

## Part E. Make your own timer

**a. Make a short video showing how your timer works, and what happens when time is up!**

[Video](https://drive.google.com/file/d/1wTW278W7xBlB4dKmD_oRZBtiHggA3H7P/view?usp=sharing)

code:
#include <LiquidCrystal.h>

#define ENC_A 6 //these need to be digital input pins
#define ENC_B 7

const int rs = 12, en = 11, d4 = 5, d5 = 4, d6 = 3, d7 = 2;
LiquidCrystal lcd(rs, en, d4, d5, d6, d7);

bool countDownStarted = false; 
int lastSecond = 0;

int displayTime = 10;
int MAX_COUNT_DOWN_TIME = 99;

void setup()
{
  /* Setup encoder pins as inputs */
  pinMode(ENC_A, INPUT_PULLUP);
  pinMode(ENC_B, INPUT_PULLUP);
  pinMode(8, OUTPUT);
  lcd.begin(16, 2);
  
  Serial.begin (9600);
  Serial.println("Start");
}
 
void loop()
{
  static unsigned int counter4x = 0;      //the SparkFun encoders jump by 4 states from detent to detent
  static unsigned int counter = 0;
  static unsigned int prevCounter = 0;
  int tmpdata;
  countDownStarted = counter == 0 ? false : true;
  if (displayTime == 0){
     digitalWrite(8, HIGH);
  } else {
     digitalWrite(8, LOW);
  }
  tmpdata = read_encoder();
  if(tmpdata) {
    counter4x += tmpdata;
    counter = counter4x/4;
    if (prevCounter != counter){
      Serial.print("Counter value: ");
      Serial.println(counter);
//      lcd.clear();
//      lcd.print(counter);
//      lcd.display();
      displayTime = counter;
    }
    prevCounter = counter;
  }

   if (countDownStarted) {
    if ((millis() / 1000 - lastSecond) > 1) {
      // triggering this function in every second      
      lastSecond = millis() / 1000;
      displayTime = displayTime > 0 ? displayTime - 1 : 0;
      lcd.clear();
      lcd.print(displayTime);
      lcd.display();      
    }
  } 
}
 
/* returns change in encoder state (-1,0,1) */
int read_encoder()
{
  static int enc_states[] = {
    0,-1,1,0,1,0,0,-1,-1,0,0,1,0,1,-1,0  };
  static byte ABab = 0;
  ABab *= 4;                   //shift the old values over 2 bits
  ABab = ABab%16;      //keeps only bits 0-3
  ABab += 2*digitalRead(ENC_A)+digitalRead(ENC_B); //adds enc_a and enc_b values to bits 1 and 0
  return ( enc_states[ABab]);
}

void countDownSet(bool state) {
  countDownStarted = state;
}

// Reset the count down state
void countDownRest() {
  countDownSet(false);
  displayTime = MAX_COUNT_DOWN_TIME;
}


