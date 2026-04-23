# INCUBADORA-NEONATAL
Sara Damaris Vasquez Cardenas y Paula Andrea Vega Pardo

# Descripción
Este proyecto consiste en el desarrollo de un prototipo de incubadora neonatal de bajo costo, diseñado para controlar variables básicas como la temperatura y el peso, utilizando un sistema basado en ESP32, sensores y actuadores electrónicos

# Procedimiento 
- Diseño del sistema
Primero se definieron las variables a controlar: Temperatura y peso, poteriormente se seleccionaron los componentes electrónicos necesarios como el ESP32, sensor de temperatura, módulo HX711, relé y sistema de calefacción.

- Conexión de hardware
Para relaizar el montaje del controlador de temperatura, se hizo uso del siguiente circuito:

<img width="855" height="426" alt="TEMP" src="https://github.com/user-attachments/assets/eac8b0c4-ed5c-436b-baba-283695897d28" />

De esta manera, se buscó mantener una temperatura estable entre 36 °C y 37.5 °C mediante el uso de dos relés, los cuales controlan el encendido y apagado del ventilador y del bombillo en función de la temperatura detectada por el sensor. Todo este proceso fue gestionado por un microcontrolador, que a través del siguiente código determina el estado de los actuadores y permite regular el sistema de forma automática perimitiendo un sistema de control automática de temperatura tipo ON/OFF.

``` #include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include <math.h>

// ===== OLED =====
#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

// ===== NTC =====
#define PIN_NTC 34
#define R_FIXED 10000.0
#define BETA 3950
#define T0 298.15
#define R0 10000.0

// ===== RELÉS =====
#define RELE_BOMBILLO 26
#define RELE_VENTILADOR 27

// ===== CONTROL =====
float setpoint = 37.5;
float margen = 0.5;

void setup() {
  Serial.begin(115200);
  Wire.begin(21, 22);

  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
  display.clearDisplay();

  pinMode(RELE_BOMBILLO, OUTPUT);
  pinMode(RELE_VENTILADOR, OUTPUT);

  digitalWrite(RELE_BOMBILLO, HIGH);   // apagado
  digitalWrite(RELE_VENTILADOR, HIGH); // apagado
}

void loop() {

  int adc = analogRead(PIN_NTC);
  float V = adc * (3.3 / 4095.0);
  float R_ntc = (3.3 * R_FIXED / V) - R_FIXED;
  float tempK = 1.0 / ((1.0/T0) + (1.0/BETA) * log(R_ntc/R0));
  float tempC = tempK - 273.15;

  Serial.println(tempC);

  // ===== CONTROL =====
  if (tempC < setpoint - margen) {
    digitalWrite(RELE_BOMBILLO, LOW);
    digitalWrite(RELE_VENTILADOR, HIGH);
  }
  else if (tempC > setpoint + margen) {
    digitalWrite(RELE_BOMBILLO, HIGH);
    digitalWrite(RELE_VENTILADOR, LOW);
  }
  else {
    digitalWrite(RELE_BOMBILLO, HIGH);
    digitalWrite(RELE_VENTILADOR, HIGH);
  }

  // ===== OLED =====
  display.clearDisplay();

  display.setTextSize(2);
  display.setCursor(0,0);
  display.print(tempC,1);
  display.print(" C");

  display.setTextSize(1);
  display.setCursor(0,40);

  if (tempC < setpoint - margen) {
    display.print("Calentando");
  } else if (tempC > setpoint + margen) {
    display.print("Enfriando");
  } else {
    display.print("Estable");
  }

  display.display();

  delay(1000);
}
```

De esta misma manera se permitió la visualización de la temperatura en forma real mediante una pantalla OLED de 3 segmentos y se implemetaron dos leds como alarma para alertar sobre el estado de la incubadora, en caso de estar por encimma o debajo del rango de la temperatura ideal se enciende el led rojo y por el contrario al encontrarse en el rango se encerá el led verde.


  
