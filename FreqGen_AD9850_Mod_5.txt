
// WH Code - Mod issue 4 debug lines removed and "Enable" detect function implemented and transmitted back to main V. Studio Pgm.
// WH Code - Mod issue 5 Baud rate increased from 9600 to tbc 14400

// ************************************************************************************************

int wclk = 8;       //Declare pin no 8 as  DDS W_CLK pin
int fqud = 9;       //Declare pin no 9 as  DDS Frq_UD pin
int d = 10;         //Declare pin no 10 as DDS Data pin
int inPin = 11;     //Declare pin no 11 as an input pin
int pinval = 0;     //
long freq;          //Declare freq variable as long (32 bits (4 bytes), from -2,147,483,648 to 2,147,483,647)

void setup() {
  // put your setup code here, to run once:

pinMode(wclk,OUTPUT);
pinMode(fqud,OUTPUT);
pinMode(d,OUTPUT);
pinMode(inPin, INPUT);    // sets the digital pin 11 as input for VFO enbabled detection

// initialize serial ports
  Serial.begin(14400);    // USB serial port 0
}

void loop() {
  // put your main code here, to run repeatedly:

while (Serial.available()==0){
}

freq = Serial.parseInt();       // assign serial data to frequency long variable "freq"

pinval = digitalRead(inPin);    // read the input pin 11
Serial.print (pinval);

// ******************************************* DDS Communication *********************************************
    
 
  digitalWrite(wclk,LOW);     //make W_CLK = 0
  digitalWrite(fqud,LOW);     //make FQ_UD = 0

  for(int i = 0; i <= 31; i++){     //initiate a loop of 32 iterations to send each bit
    if(freq&((unsigned long)1<<i)){ //identify each bit and send it to the data port
      digitalWrite(d,HIGH);
       }
 else{
       digitalWrite(d,LOW);
        }
   digitalWrite(wclk,HIGH);    //give the rising edge to transfer the data
   digitalWrite(wclk,LOW);    // make the W_CLK = 0
 }

   for (int i = 7; i >=0; i--){   //initiate a loop for remaining 8 bits of the total 40 bit word
       digitalWrite(d,LOW);       //these bits have information of phase and power down mode
       digitalWrite(wclk,HIGH);   //give a pulse at W_CLK
       digitalWrite(wclk,LOW);
   }     

  digitalWrite(fqud,HIGH);  //Update the 40 bit frequency word
  digitalWrite(fqud,LOW);  //the frequency will be changed accordingly
  //while (1);
  Serial.end();
  Serial.begin(14400);
}
// ****************************************** DDS Communication End ********************************************
 
