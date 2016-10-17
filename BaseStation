/* ============================================
         SCOTT & ANDREW PBL 2016
         MULTI TEMPERATURE SYSTEM
        
================================================
================================================

                VARIABLES & DEFINES

================================================*/


#include "DHT.h"

#include <SoftwareSerial.h>
SoftwareSerial SIM900(7, 8);


#define DHTPIN 50     // what pin we're connected to
#define DHTTYPE DHT22   // DHT 22  (AM2302)

#include <LiquidCrystal.h>
#include <VirtualWire.h>

// RX PIN = 46
const int RxPin = 46;

//LiquidCrystal lcd(8, 13, 9, 4, 5, 6, 7); //lcd pin setup

// GSM LCD PINS
// LCD PIN TO MEGA PIN
//13>13, 9>9, 8>6, 7>5, 6>4, 5>3, 4>2
//LiquidCrystal lcd(6, 13, 9, 2, 3, 4, 5); //lcd pin setup
LiquidCrystal lcd(38, 43, 39, 34, 35, 36, 37); //lcd pin setup

const int buttonPin = 19;     // the number of the pushbutton pin
const int buttonPin2 = 18;     // the number of the pushbutton pin
//const int ledPin =  53;      // the number of the LED pin


volatile int buttonState = 0;         // variable for reading the pushbutton status


// Sensors 

 int ref_old ;
 int dev1_old ;
 int dev2_old ;

int button_num = 1;

int temp ;
int hum ;
int t ;

int RX_temp;
int RX_hum;
int RX_dev_u;
int RX_dev_t;

int dev1_temp = 1;
int dev2_temp = 1;

int dev1_hum;
int dev2_hum;
char stringreceived [6];
  
int dev1_count = 1;
int dev2_count = 1;


unsigned int tmr;
int maxHum = 60;
int maxTemp = 40;

DHT dht(DHTPIN, DHTTYPE);

  const int buzzerPin = 12;
   const int songLength = 18;
   char notes[] = "cdfda ag cdfdg gf "; // a space represents a rest
   int beats[] = {1,1,1,1,1,1,4,4,2,1,1,1,1,1,1,4,4,2};
   int tempo = 113;
   
   boolean dev1_warns = false ;
   boolean dev2_warns = false ;
   
   
 String sms_msg;    
   


/*================================================

                VOID SETUP

================================================*/
void setup(){
  
  lcd.begin(16, 2);
  dht.begin();
  
  SIM900.begin(19200);
  SIM900power();  
  //delay(10000);  // give time to log on to network. 
  
   Serial.begin(9600);	// Debugging only
   Serial.println("setup"); //Prints "Setup" to the serial monitor
    vw_set_rx_pin(RxPin);       //Sets pin D12 as the RX Pin
    vw_set_ptt_inverted(true); // Required for DR3100
    vw_setup(4000);	     // Bits per sec
    vw_rx_start();       // Start the receiver PLL running
    
    
  lcd.clear();
  lcd.setCursor(0,0); 
  lcd.print("Outside");
  lcd.setCursor(0,1); 
  lcd.print("Temp: ");
  lcd.print(t);
  lcd.print((char)223);
  lcd.print("C");

  attachInterrupt(4, pin_ISR, FALLING); // pin 19
  attachInterrupt(5, pin_ISR2, FALLING); // pin 18
  
 pinMode(buzzerPin, OUTPUT);
    
}


/*================================================

                VOID LOOP

================================================*/


void loop()
{
  // =======================================================
  // ===================TEMPERATURE SENSOR==================
  // =======================================================
  
    // Wait a few seconds between measurements.
 // delay(1000);
  // Reading temperature or humidity takes about 250 milliseconds!
  // Sensor readings may also be up to 2 seconds 'old' (its a very slow sensor)
 //  h = dht.readHumidity();
  // Read temperature as Celsius
   t = dht.readTemperature();
  
  // Check if any reads failed and exit early (to try again).
  if (isnan(t)) {
    lcd.setCursor(0,0);
    lcd.print("Error Read");
    delay(3000);
    return;
  }
 
  
  // =====================================================
  // ===================== RECEIVER ======================
  // =====================================================
  
   uint8_t buf[VW_MAX_MESSAGE_LEN];
   uint8_t buflen = VW_MAX_MESSAGE_LEN;
   
    
     //sscanf(StringReceived, "%d,%d,%d,%d,%d,%d",&Sensor1Data, &Sensor2Data,&SensorData3,&SensorData4); // Converts a string to an array

if (vw_get_message(buf, &buflen)) 
    {
 int i;
 Serial.print("Got: ");
        // Message with a good checksum received, dump it. 
        for (i = 0; i < buflen; i++)
 {            
    Serial.print(buf[i], HEX);
	    Serial.print(" ");
          // Fill Sensor1CharMsg Char array with corresponding 
          // chars from buffer.   
          stringreceived[i] = char(buf[i]);
         
          
 }
  Serial.println("");
// sscanf(StringReceived, "%d,%d,%d,%d,%d,%d",&Sensor1Data, &Sensor2Data,&SensorData3,&SensorData4); // Converts a string to an array

 sscanf(stringreceived, "%d,%d,%d,%d",&RX_dev_t,&RX_dev_u,&RX_temp,&RX_hum); // Converts a string to an array

    }
    
    
if (RX_dev_u == 1)
{
  dev1_temp = RX_temp ;
  dev1_hum  = RX_hum  ;
  //dev1_count = 0;
}

if (RX_dev_u == 2)
{
  dev2_temp = RX_temp ;
  dev2_hum  = RX_hum  ;
 //  dev2_count = 0;
}


memset( stringreceived, 0, sizeof( stringreceived));// This line is for reset the StringReceived

if (ref_old != t || dev1_old != dev1_temp || dev2_old != dev2_temp)
{ LCD_SCREEN (); }

  ref_old = t ;
  dev1_old = dev1_temp ;
  dev2_old = dev2_temp ;





// LOOP COUNTER FOR AUTO SCROLL + WARNINGS
              
 
 tmr = tmr + 1 ;
 
 if (tmr >= 30000 ){
   
   tmr= 0;
   
   // DEVICE 1 COUNT
   if (dev1_temp >= 19 )
   {
     dev1_count ++ ;
     if (dev1_count == 2) 
     {
      
      lcd.clear();
      lcd.setCursor(0,0); 
      lcd.print("Device 1 WARNING");
      lcd.setCursor(0,1); 
      lcd.print("Temp: ");
      lcd.print(dev1_temp);
      lcd.print((char)223);
      lcd.print("C");
      SOUND1 ();
      delay(5000);
     }
     
          if (dev1_count == 4) 
     {

      lcd.clear();
      lcd.setCursor(0,0); 
      lcd.print("Device 1 WARNING");
      lcd.setCursor(0,1); 
      lcd.print("Temp: ");
      lcd.print(dev1_temp);
      lcd.print((char)223);
      lcd.print("C");
      SOUND2 ();
      delay(5000);
     }
     
        if (dev1_count >= 6) 
     {
      
      dev1_warns = true;
      lcd.clear();
      lcd.setCursor(0,0); 
      lcd.print("Device 1 WARNING");
      lcd.setCursor(0,1); 
      lcd.print("Temp: ");
      lcd.print(dev1_temp);
      lcd.print((char)223);
      lcd.print("C");
      SOUND3 ();
      delay(5000);
     }
     
      if (dev1_count == 8) 
     {
           lcd.clear();
      lcd.setCursor(0,0); 
      lcd.print("Sending SMS...");
      lcd.setCursor(0,1); 
      lcd.print("Temp: ");
      lcd.print(dev1_temp);
      lcd.print((char)223);
      lcd.print("C");
      
       sms_msg = "Warning! Device 1 experiencing high risk temperatures. Please text xx to confirm" ;

     }
     
     
     
       
   }
   
   else { 
     dev1_count -- ;
     if (dev1_count == 0)
     {
       dev1_count = 1;
     }
      LCD_AUTO ();
   }
   
   
   
   // ===========================
   // DEVICE 2 COUNT 
   //============================
         if (dev2_temp >= 19 )
   {
     dev2_count ++ ;
     if (dev2_count == 2) 
     {
      
      lcd.clear();
      lcd.setCursor(0,0); 
      lcd.print("Device 2 WARNING");
      lcd.setCursor(0,1); 
      lcd.print("Temp: ");
      lcd.print(dev2_temp);
      lcd.print((char)223);
      lcd.print("C");
      SOUND1 ();
      delay(5000);
     }
     
          if (dev2_count == 4) 
     {

      lcd.clear();
      lcd.setCursor(0,0); 
      lcd.print("Device 2 WARNING");
      lcd.setCursor(0,1); 
      lcd.print("Temp: ");
      lcd.print(dev2_temp);
      lcd.print((char)223);
      lcd.print("C");
      SOUND2 ();
      delay(5000);
     }
     
        if (dev2_count >= 6) 
     {
 
      dev2_warns = true;
      lcd.clear();
      lcd.setCursor(0,0); 
      lcd.print("Device 2 WARNING");
      lcd.setCursor(0,1); 
      lcd.print("Temp: ");
      lcd.print(dev2_temp);
      lcd.print((char)223);
      lcd.print("C");
      SOUND3 ();
      delay(5000);
     }
     
           if (dev1_count == 8) 
     {
           lcd.clear();
      lcd.setCursor(0,0); 
      lcd.print("Sending SMS...");
      lcd.setCursor(0,1); 
      lcd.print("Temp: ");
      lcd.print(dev1_temp);
      lcd.print((char)223);
      lcd.print("C");
      
       sms_msg = "Warning! Device 2 experiencing high risk temperatures. Please text xx to confirm" ;

     }
      
       
   }
   
   else { 
     dev2_count -- ;
     if (dev2_count == 0)
     {
       dev2_count = 1;
     }
      LCD_AUTO ();
   }     
  
   
 } // END TIMER COUNTER
 
 
    
}  // END VOID LOOP





void LCD_AUTO () 
{
  

  lcd.clear();
  lcd.setCursor(0,0); 
  lcd.print("Outside");
  lcd.setCursor(0,1); 
  lcd.print("Temp: ");
  lcd.print(t);
  lcd.print((char)223);
  lcd.print("C");   
  
  
  delay(3000) ;

   
 //------------------- DEVICE 01 ----------------------
  
  lcd.clear();
  lcd.setCursor(0,0); 
  lcd.print("Device 01");
  lcd.setCursor(0,1); 
  lcd.print("Temp: ");
  lcd.print(dev1_temp);
  lcd.print((char)223);
  lcd.print("C");
  
  delay(3000) ;
  

     //------------------- DEVICE 02 ----------------------

  lcd.clear();
  lcd.setCursor(0,0); 
  lcd.print("Device 02");
  lcd.setCursor(0,1); 
  lcd.print("Temp: ");
  lcd.print(dev2_temp);
  lcd.print((char)223);
  lcd.print("C");
  
 delay(3000) ;
 
 LCD_SCREEN ();
  
  
}




void LCD_SCREEN ()
{
  
  // =====================================================
  // =================LCD DISPLAY BSE TEMP================
  // =====================================================
  
  //-------------------- REFERENCE ----------------------
      if (button_num == 1) 
  {

  lcd.clear();
  lcd.setCursor(0,0); 
  lcd.print("Outside");
  lcd.setCursor(0,1); 
  lcd.print("Temp: ");
  lcd.print(t);
  lcd.print((char)223);
  lcd.print("C");   
  }

if (button_num == 2) 
  {
       
 //------------------- DEVICE 01 ----------------------
  
  lcd.clear();
  lcd.setCursor(0,0); 
  lcd.print("Device 01");
  lcd.setCursor(0,1); 
  lcd.print("Temp: ");
  lcd.print(dev1_temp);
  lcd.print((char)223);
  lcd.print("C");
  
  }

     //------------------- DEVICE 02 ----------------------
  
if (button_num == 3) 
  {
    
  lcd.clear();
  lcd.setCursor(0,0); 
  lcd.print("Device 02");
  lcd.setCursor(0,1); 
  lcd.print("Temp: ");
  lcd.print(dev2_temp);
  lcd.print((char)223);
  lcd.print("C");
  }
  
}

// END VOID LCD SCREEN


// VOID SOUND 1




void SOUND1 () 
 {

 int i, duration;

  for (i = 0; i < 2; i++) // step through the song arrays
  {
    duration = beats[i] * tempo;  // length of note/rest in ms

    if (notes[i] == ' ')          // is this a rest? 
    {
      delay(duration);            // then pause for a moment
    }
    else                          // otherwise, play the note
    {
      tone(buzzerPin, frequency(notes[i]), duration);
      delay(duration);            // wait for tone to finish
    }
    delay(tempo/10);              // brief pause between notes
  }

  // We only want to play the song once, so we'll pause forever:
  //while(true){}
  // If you'd like your song to play over and over,
  // remove the above statement
}



void SOUND2 () 
 {

 int i, duration;

  for (i = 0; i < 4; i++) // step through the song arrays
  {
    duration = beats[i] * tempo;  // length of note/rest in ms

    if (notes[i] == ' ')          // is this a rest? 
    {
      delay(duration);            // then pause for a moment
    }
    else                          // otherwise, play the note
    {
      tone(buzzerPin, frequency(notes[i]), duration);
      delay(duration);            // wait for tone to finish
    }
    delay(tempo/10);              // brief pause between notes
  }

  // We only want to play the song once, so we'll pause forever:
  //while(true){}
  // If you'd like your song to play over and over,
  // remove the above statement
}



 void SOUND3 () 
 {

 int i, duration;

  for (i = 0; i < songLength; i++) // step through the song arrays
  {
    duration = beats[i] * tempo;  // length of note/rest in ms

    if (notes[i] == ' ')          // is this a rest? 
    {
      delay(duration);            // then pause for a moment
    }
    else                          // otherwise, play the note
    {
      tone(buzzerPin, frequency(notes[i]), duration);
      delay(duration);            // wait for tone to finish
    }
    delay(tempo/10);              // brief pause between notes
  }

  // We only want to play the song once, so we'll pause forever:
  //while(true){}
  // If you'd like your song to play over and over,
  // remove the above statement
}




int frequency(char note) 
{
  // This function takes a note character (a-g), and returns the
  // corresponding frequency in Hz for the tone() function.

  int i;
  const int numNotes = 8;  // number of notes we're storing

  // The following arrays hold the note characters and their
  // corresponding frequencies. The last "C" note is uppercase
  // to separate it from the first lowercase "c". If you want to
  // add more notes, you'll need to use unique characters.

  // For the "char" (character) type, we put single characters
  // in single quotes.

  char names[] = { 'c', 'd', 'e', 'f', 'g', 'a', 'b', 'C' };
  int frequencies[] = {262, 294, 330, 349, 392, 440, 494, 523};

  // Now we'll search through the letters in the array, and if
  // we find it, we'll return the frequency for that note.

  for (i = 0; i < numNotes; i++)  // Step through the notes
  {
    if (names[i] == note)         // Is this the one?
    {
      return(frequencies[i]);     // Yes! Return the frequency
    }
  }
  return(0);  // We looked through everything and didn't find it,
              // but we still need to return a value, so return 0.
              
              
}











/*================================================

                VOID SETUP

================================================*/
void pin_ISR() {
  
  
  static unsigned long last_interrupt_time = 0;
 unsigned long interrupt_time = millis();
 // If interrupts come faster than 200ms, assume it's a bounce and ignore
 if (interrupt_time - last_interrupt_time > 200) 
 {
  buttonState = digitalRead(buttonPin);
 // digitalWrite(ledPin, buttonState);

  
   button_num ++ ;
       
       if (button_num >= 4)
       {
         button_num = 1;
       }         
       
 LCD_SCREEN();
       
       
  
 }
 last_interrupt_time = interrupt_time;
}



void pin_ISR2() {
  
  
  static unsigned long last_interrupt_time = 0;
 unsigned long interrupt_time = millis();
 // If interrupts come faster than 200ms, assume it's a bounce and ignore
 if (interrupt_time - last_interrupt_time > 200) 
 {
  buttonState = digitalRead(buttonPin2);
 // digitalWrite(ledPin, buttonState);

  
 if (dev1_warns == true) 
  {
   dev1_count = 0; 
     lcd.clear();
  lcd.setCursor(0,0); 
  lcd.print("Device 01");
  lcd.setCursor(0,1); 
  lcd.print("Count Reset ");
  dev1_warns = false;
  delay(2000);
  } 
       
   if (dev2_warns == true) 
  {
   dev2_count = 0;
   
  lcd.clear();
  lcd.setCursor(0,0); 
  lcd.print("Device 02");
  lcd.setCursor(0,1); 
  lcd.print("Count Reset ");
  dev2_warns = false;
  delay(2000);
  
  } 
       
 LCD_SCREEN();
       
       
  
 }
 last_interrupt_time = interrupt_time;
}
  
  

  
  
    

void SIM900power()
// software equivalent of pressing the GSM shield "power" button
{
 // digitalWrite(9, HIGH);
  //delay(1000);
  //digitalWrite(9, LOW);
  //delay(5000);
}


/*================================================

                VOID SMS

================================================*/

void sendSMS()
{
  SIM900.print("AT+CMGF=1\r");                                                        // AT command to send SMS message
  delay(100);

  //Client
//  SIM900.println("AT + CMGS = \"+614xxxxxxxx");   // recipient's mobile number, in international format

 
  delay(100);
  SIM900.println(sms_msg);        // message to send
  delay(100);
  SIM900.println((char)26);                       // End AT command with a ^Z, ASCII code 26
  delay(100); 
  SIM900.println();
  delay(5000);                                     // give module time to send SMS
 // SIM900power();                                   // turn off module
}

