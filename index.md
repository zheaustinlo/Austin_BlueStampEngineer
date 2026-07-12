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

**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

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
void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  Serial.println("Hello World!");
}

void loop() {
  // put your main code here, to run repeatedly:

}
```

# Bill of Materials
Here's where you'll list the parts in your project. To add more rows, just copy and paste the example rows below.
Don't forget to place the link of where to buy each component inside the quotation marks in the corresponding row after href =. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize this to your project needs. 

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |

# Other Resources/Examples
One of the best parts about Github is that you can view how other people set up their own work. Here are some past BSE portfolios that are awesome examples. You can view how they set up their portfolio, and you can view their index.md files to understand how they implemented different portfolio components.
- [Example 1](https://trashytuber.github.io/YimingJiaBlueStamp/)
- [Example 2](https://sviatil0.github.io/Sviatoslav_BSE/)
- [Example 3](https://arneshkumar.github.io/arneshbluestamp/)

To watch the BSE tutorial on how to create a portfolio, click here.
