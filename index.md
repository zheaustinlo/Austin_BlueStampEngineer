# Floor Cleaning Robot
This project uses an Arduino Uno to control a robot that automatically cleans the floor. The robot is equipped with two IR sensors and one ultrasonic sensor to detect and avoid obstacles while moving around. A motor driver controls four DC motors, allowing the robot to move in different directions. A vacuum is attached to the robot so it cleans the floor as it moves. The robot operates fully autonomously without requiring human control.





| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Austin L. | Monta Vista High School | Electrical Engineering | Incoming Junior |

**Replace the BlueStamp logo below with an image of yourself and your completed project. Follow the guide [here](https://tomcam.github.io/least-github-pages/adding-images-github-pages-site.html) if you need help.**

![Headstone Image](logo.svg)
  
# Final Milestone

**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/F7M7imOVGug" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your final milestone, explain the outcome of your project. Key details to include are:
- What you've accomplished since your previous milestone
- What your biggest challenges and triumphs were at BSE
- A summary of key topics you learned about
- What you hope to learn in the future after everything you've learned at BSE



# Second Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/GF-8ctVWIKs?si=26MhPvFGy_OnyPXG" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

For my second milestone, I wired two IR sensors and one ultrasonic sensor to the Arduino and programmed them to detect obstacles around the robot. The ultrasonic sensor measures the distance to objects in front of the robot, while the IR sensors detect obstacles on the left and right sides. I also wrote the code for a random walk algorithm similar. The robot first backs up when it detects an obstacle and then randomly chooses to turn left or right for a random amount of time before moving forward again. This makes the robot travel in different directions and cover more area each time it detects an obstacle. One challenge I faced was that the motors would randomly stop while the robot was moving. After testing the hardware, I found that the motor driver was not getting enough power. I fixed this by adding a separate 9 volt power source straight to the motors, which made the robot move much more smoothly. For my final milestone, I will design and 3D print the part that connects the vacuum to the robot, and make sure the robot works properly by fixing any remaining issues.

# First Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/nKPl_gTzEUg?si=VUmnqW-5C8x7qbSd" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

My first milestone was assembling the robot chassis. I attached the mini breadboard, TT motors, TT wheels, the L9110 motor driver, the Arduino Uno board, and three sensors to the robot. I also connected the motors to the motor driver and connected the motor driver's input pins to the Arduino so the Arduino could control the motors and move the car in different directions. In addition, I wrote and tested the code that enabled the robot to move forward, backward, turn left, and turn right autonomously. For my next milestone, I will wire the sensors to the Arduino and program the robot to detect and avoid obstacles. I will also write code that allows the robot to walk around randomly, so it can clean up a large area.

# Schematics 
Here's where you'll put images of your schematics. [Tinkercad](https://www.tinkercad.com/blog/official-guide-to-tinkercad-circuits) and [Fritzing](https://fritzing.org/learning/) are both great resoruces to create professional schematic diagrams, though BSE recommends Tinkercad becuase it can be done easily and for free in the browser. 

# Code
Here's where you'll put your code. The syntax below places it into a block of code. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize it to your project needs. 

```c++
#include <EEPROM.h>
const int A_1B = 5;
const int A_1A = 6;
const int B_1B = 9;
const int B_1A = 10;

const int trigPin = 3;
const int echoPin = 4;
const int SAMPLESIZE = 5;
const int TURNSPEED = 110;

const int rightIR = 7;
const int leftIR = 8;

const long INTERVAL = 180000;
const int DISTANCETOSTOP = 20;
const int MAXMOTORSPEED = 140;

float leftOffset = 1.0;
float rightOffset = 1.0;

void setup() {
  Serial.begin(9600);

  pinMode(A_1B, OUTPUT);
  pinMode(A_1A, OUTPUT);
  pinMode(B_1B, OUTPUT);
  pinMode(B_1A, OUTPUT);

  pinMode(echoPin, INPUT);
  pinMode(trigPin, OUTPUT);

  pinMode(leftIR, INPUT);
  pinMode(rightIR, INPUT);

  EEPROM.write(0, 95);
  EEPROM.write(1, 98);
  leftOffset = EEPROM.read(0) * 0.01;
  rightOffset = EEPROM.read(1) * 0.01;

  randomSeed(analogRead(A0));
}

void loop() {
  // 1. Refresh all sensor data instantly at the start of the loop
  int left = digitalRead(leftIR);
  int right = digitalRead(rightIR);
  float distance = readMedianDist();

  // 2. Generate a random turn duration (mimicking a 0 to 2*PI angle)
  // Adjust 1000 to match how many milliseconds your robot takes to spin 360 degrees
  int randomTurnTime = random(700,1800);

  // 3. PRIORITY 1: Obstacle Dead Ahead (Ultrasonic)
  if (distance < DISTANCETOSTOP && distance > 2) {
    Serial.println("gonig backwards");
    moveBackward(MAXMOTORSPEED);
    delay(200);  // Quick reverse to clear the bumper area

    if (random(0, 2) == 0) {
      backLeft(TURNSPEED);
    } else {
      backRight(TURNSPEED);
    }
    delay(randomTurnTime);
  }
  // 4. PRIORITY 2: Object on Left Side only
  else if (!left && right) {
    backLeft(TURNSPEED);
    delay(randomTurnTime);
  }
  // 5. PRIORITY 3: Object on Right Side only
  else if (left && !right) {
    backRight(TURNSPEED);
    delay(randomTurnTime);
  }
  // 6. PRIORITY 4: Both IR sensors trapped
  else if (!left && !right) {
    moveBackward(MAXMOTORSPEED);
    delay(200);
    if (random(0, 2) == 0) {
      backLeft(TURNSPEED);
    } else {
      backRight(TURNSPEED);
    }
    delay(randomTurnTime);
  }
  // 7. PATH CLEAR: Safe to drive forward
  else {
    if (distance > DISTANCETOSTOP) {
      moveForward(MAXMOTORSPEED);
    }
  }
}
float readSensorData() {
  float distance;
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  distance = pulseIn(echoPin, HIGH, 30000) / 58.00;
  if (distance == 0) {
    distance = -1;
  }
  return distance;
}

float readMedianDist() {
  float median[SAMPLESIZE];
  for (int i = 0; i < SAMPLESIZE; i++) {
    median[i] = readSensorData();
    delay(15);
  }
  for (int i = 1; i < SAMPLESIZE; i++) {
    float key = median[i];
    int j = i - 1;

    while (j >= 0 && median[j] > key) {
      median[j + 1] = median[j];
      j--;
    }
    median[j + 1] = key;
  }
  return median[SAMPLESIZE / 2];
}

void moveForward(int speed) {
  analogWrite(A_1B, 0);
  analogWrite(A_1A, int(speed * rightOffset));
  analogWrite(B_1B, int(speed * leftOffset));
  analogWrite(B_1A, 0);
}

void moveBackward(int speed) {
  analogWrite(A_1B, speed);
  analogWrite(A_1A, 0);
  analogWrite(B_1B, 0);
  analogWrite(B_1A, speed);
}

void backLeft(int speed) {
  analogWrite(A_1B, speed);
  analogWrite(A_1A, 0);
  analogWrite(B_1B, 0);
  analogWrite(B_1A, 0);
}

void backRight(int speed) {
  analogWrite(A_1B, 0);
  analogWrite(A_1A, 0);
  analogWrite(B_1B, 0);
  analogWrite(B_1A, speed);
}

void stopMove() {
  analogWrite(A_1B, 0);
  analogWrite(A_1A, 0);
  analogWrite(B_1B, 0);
  analogWrite(B_1A, 0);
}
```

# Bill of Materials
Here's where you'll list the parts in your project. To add more rows, just copy and paste the example rows below.
Don't forget to place the link of where to buy each component inside the quotation marks in the corresponding row after href =. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize this to your project needs. 

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
| 3-in-1 Ultimate Kit for Arduino Uno R3 | self driving car | $59.99 | (https://www.amazon.com/dp/B0CGJ235XN?lv=shuf&channelId=500&plpRedirect=mhFallback) |
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |

# Other Resources/Examples
One of the best parts about Github is that you can view how other people set up their own work. Here are some past BSE portfolios that are awesome examples. You can view how they set up their portfolio, and you can view their index.md files to understand how they implemented different portfolio components.
- [Example 1](https://trashytuber.github.io/YimingJiaBlueStamp/)
- [Example 2](https://sviatil0.github.io/Sviatoslav_BSE/)
- [Example 3](https://arneshkumar.github.io/arneshbluestamp/)

To watch the BSE tutorial on how to create a portfolio, click here.
