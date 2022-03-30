# Wear-The-Ear

Problem Statement:
Hearing Impaired people find it challenging to identify and detect the direction of sound. 

Problems faced by people with low hearing:
●	 Difficulty of differently-abled people to navigate around traffic.
●	The most valuable thing for a differently-abled person is gaining independence. 
A deaf person can lead an independent life with some specially designed 
adaptive tools and devices for them.

Statistics:
1.	World statistics:
➔	By 2050 nearly 2.5 billion people are projected to have some degree of hearing loss and at least 700 million will require hearing rehabilitation.
➔	Over 1 billion young adults are at risk of permanent, avoidable hearing loss due to unsafe listening practices.
➔	An annual additional investment of less than US$ 1.40 per person is needed to scale up ear and hearing care services globally.
➔	Over a 10-year period, this promises a return of nearly US$ 16 for every US dollar invested.
➔	Over 5% of the world’s population – or 430 million people – require rehabilitation to address their ‘disabling’ hearing loss (432 million adults and 34 million children). 
➔	It is estimated that by 2050 over 700 million people – or one in every ten people – will have disabling hearing loss.



2.	Indian Statistics: 
➔	As per WHO estimates in India, there are approximately 63 million people, who are suffering from Significant Auditory Impairment; this places the estimated prevalence at 6.3% in the Indian population. 
➔	As per NSSO survey, currently there are 291 persons per one lakh population who are suffering from severe to profound hearing loss (NSSO, 2001).

3.	Accident Statistics:








Existing Solutions:
https://www.healthyhearing.com/report/53155-Buzz-device-routes-sound-through-skin


---------------------------------------------------------------------------------------------------------------------

What is required in an ideal product
1.	A product that detects as well as identifies the sound accurately. 
2.	Hearing impaired people often face psychological disorders of loneliness, the product should be able to fill the void to some extent.
3.	A compact product that is worn on the body and gets blended with the outfit in public places.
4.	 A good range coverage to detect all potential sounds.

Components:

1.	Sound Detection Sensor Module (Four) - The sensor can measure whether sound is present. Unlike a microphone, it does not measure the frequency or volume of the sound. It is a binary sensor that only indicates whether sound is detected. We could use data sampling to determine the intensity of sound sampling across a duration of 10ms, this would help us eliminate noise.

2.	Arduino Nano R3 - The Arduino Nano R3 is a microcontroller board based on the ATMega328p. It has 14 digital input/output pins (of which 6 can be used as PWM outputs), 6 analogue inputs, an on-board resonator, a reset button, and holes for mounting pin headers.

3.	Shaft less Vibration Motor (Five) - This is an extremely useful vibration motor in a variety of alert systems. Its small size (10mm x 4mm) is the biggest advantage, it can be fitted inside the smallest of objects. Works great at 3V DC and has an adhesive surface on both sides that helps in easy installation of motors on any kind of surface.

4.	2N5551 Transistor 1 KiloOhm Resistor and diode (four of each) - one for each vibration motor.
5.	USB2.0 Mini Microphone - Omni directional noise-canceling mic picks up sound from longer distances.
6.	Raspberry Pi 4 (2gb) - is a small single-board computer. Often used for embedded Linux applications. It is widely used in many areas because of its low cost, modularity, and open design. It forms the core of the device.

                       


Our Solution:

*insert CAD model

1.	This product consists of four loudness sensors to decide the direction of the incoming sound and a microphone input the sound.
2.	These signals would be conveyed to the user to make them aware of the surrounding.
3.	We first convert the data output by the sound sensor to loudness in the Arduino. Then the Arduino processes the inputs to recognise which direction the sound is coming from.
4.	Simultaneously the Raspberry Pi processes the sound signal, classifies the sound and conveys the result to Arduino.
5.	Arduino conveys the direction and type of sound to the user via vibration modules i.e. when the sound source is detected to be behind the person the Arduino turns the back vibration feedback on and the person becomes aware that there is a sound source behind him.
6.	 Also the Arduino gives the user feedback in the form of set responses to the human.
7.	Thus the human would end up not only being aware of the direction of sound but also the type of sound.

Salient Features:

1.	Will convey the direction of sound to the deaf person so that they could respond as necessary, as in case of a horn a person would move out of the way.
2.	Will convey what type the sound it is, could be a horn or of a kid crying, it could be of human speech or an engine running or a dog barking.
3.	The specially abled person at their home would be in better control of their surroundings, as they would be able to detect if the telephone is ringing or if a kid is crying. This would allow a deaf parent to take better care of their younger ones.
4.	The product is non-intrusive to the lifestyle and the comfort of the person as the human interface is in the form of a thin belt which is an everyday wearable.
 


---------------------------------------------------------------------------------------------------------------------






Code:
On the Raspberry Pi - ran with Node.js and p5.js 

// https://opensource.org/licenses/MIT
// Global variable to store the classifier
let classifier;
// Label (start by showing listening)
let label = "listening";
// Teachable Machine model URL:
let soundModelURL = 'https://teachablemachine.withgoogle.com/models/h3p9R41J/model.json';
function preload() {
 // Load the model
    classifier = ml5.soundClassifier(soundModelURL);
}
function setup() {
createCanvas(320, 240);
// Start classifying
// The sound model will continuously listen to the //microphone
classifier.classify(gotResult);
}
function draw() {
  	background(0);
  	// Draw the label in the canvas
  	fill(255);
  	textSize(32);
  	textAlign(CENTER, CENTER);
  	text(label, width / 2, height / 2);
}
// The model recognizing a sound will trigger this event
function gotResult(error, results) {
  if (error) {
    console.error(error);
    return;
  }
  // The results are in an array ordered by confidence.
  // console.log(results[0]);
  label = results[0];//now label stores the index of the  //label
  port.write(label, (err) => {
    if (err) {
      return console.log('Error on write: ', err.message);
    }
    console.log('message written');
  });
}

 
On the Arduino
unsigned long millisCurrent;
unsigned long millisLast = 0;
const byte DATA_MAX_SIZE = 32;
char data[DATA_MAX_SIZE];   // an array to store the received data
char c;
unsigned long millisElapsed = 0;
int a1 = 0;
int a2 = 0;
int a3 = 0;
int a4 = 0;
int k = 1; //this should be changed
void setup(){
  Serial.begin(9600);
}
char receiveData() {
static char endMarker = '\n'; // message separator
char receivedChar;     // read char from serial port
int ndx = 0;          // current index of data buffer  //clean data buffer
memset(data, 32, sizeof(data));  // read while we have data //available and we are
// still receiving the same message.
while (Serial.available() > 0) {
receivedChar = Serial.read();
if (receivedChar == endMarker) {
data[ndx] = '\0'; // end current message
return data[0];
    	}    
// looks like a valid message char, so append it and
    	// increment our index
    	data[ndx] = receivedChar;
ndx++;    // if the message is larger than our max 
//size then
    	// stop receiving and clear the data buffer. this will
    	// most likely cause the next part of the message
    	// to be truncated as well, but hopefully when you
    	// parse the message, you'll be able to tell that it's
    	// not a valid message.
    if (ndx >= DATA_MAX_SIZE) break;
    
  }
// no more available bytes to read from serial and we
// did not receive the separato. it's an incomplete message!
  Serial.println("error: incomplete message");
  Serial.println(data);
  memset(data, 32, sizeof(data));
}
void loop() {
millisCurrent = millis();
  if (digitalRead(7) == LOW) {
    a1++;
    a4++;
  }
  if (digitalRead(8) == LOW) {
    a1++;
    a2++;
  }
  if (digitalRead(12) == LOW) {
                a2++;
    a3++;
  }
  if (digitalRead(13) == LOW) {
    a3++;
    a4++;
  }
  if (millisCurrent - millisLast > 10) {
    if (a1 > a2 && a1 > a3 && a1 > a4) {
      analogWrite(6, a1 / (k));
    }
    if (a2 > a1 && a2 > a3 && a2 > a4) {
      analogWrite(9, a1 / (k));
    }
    if (a3 > a1 && a3 > a2 && a3 > a4) {
      analogWrite(10, a1 / (k));
    }
    if (a4 > a1 && a4 > a2 && a4 > a3) {
      analogWrite(11, a1 / (k));
    }
    a1 = 0;
    a2 = 0;
    a3 = 0;
    a4 = 0;
    millisLast = millisCurrent;
  }
  if (millisCurrent - millisLast > 7) {
 analogWrite(6, 0);
 analogWrite(9, 0);
 analogWrite(10, 0);
 analogWrite(11, 0);
  }
  c = receiveData();
  if (c == '1') {
    digitalWrite(3, 1);
    delay(100);
    digitalWrite(3, 0);
    delay(50);
    digitalWrite(3, 1);
    delay(25);
    digitalWrite(3, 0);
    delay(50);
    digitalWrite(3, 1);
    delay(25);
    digitalWrite(3, 0);
    delay(50);
    digitalWrite(3, 1);
    delay(25);
    digitalWrite(3, 0);
    delay(25);
  } else if (c == '2') {
    digitalWrite(3, 1);
    delay(100);
    digitalWrite(3, 0);
    delay(50);
    digitalWrite(3, 1);
    delay(25);
    digitalWrite(3, 0);
    delay(50);
    digitalWrite(3, 1);
    delay(100);
    digitalWrite(3, 0);
    delay(50);
    digitalWrite(3, 1);
    delay(25);
    digitalWrite(3, 0);
    delay(25);
 } else if (c == '3') {
    digitalWrite(3, 1);
    delay(25);
    digitalWrite(3, 0);
    delay(50);
    digitalWrite(3, 1);
    delay(25);
    digitalWrite(3, 0);
    delay(50);
    digitalWrite(3, 1);
    delay(25);
    digitalWrite(3, 0);
    delay(50);
    digitalWrite(3, 1);
    delay(25);
    digitalWrite(3, 0);
    delay(25);
  } else if (c == '4') {
    digitalWrite(3, 1);
    delay(100);
    digitalWrite(3, 0);
    delay(50);
    digitalWrite(3, 1);
    delay(25);
    digitalWrite(3, 0);
    delay(50);
    digitalWrite(3, 1);
    delay(100);
    digitalWrite(3, 0);
    delay(25);
} else if (c == '5') {
    digitalWrite(3, 1);
    delay(100);
    digitalWrite(3, 0);
    delay(50);
    digitalWrite(3, 1);
    delay(100);
    digitalWrite(3, 0);
    delay(50);
    digitalWrite(3, 1);
    delay(25);
    digitalWrite(3, 0);
    delay(50);
    digitalWrite(3, 1);
    delay(100);
    digitalWrite(3, 0);
    delay(25);
  } else if (c == '6') {
    digitalWrite(3, 1);
    delay(25);
    digitalWrite(3, 0);
    delay(50);
    digitalWrite(3, 1);
    delay(100);
    digitalWrite(3, 0);
    delay(50);
    digitalWrite(3, 1);
    delay(25);
    digitalWrite(3, 0);
    delay(25);
  } else if (c == '6') {
    digitalWrite(3, 1);
    delay(25);
    digitalWrite(3, 0);
    delay(50);
    digitalWrite(3, 1);
    delay(25);
    digitalWrite(3, 0);
    delay(50);
    digitalWrite(3, 1);
    delay(25);
    digitalWrite(3, 0);
    delay(25);
  }
}


 









Cost Estimation:

S No	Component	Quantity	Weight	Cost (INR)
1.	Sound Detection Sensor Module 	4	5x4 grams	60x4
2.	Arduino Nano -	1	7x1 grams	280x1
3.	Shaft less Vibration Motor	5	0.75x5 grams	30x5
4.	USB2.0 Mini Microphone	1	5x1 grams	250x1
5.	Raspberry Pi 4 (2gb)	1	46x1 grams	2663x1
	Additional Charges			200
	Total Cost			3783


Future prospects:
● The library of sounds could be expanded to include many more distinctions and new sounds
We could give the user more feedback on the quality / frequency of sound along with the direction and the w.






































ANNEXURE
