**Self driving robot car**

The future is now, through a custom-built self-driving robot designed to navigate around areas without any human control. By combining realtime sensor processing with advanced movement algorithms, the robot successfully can drive comfortably through unpredictable environment. Through overcoming dead sensors, LED's not working, and code being a hassle, I overcame everything and successfully built this amaxing robot.

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Viraj S. | Kennedy Middle School| Mechanical Engineering | Incoming 8th Grader



![Headstone Image](<1000069516.jpg>)
  
# Final Milestone



<iframe width="560" height="315" src="https://www.youtube.com/embed/jRP8sBza8Ko?si=wWM_eP-PgpRf6bJ1" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

For your final milestone, explain the outcome of your project. Key details to include are:
- Assembled all the modifications (LED, Piezo Buzzer)
- IR Distance Sensors never worked because of coding issues, I debugged with my instructors and found the problem 
- Learned how to solder, debug/problem solve, and code in Arduino
- What you hope to learn in the future after everything you've learned at BSE I hope to figure out how to 



# Second Milestone



<iframe width="560" height="315" src="https://www.youtube.com/embed/xWPwQFeYW9k?si=khreZ7yKGH77LwWj" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

For your second milestone, explain what you've worked on since your previous milestone. You can highlight:
- Technical details of what you've accomplished and how they contribute to the final goal
- What has been surprising about the project so far
- Previous challenges you faced that you overcame
- What needs to be completed before your final milestone 

# First Milestone



<iframe width="560" height="315" src="https://www.youtube.com/embed/s2_vywoENfQ?si=ETExBdIPh4D-tiVw" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
For your first milestone, describe what your project is and how you plan to build it. You can include:
- An explanation about the different components of your project and how they will all integrate together
- Technical progress you've made so far
- Challenges you're facing and solving in your future milestones
- What your plan is to complete your project



# Schematics 
https://docs.sunfounder.com/projects/3in1-kit-v2/en/latest/_images/car_7_8.png

# Code

```c++
// -------------------- PIN SETUP --------------------
const int A_1B = 5;   // Motor A backward
const int A_1A = 6;   // Motor A forward
const int B_1B = 9;   // Motor B forward
const int B_1A = 10;  // Motor B backward
const int echoPin = 4; 
const int trigPin = 3; 

const int leftIR  = 7;
const int rightIR = 8;

const int red = 12;   
const int green = 11; 
const int blue = 13;  

const int buz = 2;  
int errorCounter = 0; 


const int IR_THRESHOLD = 500;  


// -------------------- ULTRASONIC --------------------
float readSensorData() {
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  long duration = pulseIn(echoPin, HIGH, 11500); 
  if (duration == 0) return -1;  

  float distance = duration / 58.0;
  return distance;
}


// -------------------- MOTOR & LED CONTROL --------------------
void moveForward(int speed) {
  setColor(0, 128, 0);

  analogWrite(A_1A, speed);
  analogWrite(A_1B, 0);
  analogWrite(B_1B, speed);
  analogWrite(B_1A, 0);
}

void moveBackward(int speed) {
  setColor(255, 165, 0);

  analogWrite(A_1B, speed);
  analogWrite(B_1B, 0);
  analogWrite(B_1A, speed);
}

void setColor(int redValue, int greenValue,  int blueValue) {
  analogWrite(red, redValue);
  analogWrite(green,  greenValue);
  analogWrite(blue, blueValue);
}

void backLeft(int speed) {  
  setColor(255,255,0);
  analogWrite(A_1B, speed);
  analogWrite(B_1B, 0);
  analogWrite(B_1A, 0);
}

void backRight(int speed) {  
  setColor(255,255,0);
  analogWrite(A_1A, 0);
  analogWrite(A_1B, 0);
  analogWrite(B_1B, 0);
  analogWrite(B_1A, speed);
}

void stopMove() {
  setColor(255,0,0);
  analogWrite(A_1A, 0);
  analogWrite(A_1B, 0);
  analogWrite(B_1B, 0);
  analogWrite(B_1A, 0);

  tone(buz, 1000, 400);  
}


// -------------------- SETUP --------------------
void setup() {
  Serial.begin(9600);

  pinMode(A_1A, OUTPUT);
  pinMode(A_1B, OUTPUT);
  pinMode(B_1A, OUTPUT);
  pinMode(B_1B, OUTPUT);

  pinMode(echoPin, INPUT);
  pinMode(trigPin, OUTPUT);

  pinMode(leftIR, INPUT);
  pinMode(rightIR, INPUT);

  pinMode(red, OUTPUT);
  pinMode(green, OUTPUT);
  pinMode(blue, OUTPUT);

  pinMode(buz, OUTPUT);
  
}


// -------------------- MAIN LOOP --------------------
void loop() {
  int rightRaw  = digitalRead(leftIR);
  int leftRaw = digitalRead(rightIR);
  Serial.print("Right sensor: ");
  Serial.print(rightRaw);
  Serial.print(" Left sensor: ");
  Serial.println(leftRaw);
  delay(1000);
  
  bool leftCovered  = leftRaw;
  bool rightCovered = rightRaw;

  float distance = readSensorData();
  Serial.print("Distance: ");
  Serial.println(distance);

  Serial.print("IR left: ");
  Serial.print(leftRaw);
  Serial.print(" | IR right: ");
  Serial.println(rightRaw);

  delay(1000); 


  // -------------------- IR OBSTACLE LOGIC (ANALOG) --------------------
  if (leftRaw == 0 && rightRaw == 1) {
    stopMove();
    delay(300);
    backRight(150);
    delay(500);
    return;
  }

  if (rightRaw == 0 && leftRaw== 1) {
    stopMove();
    delay(300);
    backLeft(150);
    delay(500);
    return;
  }

  if (leftRaw ==0 && rightRaw==0) {
    stopMove();
    delay(300);
    moveBackward(150);
    delay(600);
    return;
  }


  // -------------------- ULTRASONIC GLITCH FILTER --------------------
  if (distance == -1) {
    errorCounter++;
    if (errorCounter > 10) { 
      stopMove();
    }
    return; 
  }
  
  errorCounter = 0; 


  // -------------------- ULTRASONIC LOGIC --------------------
  if (distance < 10) {
    stopMove();
    delay(400); 
    moveBackward(150);
    delay(600);
    backLeft(150);
    delay(400);
    return;
  }

  if (distance < 25) {
    moveForward(120);
    return;
  }

  moveForward(200);
  
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

