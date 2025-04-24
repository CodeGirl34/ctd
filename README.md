# ctd
#include <Servo.h>
int trig = 3;
int echo = 2;
int IN1 = 5;
int IN2 = 6;
int IN3 = 10;
int IN4 = 11;
//int speed = 255;
float time;
float distance;
float front_D;
float right_D;
float left_D;

Servo myservo;
void setup() {
  Serial.begin(9600);
  pinMode(trig, OUTPUT);
  pinMode(echo, INPUT);
  myservo.attach(9);
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);
  myservo.write(90);
  delay(1000);
}

void loop() {
   front_D = USreading();
   Serial.print("Front Distance: ");
   Serial.println(front_D);
  if (front_D <= 0 || front_D > 400) {
  front_D = 400; 
}

  if (front_D < 30 && front_D > 2) {  
    Serial.println("Obstacle detected! Scanning...");
    stop();

    myservo.write(180);  
    delay(500);
    right_D = USreading();
    Serial.print("Right Distance: ");
    Serial.println(right_D);

    myservo.write(0);  
    delay(500);
    left_D = USreading();
    Serial.print("Left Distance: ");
    Serial.println(left_D);

    myservo.write(90);  
    delay(500);

    if (left_D < 30 && right_D < 30) {
      Serial.println("No path! Backing up...");
      backward(100);
      delay(500);
    }

    if (left_D > right_D) {
      Serial.println("Turning LEFT");
      rotate_left(150);
      delay(500);  
    } else {
      Serial.println("Turning RIGHT");
      rotate_right(150);
      delay(500);
    }
  } else {
    Serial.println("Moving FORWARD");
    forward(150);
  }
}
float USreading() {
  digitalWrite(trig, LOW);
  delayMicroseconds(2);
  digitalWrite(trig, HIGH);
  delayMicroseconds(10);
  digitalWrite(trig, LOW);

  time = pulseIn(echo, HIGH);
  if (time <= 0) {
    return 100.0;
  }
  distance = time * 0.034 / 2;
  return distance;
}



void forward(int speed) {
  analogWrite(IN1, 0);
  analogWrite(IN2, speed);
  analogWrite(IN3, 0);
  analogWrite(IN4, speed);
}

void stop() {
  analogWrite(IN1, 0);
  analogWrite(IN2, 0);
  analogWrite(IN3, 0);
  analogWrite(IN4, 0);
}

void rotate_right(int speed) {
  analogWrite(IN1, 0);
  analogWrite(IN2, speed);
  analogWrite(IN3, speed);
  analogWrite(IN4, 0);
}

void rotate_left(int speed) {
  analogWrite(IN1, speed);
  analogWrite(IN2, 0);
  analogWrite(IN3, 0);
  analogWrite(IN4, speed);
}
void backward(int speed) {
  analogWrite(IN1, speed);
  analogWrite(IN2, 0);
  analogWrite(IN3, speed);
  analogWrite(IN4, 0);
}
