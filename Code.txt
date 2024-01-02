#include <Servo.h>

#define RIGHT_S0_PIN 44 
#define RIGHT_S1_PIN 45
#define RIGHT_S2_PIN 40
#define RIGHT_S3_PIN 43
#define RIGHT_OUT_PIN 13
#define RIGHT_GND 42

#define LEFT_S0_PIN 26 
#define LEFT_S1_PIN 24
#define LEFT_S2_PIN 23
#define LEFT_S3_PIN 20
#define LEFT_OUT_PIN 12
#define LEFT_GND 22

int right_motor_speed = 45;//left side fast code
int left_motor_speed = 200;
int Common_Vcc = 23;
//int Common_Gnd = 29;
//int Led_array_Vcc = 40;
int RRM = 6;
int RFM = 5;
int LFM = 2;
int LRM = 3;
int LLIR = 25;
int LIR = 37;
int MIR = 31;
int RIR = 33;
int RRIR = 27;
int wall_1_switch = 51;
int wall_1_gnd = 50;
int wall_2_switch = 53;
int wall_2_gnd = 52; 
int LIR_state;
int MIR_state;
int RIR_state;
int LLIR_state;
int RRIR_state;
int MRIR;
int MLIR;
int Middle_left_IR = 35;
int Middle_Right_IR = 39;
int flag = 0;
int dela = 530;
int del = 0;
int ans = 50;
int Enable_H_bridge = 47;

Servo right;
Servo left;

class Color{
  public:
  int red = 0, green = 0, blue = 0;
}Left, Right;


void process_red_value()
{
  digitalWrite(LEFT_S2_PIN, LOW);
  digitalWrite(LEFT_S3_PIN, LOW);
  digitalWrite(RIGHT_S2_PIN, LOW);
  digitalWrite(RIGHT_S3_PIN, LOW);
  Left.red = pulseIn(LEFT_OUT_PIN, LOW);
  Right.red = pulseIn(RIGHT_OUT_PIN, LOW);
}
void process_green_value()
{
  digitalWrite(LEFT_S2_PIN, HIGH);
  digitalWrite(LEFT_S3_PIN, HIGH);
  digitalWrite(RIGHT_S2_PIN, HIGH);
  digitalWrite(RIGHT_S3_PIN, HIGH);
  Left.green = pulseIn(LEFT_OUT_PIN, LOW);
  Right.green = pulseIn(RIGHT_OUT_PIN, LOW);
}
void process_blue_value()
{
  digitalWrite(LEFT_S2_PIN, LOW);
  digitalWrite(LEFT_S3_PIN, HIGH);
  digitalWrite(RIGHT_S2_PIN, LOW);
  digitalWrite(RIGHT_S3_PIN, HIGH);
  Left.blue = pulseIn(LEFT_OUT_PIN, LOW);
  Right.blue = pulseIn(RIGHT_OUT_PIN, LOW);
}
void Check_color()
{
  process_red_value();
  process_green_value();
  process_blue_value();
}
bool Pluck_fruit(Color plate)
{
  Check_color();
  if(plate.red < plate.green)
  return 1;
  else
  return 0;
}

void setup() {
  pinMode(Enable_H_bridge, OUTPUT);
  
  pinMode(RFM,OUTPUT);
  pinMode(LFM,OUTPUT);
  pinMode(RRM,OUTPUT);
  pinMode(LRM,OUTPUT);
  Stop();
  delay(1500);
  //right_turn();
  pinMode(wall_1_switch, INPUT_PULLUP);
  pinMode(wall_1_gnd, OUTPUT);
  pinMode(wall_2_switch, INPUT_PULLUP);
  pinMode(wall_2_gnd, OUTPUT);
  pinMode(Common_Vcc, OUTPUT);
  //pinMode(Led_array_Vcc, OUTPUT);
  //pinMode(Common_Gnd, OUTPUT);
  digitalWrite(wall_1_gnd, LOW);
  digitalWrite(wall_2_gnd, LOW);
  digitalWrite(Common_Vcc, HIGH);
  //digitalWrite(Led_array_Vcc, HIGH);
  //digitalWrite(Common_Gnd, LOW);
  pinMode(LIR,INPUT);
  pinMode(MIR,INPUT);
  pinMode(RIR,INPUT);
  pinMode(Middle_left_IR,INPUT);
  pinMode(Middle_Right_IR,INPUT);
  digitalWrite(Enable_H_bridge, HIGH);
  
  right.attach(2);
  left.attach(3);
  pinMode(LEFT_S0_PIN, OUTPUT);
  pinMode(LEFT_S1_PIN, OUTPUT);
  pinMode(LEFT_S2_PIN, OUTPUT);
  pinMode(LEFT_S3_PIN, OUTPUT);
 //Set OUT_PIN as Input
  pinMode(LEFT_OUT_PIN, INPUT);
  pinMode(LEFT_GND, OUTPUT);
  digitalWrite(LEFT_GND, LOW);
  // Set Pulse Width scaling to 20%
  digitalWrite(LEFT_S0_PIN, HIGH);
  digitalWrite(LEFT_S1_PIN, LOW);


  pinMode(RIGHT_S0_PIN, OUTPUT);
  pinMode(RIGHT_S1_PIN, OUTPUT);
  pinMode(RIGHT_S2_PIN, OUTPUT);
  pinMode(RIGHT_S3_PIN, OUTPUT);
 //Set OUT_PIN as Input
  pinMode(RIGHT_OUT_PIN, INPUT);
  pinMode(RIGHT_GND, OUTPUT);
  digitalWrite(RIGHT_GND, LOW);
  // Set Pulse Width scaling to 20%
  digitalWrite(RIGHT_S0_PIN, HIGH);
  digitalWrite(RIGHT_S1_PIN, LOW);
  Serial.begin(115200);
  //mechanism_open();
}

void mechanism_open()
{ 
  for (int pos = 115; pos >= 0; pos -= 1) { 
    right.write(pos);              
    delay(50);
  }

  for (int pos = 70; pos <= 180; pos += 1) { 
    left.write(pos);
    delay(50);
  }
}

void mechanism_close()
{
  for (int pos = 0; pos <= 115; pos += 1) { 
    right.write(pos);
    delay(50);
  }

  for (int pos = 180; pos >= 70; pos -= 1) { 
    left.write(pos);               
    delay(50);
  }
}

void right_turn(){
  Serial.println("in right_turn 90");
  digitalWrite(RFM,LOW);
  digitalWrite(LRM,LOW);
  delay(10);
  analogWrite(RRM,right_motor_speed* 2);  //172
  analogWrite(LFM,250);  //182
  delay(500);
  Update_front();
  while(MIR_state != 1)
  {
    
    /*analogWrite(RRM,172);
    analogWrite(LFM,182);*/
    Update_front();    
  }
  //delay(dela-10);

  Stop();
  delay(1000);
}

void right_turn1(){
  Serial.println("in right_turn");
  digitalWrite(RFM,LOW);
  digitalWrite(LRM,LOW);
  delay(10);
  analogWrite(RRM,right_motor_speed* 2);  //172
  analogWrite(LFM,left_motor_speed);  //182
  delay(1000);
  Update_front();
  while(MIR_state != 1)
  {
    /*analogWrite(RRM,172);
    analogWrite(LFM,182);*/
    Update_front();    
  }
  //delay(dela-10);

  Stop();
  delay(1000);
}

void left_turn(){
  Serial.println("in left_turn");
  digitalWrite(RRM,LOW);
  digitalWrite(LFM,LOW);
  delay(10);
  analogWrite(LRM, 122);
  analogWrite(RFM, 92);
  delay(300);
  Update_front();
  while(MIR_state != 1)
  {
    analogWrite(LRM, 202);
    analogWrite(RFM, 202);
    Update_front();    
  }
  //delay(dela+250);

  Stop();
  delay(1000);
}

void left_turn1(){
  Serial.println("in left_turn");
  digitalWrite(RRM,LOW);
  digitalWrite(LFM,LOW);
  delay(10);
  analogWrite(LRM, 122);
  analogWrite(RFM, 92);
  delay(1000);
  Update_front();
  while(MIR_state != 1)
  {
    analogWrite(LRM, 202);
    analogWrite(RFM, 202);
    Update_front();    
  }
  //delay(dela+250);

  Stop();
  delay(1000);
}

void Stop()
{
  digitalWrite(RFM, LOW);
  digitalWrite(LFM, LOW);
  digitalWrite(RRM, LOW);
  digitalWrite(LRM, LOW);
  delay(10);
}

void Centralise_on_junction()
{
  Stop();
  analogWrite(RFM,right_motor_speed/2);
  analogWrite(LFM, left_motor_speed);
  Update_side();
  while(!Check_side())
    {
      Update_side();
      Update_front();
    while(!Junction())
    {
      Update_side();
      Update_front();
      if(Right_condition()){
      Serial.println("in right_condition centralise");
      digitalWrite(RRM,LOW);
      digitalWrite(LFM,LOW);
      delay(10);
      analogWrite(RFM,110);
      analogWrite(LRM,80);
      delay(10);
      }else{
        Update_side();
        analogWrite(RFM,right_motor_speed/2);
        analogWrite(LFM, left_motor_speed);
        if(Check_side())
        {
          Serial.println("Else break");
          break;
        }
      }
    }
    while(!Junction())
    {
      Update_side();
      if(Left_condition()){
      Serial.print("in left_condition centralise");
      digitalWrite(RFM,LOW);
      digitalWrite(LRM,LOW);
      delay(10);
      analogWrite(RRM,80);
      analogWrite(LFM,110);
      delay(10);

      Update_front();
      }else{
        Update_side();
        analogWrite(RFM,27);
        analogWrite(LFM,20);
        if(Check_side())
        {
          break;
          Serial.println("Else break");
        }
      }
    }
      Serial.print("Centralise_on_junction");
      Serial.print(MLIR);
      Serial.println(MRIR);
      analogWrite(RFM,27);
      analogWrite(LFM,20);
      Update_side();
    }
  Stop();
  delay(1000);
}

void Centralise_on_junction_reverse()
{
  Stop();
  analogWrite(RRM,right_motor_speed/2);
  analogWrite(LRM,left_motor_speed/2);
  delay(500);
  analogWrite(RRM,27);
  analogWrite(LRM,20);
  Update_side();
  while(!Check_side()){
    Update_front();
    while(Right_condition())
    {
      Serial.println("in right_condition hit_wall");
      digitalWrite(RRM,LOW);
      digitalWrite(LFM,LOW);
      delay(10);
      analogWrite(RFM,110);
      analogWrite(LRM,80);
      delay(10);
      
      Update_front();
    }
    while(Left_condition())
    {
      Serial.print("in left_condition hit_wall");
      digitalWrite(RFM,LOW);
      digitalWrite(LRM,LOW);
      delay(10);
      analogWrite(RRM,80);
      analogWrite(LFM,110);
      delay(10);

      Update_front();
    }
      Serial.print("Centralise_on_junction_reverse");
      Serial.print(MLIR);
      Serial.println(MRIR);
      Update_side();
    }
  Stop();
  delay(1000);
}

void hit_wall()
{
  while(digitalRead(wall_1_switch) == 1 && digitalRead(wall_2_switch) == 1)
  {
    Update_front();
    while(Right_condition())
    {
      Serial.println("in right_condition hit_wall");
      digitalWrite(RRM,LOW);
      digitalWrite(LFM,LOW);
      delay(10);
      analogWrite(RFM,60);
      analogWrite(LRM,60);
      delay(10);
      
      Update_front();
    }
    while(Left_condition())
    {
      Serial.print("in left_condition hit_wall");
      digitalWrite(RFM,LOW);
      digitalWrite(LRM,LOW);
      delay(10);
      analogWrite(RRM,60);
      analogWrite(LFM,60);
      delay(10);

      Update_front();
    }
    Serial.println("In straight hit_wall");
    digitalWrite(RRM,LOW);
    digitalWrite(LRM,LOW);
    delay(10);
    analogWrite(RFM, 35);
    analogWrite(LFM, 28);
  }

  if(digitalRead(wall_1_switch) == 0)
    {
      Stop();
      while(digitalRead(wall_2_switch) == 1)
      {
        analogWrite(LFM, 100);  
      }
    }else if(digitalRead(wall_2_switch) == 0)
    {
      while(digitalRead(wall_1_switch) == 1)
      {
        analogWrite(RFM, 100);  
      }
    }
  Stop();
  delay(1000);
}

void Update_front()
{
  MIR_state = !digitalRead(MIR);
  RIR_state = !digitalRead(RIR); 
  RRIR_state = !digitalRead(RRIR);
  LIR_state = !digitalRead(LIR);
  LLIR_state = !digitalRead(LLIR); 
}

void Update_side()
{
  if(MLIR == 1 && MRIR == 0)
  MRIR = digitalRead(Middle_Right_IR);
  else if(MLIR == 0 && MRIR == 1)
  MLIR = digitalRead(Middle_left_IR);
  else{
    MLIR = digitalRead(Middle_left_IR);
    MRIR = digitalRead(Middle_Right_IR);
    }
}

bool Check_side()
{
  if(MLIR+MRIR == 2)
  return 1;
  else 
  return 0;
}

/*void left_opposite_turn()
{
  Serial.println("in left_opposite_turn");
  digitalWrite(RRM,LOW);
  digitalWrite(LFM,LOW);
  delay(10);
  analogWrite(LRM,120);
  analogWrite(RFM,192);
  delay(dela);
  delay(dela + 20);

  Stop();
}*/

void line_follow(){

  Update_front();
  
  Serial.print("Line follow start Flag: ");
  Serial.println(flag);
  if(Left_condition())
  {
    while(Left_condition())
    {
      Serial.print("in left_condition");
      digitalWrite(RFM,LOW);
      digitalWrite(LRM,LOW);
      delay(10);
      analogWrite(RRM,80);
      analogWrite(LFM,110);
      delay(10);

      Update_front();
    }
  }
  else if(Right_condition())
  {
    while(Right_condition())
    {
      Serial.println("in right_condition");
      digitalWrite(RRM,LOW);
      digitalWrite(LFM,LOW);
      delay(10);
      analogWrite(RFM,110);
      analogWrite(LRM,80);
      delay(10);
      
      Update_front();
    }
  }
   else if(Junction())
   {
     ++flag;
     delay(del); 
     if(flag == 4)
    {
    Serial.print("in flag == 4");

    Update_side();
    Centralise_on_junction();
    Stop();
    right_motor_speed -= 10;
    left_motor_speed -= 10;
    right_turn();
    /*analogWrite(RFM, right_motor_speed);
    analogWrite(LFM, left_motor_speed);
    delay(350);
    Stop();*/
    right_turn1();
    //mechanism_open();
    /*for(int i = 0; i<2; i++)
    {
      Centralise_on_junction();
      analogWrite(LRM, 82);
      analogWrite(RRM, 65);
      delay(600);
      Stop();
    }*/
    hit_wall();
    Serial.print("Fruits_Plucked: ");
    Serial.print(Pluck_fruit(Left));
    Serial.println(Pluck_fruit(Right));
    //mechanism_close();
    Centralise_on_junction_reverse();
    right_turn();
    //mechanism_open();
    delay(10);
    flag = 4;
    del = 150;
   }
   else if(flag == 2)
  {
    Serial.println("in flag == 2 : ");
    
    Update_side();
    Centralise_on_junction();
    Stop();
    
    right_turn();
    analogWrite(RFM, right_motor_speed);
    analogWrite(LFM, left_motor_speed);
    delay(800);
    Stop();
    right_turn1();
    hit_wall();
    Serial.print("Fruits_Plucked: ");
    Serial.print(Pluck_fruit(Left));
    Serial.println(Pluck_fruit(Right));
    //mechanism_close();
    Centralise_on_junction_reverse();
    right_turn();
    right_turn();
    //mechanism_open();
    del = 50;
  }
  else if( flag >= 5)
  {
    Serial.print("in flag > 5 : ");
    Serial.println(flag);
    
    Update_side();
    Centralise_on_junction();
    Stop();
    
    right_turn();
    analogWrite(RFM, right_motor_speed);
    analogWrite(LFM, left_motor_speed);
    delay(1100);
    Stop();
    //mechanism_open();
    right_turn1();
    /*for(int i = 0; i<2; i++)
    {
      Centralise_on_junction();
      analogWrite(LRM, 82);
      analogWrite(RRM, 65);
      delay(1000);
      Stop();
    }*/
    hit_wall();
    Serial.print("Fruits_Plucked: ");
    Serial.print(Pluck_fruit(Left));
    Serial.println(Pluck_fruit(Right));
    //mechanism_close();
    Centralise_on_junction_reverse();
    //dela -=20;
    //left_opposite_turn();
    /*right_motor_speed -= 10;
    left_motor_speed -= 10;*/
    analogWrite(RRM,right_motor_speed);
    analogWrite(LRM,left_motor_speed);
    delay(1800);
    Stop();
    delay(10000);
  }
   }
   else
   {
    Serial.println("In straight");
    digitalWrite(RRM,LOW);
    digitalWrite(LRM,LOW);
    delay(10);
    analogWrite(RFM, right_motor_speed);
    analogWrite(LFM, left_motor_speed);
   }
 }

bool Right_IR()
{
  Update_front();
  if(RRIR_state == 1 || RIR_state == 1)
  return 1;
  else 
  return 0;
}

bool Left_IR()
{
  Update_front();
  if(LLIR_state == 1 || LIR_state == 1)
  return 1;
  else 
  return 0;
}

bool Junction()
{
  Update_front();
  Serial.println("In junction");
  return (Left_IR() == 1 && Right_IR() == 1 && MIR_state == 1);
}
bool Right_condition()
{
  Update_front();
  return (MIR_state == 0 &&  RIR_state == 0 && (LIR_state == 1 || LLIR_state == 1));
}

bool Left_condition()
{
  Update_front();
  return (MIR_state == 0 &&  LIR_state == 0 && (RIR_state == 1 || RRIR_state == 1));
}

void loop() 
{
  line_follow();
}