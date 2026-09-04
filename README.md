# Air-purifier-system-
Arduino based air purifier with gas and dust detector
Problem statement; Indoor air pollution has become a major health threat due to increasing levels of dust, smog, smoke and harmful gases in residential, commercial and industrial environment. Prolonged exposure to poor indoor air quality causes respiratory diseases, allergies and reduces over wellbeing of especially vulnerable groups like children, elderly and individuals with asthma.

 Solution; To develop a low-cost and energy efficient automated air purifier using Arduino. The system uses MQ135 and DSM501A sensors to detect gas and dust levels. When pollution exceedes safe thresholds 300ppm for gas and 100 micro gram per meter cubed for dust the system automatically turns ON a fan to purify the air and activates a buzzer to alert users. Air quality readings are displayed on an LCD for real-time monitoring. 
 
Component	Arduino Pin	Purpose
MQ135	A0	Reads gas level
Fan (Relay)	7	Controls purifier fan
Buzzer	8	Audible warning
LED	9	Visual alert

Threshold Values
#define GAS_THRESHOLD 300
#define PM25_THRESHOLD 75
#define PM10_THRESHOLD 100
These define danger levels:
*If gas value > 300 → Air is polluted
*If PM2.5 > 75 µg/m³ → Dangerous
*If PM10 > 100 µg/m³ → Dangerous
These values should be adjusted after sensor calibration.

step by step instructions 
1.    setup() Function
This runs once when the system starts.
Step 1: Start Serial Communication
Serial.begin(9600);
dustSerial.begin(9600);
9600 baud rate for monitoring and dust sensor communication.
Step 2: Initialize LCD
lcd.init();
lcd.backlight();
Displays:
Air Quality Init
Step 3: Set Pin Modes
pinMode(FAN_PIN, OUTPUT);
pinMode(BUZZER_PIN, OUTPUT);
pinMode(LED_PIN, OUTPUT);
Step 4: Default OFF State
digitalWrite(FAN_PIN, LOW);
digitalWrite(BUZZER_PIN, LOW);
digitalWrite(LED_PIN, LOW);
All alert devices are OFF at startup.

2. loop() Function
This runs continuously.

Step 1: Read MQ135 Gas Sensor
int gasValue = analogRead(MQ135_PIN);
*Reads analog voltage (0–1023)
*Higher value = more gas concentration

Step 2: Read PM2.5 and PM10
bool valid = readZPH01(&pm25, &pm10);
This calls your custom function to extract:
*PM2.5 value
*PM10 value

Step 3: Display on LCD
First line:
G:xxx P2:xx
*Gas level
*PM2.5 value
Second line:
P10:xx
*PM10 value

Step 4: Air Quality Decision Logic
if (gasValue > GAS_THRESHOLD || pm25 > PM25_THRESHOLD || pm10 > PM10_THRESHOLD)
If ANY pollutant exceeds threshold:
System Response:
digitalWrite(FAN_PIN, HIGH);
digitalWrite(BUZZER_PIN, HIGH);
digitalWrite(LED_PIN, HIGH);
✔ Fan turns ON✔ Buzzer sounds✔ LED lights up

Otherwise:
digitalWrite(FAN_PIN, LOW);
digitalWrite(BUZZER_PIN, LOW);
digitalWrite(LED_PIN, LOW);
✔ Fan OFF✔ No alert

3.ZPH01 Reading Function Explained
bool readZPH01(int* pm25, int* pm10)
This function extracts PM data from the dust sensor.

Step 1: Check Available Data
if (dustSerial.available() >= 9)
The sensor sends 9-byte data packets.

Step 2: Check Start Byte
if (dustSerial.read() == 0xFF)
Every valid packet begins with 0xFF.

Step 3: Read Data Bytes
dustSerial.readBytes(data, 9);

Step 4: Extract PM Values
*pm25 = ((int)data[0] << 8) | data[1];
*pm10 = ((int)data[2] << 8) | data[3];
This combines two bytes into one integer:
Example:
*High byte << 8
*OR with low byte
*Gives final PM value

Step 5: Return True if Valid
return true;
If no valid packet:
return false;

4. Overall System Operation Flow
1.Sensors measure air pollution.
2.Arduino processes sensor data.
3.LCD displays real-time air quality.
4.If pollution is high:
oFan activates
oBuzzer alerts
oLED turns ON
5.If air is clean:
oSystem stays idle
Loop repeats every 2 seconds

Engineering Group 16
