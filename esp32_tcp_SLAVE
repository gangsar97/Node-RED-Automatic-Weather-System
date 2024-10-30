#include <DHT.h>
#include <WiFi.h>
#include <ModbusIP_ESP8266.h>

class HCSR04 {
  private:
    byte _trigPin;
    byte _echoPin;
    long _duration;
    int _distance;

  public:
    HCSR04(byte trigPin, byte echoPin) {
      _trigPin = trigPin;
      _echoPin = echoPin;
    }

    void begin() {
      pinMode(_trigPin, OUTPUT);
      pinMode(_echoPin, INPUT);
    }

    int readDistance() {
      digitalWrite(_trigPin, LOW);
      delayMicroseconds(2);
      digitalWrite(_trigPin, HIGH);
      delayMicroseconds(10);
      digitalWrite(_trigPin, LOW);

      _duration = pulseIn(_echoPin, HIGH);
      _distance = _duration / 58;
      return _distance;
    }
};


// DHT11 config
#define DHTPIN 27
#define DHTTYPE DHT11

// modbus data address
#define SLAVE_ID 1
#define IReg_temp_address 1
#define IReg_hum_address 2
#define IReg_dist_address 3

//ultrasonic HCSR04 config
#define triggerPin 14
#define echoPin 12

HCSR04 ultrasonic(triggerPin, echoPin);
ModbusIP modbus;
DHT dht(DHTPIN, DHTTYPE);

byte mac[] = {
  0xDE, 0xAD, 0xBE, 0xEF, 0xFE, 0xED
};

IPAddress ip(192, 168, 1, 254);
IPAddress netmask(255, 255, 255, 0);
IPAddress gateway(192, 168, 1, 1);
IPAddress dns(8, 8, 8, 8);


void setup() {
  Serial.begin(115200);
  WiFi.config(ip, gateway,netmask,dns);
  WiFi.begin("Edutic.id", "Edutic5758-");
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println("");
  Serial.println("WiFi connected");
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
  dht.begin();
  ultrasonic.begin();

  modbus.server();
  modbus.addIreg(IReg_temp_address);
  modbus.addIreg(IReg_hum_address);
  modbus.addIreg(IReg_dist_address);
}

void loop() {
  float temp = dht.readTemperature();
  float hum = dht.readHumidity();
  int distance = ultrasonic.readDistance();

  Serial.print("Temp: ");
  Serial.print(temp);
  Serial.println(" C");

  Serial.print("Hum: ");
  Serial.print(hum);
  Serial.println(" %");

  Serial.print("Dist: ");
  Serial.print(distance);
  Serial.println(" Cm");
  Serial.println();

  modbus.Ireg(IReg_temp_address, temp * 10);
  modbus.Ireg(IReg_hum_address, hum * 10);
  modbus.Ireg(IReg_dist_address, distance);

  modbus.task();
  delay(500);
}
