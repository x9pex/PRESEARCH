#include "AccelStepper.h"
#include <Servo.h>

// Define stepper motor connections and motor interface type
//Top Stepper
#define dirPin 2
#define stepPin 3

//Bot Stepper
#define dirPin2 4
#define stepPin2 5

#define motorInterfaceType 1

#define spr 200 //stepsPerRevolution

#define button1Pin 6
#define button2Pin 7
#define button3Pin 8
#define limitSwitchPin 12

// Create a new instance of the AccelStepper class:
AccelStepper stepper = AccelStepper(motorInterfaceType, stepPin, dirPin);
AccelStepper stepper2 = AccelStepper(motorInterfaceType, stepPin2, dirPin2);


// Previous button states
bool prevLeftButtonState = HIGH;
bool prevRightButtonState = HIGH;


//Servo
Servo topservo;
Servo botservo;
const int posDown = 90;
const int posUp = 180;

int sprDistance = 0;

int wipes = 2;

void setup() {
  Serial.begin(9600);
  // Set the maximum speed in steps per second:
  stepper.setMaxSpeed(1000);
  stepper2.setMaxSpeed(1000);

  // Enable internal pullup resistors
  pinMode(button1Pin, INPUT_PULLUP);
  pinMode(button2Pin, INPUT_PULLUP);   
  pinMode(button3Pin, INPUT_PULLUP); 
  pinMode(limitSwitchPin, INPUT_PULLUP);


  topservo.attach(10);
  botservo.attach(9);
  Serial.println("WELCOME TO SQUEEGEE SYSTEM!!");
}


void Home() {
    Serial.println("Homing!!");
    //Serial.println("HOME PIN = ON");
    while (digitalRead(limitSwitchPin) == HIGH) {
      //Serial.println("LIMIT PIN = OFF");
      stepper.setSpeed(500);
      stepper.moveTo(200);

      stepper2.setSpeed(500);
      stepper2.moveTo(200);

      stepper.run();
      stepper2.run();
    }
    Serial.println("Home Process Done!");
    stepper.setCurrentPosition(0.0);

}



void moveRight() {
    Serial.println("Moving Right");
    delay(50);
    while (stepper.currentPosition() != -spr*sprDistance){
      stepper.setSpeed(-500);
      //stepper.moveTo(200);
      stepper.moveTo(-200*14);
      stepper.runSpeedToPosition();

      stepper2.setSpeed(-500);
      //stepper2.moveTo(200);
      stepper2.moveTo(-200*14);
      stepper2.runSpeedToPosition();
      
      //stepper.run();
      //stepper2.run();
    }
}

void moveLeft() {
    Serial.println("Moving Left");
    delay(50);
    while (stepper.currentPosition() != spr*sprDistance){
      stepper.setSpeed(500);
      //stepper.moveTo(200);
      stepper.moveTo(200*14);
      stepper.runSpeedToPosition();

      stepper2.setSpeed(500);
      //stepper2.moveTo(200);
      stepper2.moveTo(500*14);
      stepper2.runSpeedToPosition();
      
      //stepper.run();
      //stepper2.run();
    }
}


void servoDown(){
    delay(50);
    //Serial.println("Squeegee Down");
    topservo.write(posDown);
    botservo.write(posDown);
    delay(50);
}

void servoUp(){
    delay(50);
    //Serial.println("Squeegee Up");
    topservo.write(posUp);
    botservo.write(posUp);
    delay(50);
}




void loop() {

  // Check button presses and releases
  //bool currentLeftButtonState = digitalRead(leftButtonPin);
  //bool currentRightButtonState = digitalRead(rightButtonPin);
  servoUp();

  //FULL WIPE
  if (digitalRead(button1Pin) == LOW) {

    Serial.println("Button 1 Pressed!");
    servoUp();

    Home();
    sprDistance = 14;
    
    for(int i=1;i<=wipes;i++){
      servoDown();

      delay(500);
      moveRight();
      delay(500);
      Home();
    }
    
    Home();
    servoUp();
    
  }


  //1st Half Wipe
  if (digitalRead(button2Pin) == LOW) {
    
    Serial.println("Button 2 Pressed!");
    servoUp();

    Home();
    sprDistance = 7;
    for(int i=1;i<=wipes;i++){
      servoDown();
    
      delay(500);
      moveRight();
      delay(500);
      Home();
    }
    
    Home();

    servoUp();
  }

  //2nd Half Wipe
  if (digitalRead(button3Pin) == LOW) {
    
    Serial.println("Button 3 Pressed!");
    servoUp();

    Home();
    sprDistance = 7;
    servoUp();
    
    delay(500);
    moveRight();
    servoDown();
    delay(500);

    for(int i=1;i<=wipes;i++){
      sprDistance = 14;
      moveRight();
      delay(500);

      sprDistance = -7;
      moveLeft();
      delay(500);
    }
    

    servoUp();
    Home();
  }

   // Update previous button states
  //prevLeftButtonState = currentLeftButtonState;
  //prevRightButtonState = currentRightButtonState;
}





/*
void moveRight() {
 // while (stepper.currentPosition() != -spr*14) //RIGHT
  
    stepper.setSpeed(-500);
    stepper.moveTo(-200*14);
    stepper.runSpeedToPosition();
  
}

void moveLeft() {
 // while (stepper.currentPosition() != spr*14) //LEFT
    stepper.setSpeed(500);
    stepper.moveTo(200*14);
    stepper.runSpeedToPosition();
  
}
*/
