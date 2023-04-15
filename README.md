This is an Arduino sketch that controls two stepper motors using the AccelStepper library and an LCD keypad. The motors are connected to pins A1, A2, A3, A4, and A5, and are enabled using the enableOutputs() function. The LCD keypad is connected to the Arduino and is used to input commands for controlling the motors.
The sketch initializes the motors and LCD keypad in the setup() function, and then reads the input from the keypad in the loop() function. The switch statement in the loop() function processes the input from the keypad and updates the speed and direction of the selected motor accordingly.
The refresh() function is called from the switch statement and updates the LCD keypad display with the selected motor, motor speed, and motor direction. The runSpeed() function is used to move the motors one step at a time, and a delay is used to allow time for the motors to move.
Overall, this sketch provides a simple interface for controlling two stepper motors with an LCD keypad and demonstrates how the AccelStepper library can be used to control the speed and direction of the motors.
#include <AccelStepper.h>
#include <LCD1602KeyPad.h>
#include <LiquidCrystal.h>
// Initialize the LCD keypad object
LCD1602KeyPad lcd;

// Initialize the stepper motor pins
const int motor1_step = A1;
const int motor1_dir = A2;
const int motor1_enable = A3;
const int motor2_step = A4;
const int motor2_dir = A5;

// Initialize the stepper motor objects
AccelStepper motor1(AccelStepper::DRIVER, motor1_step, motor1_dir);
AccelStepper motor2(AccelStepper::DRIVER, motor2_step, motor2_dir);

// Initialize variables for motor speed and direction
int motor1_speed = 0;
int motor2_speed = 0;
bool motor1_cw = true;
bool motor2_cw = true;

int motorspeedratio1 = 15; // zarib motor 1 *********
int motorspeedratio2 = 5; // zarib motor 2 *********

bool selectmotor = true;  //true = motor1 false motor2

void setup() {
  // Initialize the LCD keypad with default pin assignments
  lcd.begin();
  // Set the initial direction of the motors
  motor1.setSpeed(0);
  motor1.setMaxSpeed(100 * motorspeedratio1);
  motor2.setSpeed(0);
  motor2.setMaxSpeed(100 * motorspeedratio2);

  // Enable the stepper motors
  motor1.enableOutputs();
  motor2.enableOutputs();

  pinMode(motor1_enable, OUTPUT);

  refresh();
}

void loop() {
  // Read the keypad input and update the motor speed and direction
  ButtonKey key = lcd.readButtons();
  switch ( key )
  {
    case ButtonKey::NONE:
      break;
    case ButtonKey::RIGHT:
      if (selectmotor) {
        motor1_speed += 10;
        if (motor1_speed > 100)
          motor1_speed = 100;
      }
      else {
        motor2_speed += 10;
        if (motor2_speed > 100)
          motor2_speed = 100;
      }
      refresh();
      break;
    case ButtonKey::LEFT:
      if (selectmotor) {
        motor1_speed -= 10;
        if (motor1_speed < 0)
          motor1_speed = 0;
      }
      else {
        motor2_speed -= 10;
        if (motor2_speed < 0)
          motor2_speed = 0;
      }
      refresh();
      break;
    case ButtonKey::UP:
      if (selectmotor) {
        motor1_cw = !motor1_cw;
      }
      else {
        motor2_cw = !motor2_cw;
      }
      refresh();
      break;
    case ButtonKey::DOWN:
      if (selectmotor) {
        motor1_speed = 0;
      }
      else {
        motor2_speed = 0;
      }
      refresh();
      break;
    case ButtonKey::SELECT:
      selectmotor = !selectmotor;
      refresh();
      break;
  }





  // Move the motors one step
  motor1.runSpeed();
  motor2.runSpeed();



  // Delay to allow time for the motors to move
}
int refresh() {

  if (motor1_speed == 0)
    digitalWrite(motor1_enable, HIGH);
  else
    digitalWrite(motor1_enable, LOW);
  // Set the motor speed and direction
  if (motor1_cw) {
    motor1.setSpeed(motor1_speed * motorspeedratio1);
  } else {
    motor1.setSpeed(-motor1_speed * motorspeedratio1);
  }

  if (motor2_cw) {
    motor2.setSpeed(motor2_speed * motorspeedratio2);
  } else {
    motor2.setSpeed(-motor2_speed * motorspeedratio2);
  }

  // Update the LCD keypad display with the selected motor
  lcd.setCursor(0, 0);
  lcd.print(selectmotor ? ">" : " ");
  lcd.setCursor(0, 1);
  lcd.print(selectmotor ? " " : ">");

  // Update the LCD keypad display with the motor speed
  lcd.setCursor(1, 0);
  lcd.print("SM1=");
  lcd.setCursor(5, 0);
  lcd.print("    " );
  lcd.setCursor(5, 0);
  lcd.print(motor1_speed);

  lcd.setCursor(1, 1);
  lcd.print("SM2=");
  lcd.setCursor(5, 1);
  lcd.print("    ");
  lcd.setCursor(5, 1);
  lcd.print(motor2_speed);

  // Update the LCD keypad display with the motor direction
  lcd.setCursor(13, 0);
  lcd.print("   ");
  lcd.setCursor(11, 0);
  lcd.print(motor1_cw ? "D=CW " : "D=CCW");
  lcd.setCursor(13, 1);
  lcd.print("   ");
  lcd.setCursor(11, 1);
  lcd.print(motor2_cw ? "D=CW " : "D=CCW");
  delay(200);
}


