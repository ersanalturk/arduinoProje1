#include <Servo.h>

#include <RBD_Light.h> // Used for lights
/*
 * Arduino Clap/Noise Counter
 * Joshua Anderson
 * Description: Counts claps, or other noises.
 * Uses: Clap lights, clap door lock, etc...
 * Note: Unreliable at times, so not recommended for security.
 */
const int micIn = 7; // Input pin for mic module
const int ledPin = 12; // Output pin for the light
const int ledPinYesil = 3; // Output pin for the Yeşil light
bool ledState = false; // The current state of the light
bool relayState = true; // The current state of the relay
const int relayPin = 8; // Output pin for the relay
const int spaced = 100; // Time required between claps, in ticks not ms
int clapCount = 0; // The clap count
int submitTime = 0; // Time since last clap, used for submitting after time
const int notifyPin = 13; // Output pin for notification light
RBD::Light light(ledPin); // Set up the light LED
RBD::Light lightYesil(ledPinYesil); // Set up the Yeşil light LED
RBD::Light notifyLed(notifyPin); // Set up the notify LED

/*//ir kumanda kodları
#include<IRremote.h>  //IR Kumanda Kütüphaneyi ekle
int RECV_PIN=11;
IRrecv irrecv(RECV_PIN);
decode_results results;

//ir kumanda kodları bitiş*/

//Kotrol (kapi- lamba)
bool kapiKapali=true;
bool lambaAcik=false;


//buzzer kodları
int buzzerPin = 2;
int notaSayisi = 8;
int C = 262;
int D = 294;
int E = 330;
int F = 349;
int G = 392;
int A = 440;
int B = 494;
int C_ = 523;
int notalar[] = {C, D, E, F, G, A, B, C_};
//buzzer kodları bitiş

Servo servoKapi;

void setup() {
  pinMode(micIn, INPUT); // Set up the mic
  pinMode(relayPin, OUTPUT); // Set up the relay
  pinMode(3,OUTPUT);
  digitalWrite(3,0); // Pin3 teki lambayı söndür
  delay(100);
  light.off();
  Serial.begin(9600); // Start up Serial
  /*irrecv.enableIRIn();  //11.Pini IR kumanda için giriş pini olarak tanımla*/
  Serial.println("Sistem başlatıldı."); // Tell Serial it's on and ready
//açılış melodisi
for (int i = 0; i < notaSayisi; i++)
  {
    tone(buzzerPin, notalar[i]);
    delay(80);
    noTone(buzzerPin);
    delay(20);
  }
  noTone(buzzerPin);
}

void loop() {
  notifyLed.update(); // Update the notifyLED
  light.update();
  if(Serial.available()){
    clapEval(Serial.parseInt()); // Serialekranından gelen el çırpma sayısını hesapla
  }
  if(digitalRead(micIn)==true){ // If there's a clap (false means on)
    submitTime=0; // Reset submit time
    clapCount++; // Add one clap to count
    notify(1); // Blink the notify LED
    Serial.print("Şu ana kadar algılanan alkış sayısı: "); // Tell Serial the clap data
    Serial.println(clapCount);
    delay(spaced); // Wait to listen
  }
  if(submitTime>=32000 && clapCount>0) { // If ready to submit
    Serial.println(clapCount); // Tell Serial the clap count
    clapEval(clapCount); // Evaluate what to do
    clapCount=0; // Reset the count
  }
  submitTime++; // Increment time

  //IR Kodları
  /*if(irrecv.decode(&results)){
    Serial.println(results.value, HEX);  //alınan değeri HEX 
    irrecv.resume();  // sonraki değeri almak için
    }
    delay(100);*/
}
void clapEval( int num ) { // Clap count evaluation - What to do...
  notify(num); // Tell user how many claps were there
  switch(num) { // Pick one based on clap count
      case 1:
        Serial.println("Işıklar"); // Tell Serial what we're doing
        if(light.isOn()){ // If the light is on
          light.off(); // Turn it off
          lightYesil.off(); // Turn it off
          lambaAcik=false;
        }else{ // Otherwise
          light.on(); // Turn it on
          lightYesil.on(); // Turn it off
          lambaAcik=true;
        }
      break;
      case 2:
        Serial.println("Kapı servo");
        /*if(lightYesil.isOn()){ // If the light is on
          lightYesil.off(); // Turn it off
        }else{ // Otherwise
          lightYesil.on(); // Turn it off
        }*/
        if(kapiKapali==true){
          servoKapi.attach(9);
          servoKapi.write(15);
          delay(100);
          servoKapi.write(30);
          delay(100);
          servoKapi.write(45);
          delay(100);
          servoKapi.write(60);
          delay(100);
          servoKapi.write(75);
          delay(100);
          servoKapi.write(90);
          delay(100);
          kapiKapali=false;
          break;
          }
        if(kapiKapali==false){
          {
          servoKapi.write(-15);
          delay(100);
          servoKapi.write(-30);
          delay(100);
          servoKapi.write(-45);
          delay(100);
          servoKapi.write(-60);
          delay(100);
          servoKapi.write(-75);
          delay(100);
          servoKapi.write(-90);
          delay(100);
          kapiKapali=true;
          break;
          }
        }
      break;
      case 3:
        Serial.println("Relay toggle");
        digitalWrite(relayPin, !relayState); // Toggle actual relay
        relayState = !relayState; // Toggle relay variable
        delay(70); // So it won't trigger a clap
      break;
      default:
        Serial.println("3'ten fazla algılandı...İşlem yok."); // No entry for this many claps
      break;
    }
}
void notify(int count) { // Blink however many times
  notifyLed.blink(50,50,count);
}
