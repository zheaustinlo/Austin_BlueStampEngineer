# Autonomous Cleaner
This project uses an Arduino Uno to control a robot that automatically cleans the floor. The robot is equipped with two IR sensors and one ultrasonic sensor to detect and avoid obstacles while moving around. A motor driver controls two DC motors, allowing the robot to move in different directions. A vacuum is attached to the robot so it cleans the floor as it moves. The robot operates fully autonomously without requiring human control.

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Austin L. | Monta Vista High School | Electrical Engineering | Incoming Junior |

**Replace the BlueStamp logo below with an image of yourself and your completed project. Follow the guide [here](https://tomcam.github.io/least-github-pages/adding-images-github-pages-site.html) if you need help.**

![Headstone Image](logo.svg)
  
# Final Milestone & Modifications

<iframe width="560" height="315" src="https://www.youtube.com/embed/6Tg_-RMH1v0?si=8twbWN7D_aOu2v6G" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

For my final milestone, I completed the design and 3D printing of a vacuum holder and lid, allowing the vacuum to be attached to the robot chassis. After assembling the robot, I troubleshooted both the hardware and software to ensure it works. After completing the base project, I added three major modifications. Modification 1 was adding a transistor to control the vacuum through the Arduino. The transistor is a electronic switch allowing the Arduino to turn the vacuum on and off. This lets the vacuum start automatically whenever the robot begins moving, so I would only need to turn on one switch to power the entire system. Modification 2 was programming a  timer that automatically stops the motors and activates a buzzer when the robot is done cleaning. This alerts the user that the robot has finished its task. Modification 3 was adding a gyroscope sensor to make the robot drive straight, because the two motors rotate at different speeds with the same amount of power, so the robot drifted to one side. The gyroscope continuously measures the robot's heading, and the Arduino compares the current heading to the correct heading. If the robot begins to drift, the program automatically increases the speed of one motor and decreases the speed of the other, which forces the car to return to the correct path. The biggest challenges I faced at BSE were troubleshooting both the hardware and software, especially fixing the transistor circuit and making the gyroscope run smoothly. I solved these problems by finding that a P-type transistor worked for my circuit and by switching the gyroscope code from the Y-axis to the X-axis, which allowed the robot to drive straight. My biggest triumph was seeing the robot successfully avoid obstacles, drive in a straight line, and have all of the components work together as one complete system. I learned about Arduino programming, electronics, motor control, sensors, 3D printing, and debugging in BSE. In the future, I hope to use this robot as a cleaning tool for small areas around my house.


# Second Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/GF-8ctVWIKs?si=26MhPvFGy_OnyPXG" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

For my second milestone, I wired two IR sensors and one ultrasonic sensor to the Arduino and programmed them to detect obstacles around the robot. The ultrasonic sensor measures the distance to objects in front of the robot, while the IR sensors detect obstacles on the left and right sides. I also wrote the code for a random walk algorithm similar. The robot first backs up when it detects an obstacle and then randomly chooses to turn left or right for a random amount of time before moving forward again. This makes the robot travel in different directions and cover more area each time it detects an obstacle. One challenge I faced was that the motors would randomly stop while the robot was moving. After testing the hardware, I found that the motor driver was not getting enough power. I fixed this by adding a separate 9 volt power source straight to the motors, which made the robot move much more smoothly. For my final milestone, I will design and 3D print the part that connects the vacuum to the robot, and make sure the robot works properly by fixing any remaining issues.

# First Milestone
<iframe width="560" height="315" src="https://www.youtube.com/embed/nKPl_gTzEUg?si=VUmnqW-5C8x7qbSd" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

My first milestone was assembling the robot chassis. I attached the mini breadboard, TT motors, TT wheels, the L9110 motor driver, the Arduino Uno board, and three sensors to the robot. I also connected the motors to the motor driver and connected the motor driver's input pins to the Arduino so the Arduino could control the motors and move the car in different directions. In addition, I wrote and tested the code that enabled the robot to move forward, backward, turn left, and turn right autonomously. For my next milestone, I will wire the sensors to the Arduino and program the robot to detect and avoid obstacles. I will also write code that allows the robot to walk around randomly, so it can clean up a large area.

# Schematics 
<h4>Vacuum Holder</h4>
<img src="vacuum_holder.jpg" width="500"><br>
<b>Vacuum Holder STL:</b> <a href="vacuum%20holder(updated).stl">Download</a>
<h4>Vacuum Lid</h4>
<img src="vacuum_lid.jpg" width="500"><br>
<b>Vacuum Lid STL:</b> <a href="vacuum%20lid.stl">Download</a>
<h4>Robot</h4>
<img src="robotpart1.jpg" width="500"><br>
<img src="robotpart2.jpg" width="500">

# Code
```c++
#include <Wire.h>
#include <Adafruit_MPU6050.h>
#include <Adafruit_Sensor.h>

// motor pins
const int A_1B = 5;
const int A_1A = 6;
const int B_1B = 9;
const int B_1A = 10;

// ultrasonic pins
const int trigPin = 3;
const int echoPin = 4;
const int SAMPLESIZE = 5; // values to take the median of
const int TURNSPEED = 220; // has to turn fast
const long INTERVAL = 30000; // run time before stopping
const int DISTANCETOSTOP = 20; // stop distance for the ultrasonic sensor
const int MAXMOTORSPEED = 220; // forward speed

// ir pins
const int rightIR = 7;
const int leftIR = 8;

// vacuum pins
const int speakerPin = 11;
const int vacuumPin = 13;

bool beeped = false; // only beeps once(big buzzer)

Adafruit_MPU6050 mpu; // create the gyro obj

float heading = 0; // current angle
float gyroErrorX = 0; // gyro offset

unsigned long previousTime; // previous gyro update
unsigned long startTime; // the time since the program started running 

const float KP = 10; // steering correction strength


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

  pinMode(speakerPin, OUTPUT);
  pinMode(vacuumPin, OUTPUT);

  digitalWrite(vacuumPin, LOW); // vacuum off

  randomSeed(analogRead(A0)); // random starting value

  if (!mpu.begin()) {
    Serial.println("MPU6050 not found");
    while (1) {
      delay(10);
    }
  }

  mpu.setGyroRange(MPU6050_RANGE_500_DEG);
  mpu.setFilterBandwidth(MPU6050_BAND_21_HZ);

  calibrateGyro(); // find gyro offset

  heading = 0; // reset angle
  previousTime = micros();
  startTime = millis(); // save start time

  digitalWrite(vacuumPin, HIGH); // turns on the vacuum
}
int left = 0;
int right = 0;
float distance = 0;
int randomTurnTime;
bool obstacleAhead;
bool pathClearAhead;
void loop() {
  if (beeped) { // stops after beeped so progrom only runs once
    stopMove();
    return;
  }

  updateAngle(); // update robot angle
  left = digitalRead(leftIR);
  right = digitalRead(rightIR);
  distance = readMedianDist();
  
  randomTurnTime = random(700, 1800); // random turn direction

  obstacleAhead = (distance > 0 && distance < DISTANCETOSTOP);   // Anything from 0 - 20 is detected as an obstacle
  pathClearAhead = (distance <= 0 || distance >= DISTANCETOSTOP); 

  if (obstacleAhead) { // obstacle straight ahead
    moveBackward(MAXMOTORSPEED);
    delay(500);

    if (random(0, 2) == 0) {
      backLeft(TURNSPEED);
    } else {
      backRight(TURNSPEED);
    }
    delay(randomTurnTime);

    // reset heading tracking after every turn so the next
    heading = 0;
    previousTime = micros();
  }
  else if (!left && right) { // obstacle on the left
    backRight(TURNSPEED); // turn away, to the right
    delay(randomTurnTime);

    heading = 0;
    previousTime = micros();
  }

  else if (left && !right) { // obstacle on the right
    backLeft(TURNSPEED); // turn away, to the left
    delay(randomTurnTime);

    heading = 0;
    previousTime = micros();
  }

  else if (!left && !right) { // obstacle on both sides
    moveBackward(MAXMOTORSPEED);
    delay(500);
    if (random(0, 2) == 0) {
      backLeft(TURNSPEED);
    } else {
      backRight(TURNSPEED);
    }
    delay(randomTurnTime);

    heading = 0;
    previousTime = micros();
  }
  else { // clear path
    if (pathClearAhead) {
      moveForward(MAXMOTORSPEED);
    }
  }
  if (!beeped && millis() - startTime >= INTERVAL) { // checks if the robot has been running for a certain time
    stopMove();
    digitalWrite(vacuumPin, LOW);
    tone(speakerPin, 440, 2000);
    beeped = true;
  }
}
float otherDistance = 0;
// Sends a short ultrasonic pulse and times how long the echo takes to
float readSensorData() {
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);

  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);

  digitalWrite(trigPin, LOW);

  otherDistance = pulseIn(echoPin, HIGH, 15000) / 58.0;

  if (otherDistance == 0) { // return -1 if no reading
    otherDistance = -1;
  }
  return otherDistance;
}
float median[SAMPLESIZE];
float key;
// Ultrasonic sensor takes 5 readings and using the median to filter out the bad readiings
float readMedianDist() {
  for (int i = 0; i < SAMPLESIZE; i++) {
    median[i] = readSensorData();
    delay(5);
  }

  for (int i = 1; i < SAMPLESIZE; i++) {
    key = median[i];
    int j = i - 1;

    while (j >= 0 && median[j] > key) {
      median[j + 1] = median[j];
      j--;
    }
    median[j + 1] = key; // sort them from small to big
  }
  return median[SAMPLESIZE / 2];
}
sensors_event_t a, g, temp; 
unsigned long currentTime;
float dt, gyroX;
//The gyro reports how fast the car is spinning.
void updateAngle() {
  mpu.getEvent(&a, &g, &temp);

  currentTime = micros();  
  // dt = time elapsed sin  ce the last time this ran.
   dt = (currentTime - previousTime) / 1000000.0;
  previousTime = currentTime;
   gyroX = g.gyro.x * 180.0 / PI;// converting to degrees
  heading += (gyroX - gyroErrorX) * dt;// updates the current angle
}

void calibrateGyro() {
  gyroErrorX = 0; // resets gyro error

  for (int i = 0; i < 100; i++) { // takes 100 readings
    mpu.getEvent(&a, &g, &temp);

    gyroErrorX += g.gyro.x * 180.0 / PI;
    delay(5);
  }
  gyroErrorX /= 100.0; // find average gyro offset

}

// drive forward
float correction;
int rightMotor, leftMotor;

void moveForward(int speed) {
  // Serial.println("moving forward");
   correction = heading * KP; // calculate steering correction
  correction = constrain(correction, -100, 100); // limit correction
   rightMotor = constrain(speed - correction, 0, 255);  // calculates motor speed
   leftMotor = constrain(speed + correction, 0, 255); // added lefttrim so left matches right wheel
  if(correction < 0){
    Serial.println("going left!");
    Serial.println(correction);
  }
  else if(correction > 0){
    Serial.println("going write pls work!!!!!!!!!!");
  }
  analogWrite(A_1B, 0);
  analogWrite(A_1A, rightMotor);

  analogWrite(B_1B, leftMotor);
  analogWrite(B_1A, 0);

}

// drive backward
void moveBackward(int speed) {
  // Serial.println("moving backward");
  analogWrite(A_1B, speed);
  analogWrite(A_1A, 0);

  analogWrite(B_1B, 0);
  analogWrite(B_1A, speed);
}

// turn left while backing up
void backLeft(int speed) {
  // Serial.println("backing up to the left");
  analogWrite(A_1B, 0);
  analogWrite(A_1A, speed);

  analogWrite(B_1B, 0);
  analogWrite(B_1A, constrain(speed, 0, 255)); // added the trim so left matches right motor
}

// turn right while backing up
void backRight(int speed) {
  // Serial.println("backing up to the right");
  analogWrite(A_1B, speed);
  analogWrite(A_1A, 0);

  analogWrite(B_1B, constrain(speed, 0, 255));
  analogWrite(B_1A, 0);
}

// stops the car
void stopMove() {
  // Serial.println("stopping");
  analogWrite(A_1B, 0);
  analogWrite(A_1A, 0);

  analogWrite(B_1B, 0);
  analogWrite(B_1A, 0);
}
```

# Bill of Materials

| Part | Note | Price | Link |
|------|------|:------|------|
| 3-in-1 Ultimate Kit for Arduino Uno R3 (Arduino Uno, L298N Motor Driver, Ultrasonic Sensor, 2× IR Sensors) | Main electronics and obstacle detection | $69.99 | [Amazon](https://www.amazon.com/dp/B0CGJ235XN) |
| ODISTAR Desktop Vacuum Cleaner | Provides suction for floor cleaning | $12.98 | [Amazon](https://www.amazon.com/dp/B07Q128V6W) |
| Big Buzzer | Alerts the user when cleaning is complete | $1.49 | [Amazon](https://www.amazon.com/dp/B0DVT1Q9NK) |

# Other Resources/Examples
One of the best parts about Github is that you can view how other people set up their own work. Here are some past BSE portfolios that are awesome examples. You can view how they set up their portfolio, and you can view their index.md files to understand how they implemented different portfolio components.
- [Example 1](https://trashytuber.github.io/YimingJiaBlueStamp/)
- [Example 2](https://sviatil0.github.io/Sviatoslav_BSE/)
- [Example 3](https://arneshkumar.github.io/arneshbluestamp/)

To watch the BSE tutorial on how to create a portfolio, click here.
