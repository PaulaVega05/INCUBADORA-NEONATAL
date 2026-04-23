# INCUBADORA-NEONATAL
Sara Damaris Vasquez Cardenas y Paula Andrea Vega Pardo

## Marco teórico 
### Partes externas / estructura
Las incubadoras neonatales son equipos médicos diseñados para proporcionar un ambiente controlado que permita mantener condiciones óptimas para el desarrollo y estabilidad del recién nacido, especialmente en casos de prematuridad; estos sistemas están conformados por varios subsistemas que trabajan de manera integrada para garantizar el control térmico, la protección del neonato y el monitoreo continuo de sus condiciones.

En cuanto a sus componentes principales de tipo estructural o externo, se destaca la cabina o cámara de incubación, la cual es un compartimiento cerrado generalmente fabricado en material transparente que permite el aislamiento térmico y la observación del neonato. Esta cabina incluye accesos o compuertas que facilitan la intervención médica sin alterar significativamente las condiciones internas e  incorpora un sistema de circulación de aire, encargado de distribuir de manera uniforme la temperatura en el interior y evitar gradientes térmicos.

Adicionalmente, la estructura presenta elementos como la pared doble, que mejora el aislamiento térmico reduciendo la pérdida de calor hacia el exterior, y ojales o accesos adicionales, que permiten la conexión de equipos médicos o sondas sin comprometer el ambiente interno. En la parte inferior, se encuentra una bandeja o superficie de soporte, donde se ubica el neonato, muchas veces integrada con sistemas como balanza digital para el monitoreo del peso sin necesidad de retirarlo de la incubadora.

Por otro lado, la incubadora cuenta con componentes funcionales externos como el depósito de agua para el sistema de humidificación, encargado de mantener niveles adecuados de humedad, y la estructura móvil con ruedas y frenos, que facilita el transporte seguro del equipo dentro del entorno hospitalario. También incorpora elementos de ergonomía como ajuste de altura mediante pedales y compartimientos o cajones, que permiten almacenar insumos médicos.

Finalmente, en la parte superior o lateral se ubican los sistemas de visualización y monitoreo, como pantallas digitales o táctiles, donde se presentan en tiempo real las variables del sistema y se permite la interacción con los parámetros de control. 

A continuación, en la Figura 1 se presentan las principales partes de una incubadora neonatal:

<img width="402" height="226" alt="PARTEFUERA" src="https://github.com/user-attachments/assets/6579f636-2bcd-4945-aa37-0646dedbcb47" />

### Parte eléctrica

Las incubadoras cuentan con componentes eléctricos y funcionales que permiten la regulación activa de las condiciones internas. Entre ellos se encuentran los elementos calefactores, responsables de generar el calor necesario para mantener la temperatura adecuada, y los ventiladores, que permiten la correcta circulación del aire dentro de la cabina, asegurando una distribución homogénea del calor. Asimismo, el sistema incluye una entrada de aire con filtrado, que garantiza la renovación del aire sin introducir contaminantes al entorno del neonato.

También forman parte de estos sistemas los mecanismos de control de humedad, los cuales emplean humidificadores que utilizan calentamiento del agua para generar vapor, permitiendo mantener niveles adecuados de humedad y reducir la pérdida de líquidos a través de la piel del neonato. De igual manera, se incorporan sistemas de control de oxígeno, que regulan el flujo proveniente de una fuente externa mediante válvulas y filtros, asegurando concentraciones adecuadas según las necesidades clínicas.

Adicionalmente, estos sistemas están interconectados mediante módulos de control electrónico, los cuales reciben información de sensores como temperatura del aire, temperatura de la piel y concentración de oxígeno. Esta información es procesada para accionar los diferentes subsistemas, como el calefactor, el ventilador o el humidificador, permitiendo mantener condiciones estables dentro de la incubadora.

Como se observa en la Figura 2, estos componentes eléctricos y funcionales trabajan de manera integrada mediante un esquema de control, donde la interacción entre sensores, actuadores y sistemas de regulación permite mantener un ambiente controlado y seguro para el neonato.

En la Figura 2 se presenta el esquema del sistema electrónico implementado, donde se observa la interconexión entre sensores, controlador y actuadores:

<img width="1181" height="685" alt="SESNSOR" src="https://github.com/user-attachments/assets/3841ae44-29f9-4905-aaab-38f4c3d7178c" />

## Procedimiento 


### Diseño del sistema

Adicionalmente, se consideró el diseño mecánico del sistema, incluyendo una estructura básica que simula el funcionamiento de una incubadora neonatal, utilizando materiales como carton-plástico y vinilo para representar la cabina, también se tuvo en cuenta el soporte de la celda de carga y la disposición de los componentes, buscando lograr una adecuada estabilidad, distribución del calor y medición del peso. Cabe resaltar que se trata de una simulación funcional, en la cual se procuró implementar el sistema de la mejor manera posible con los recursos disponibles.

### Parte mecánica

<img width="960" height="1280" alt="image" src="https://github.com/user-attachments/assets/47047507-e59f-4b03-a149-65ed5fe5c0c2" />
<img width="960" height="1280" alt="image" src="https://github.com/user-attachments/assets/471d3b9c-870e-4f4f-92b8-9574f1076795" />


#### Conexión de hardware / Temperatura 

Para realizar el montaje del controlador de temperatura, se hizo uso del siguiente circuito:

<img width="855" height="426" alt="TEMP" src="https://github.com/user-attachments/assets/eac8b0c4-ed5c-436b-baba-283695897d28" />

De esta manera, se buscó mantener una temperatura estable entre 36 °C y 37.5 °C mediante el uso de dos relés, los cuales controlan el encendido y apagado del ventilador y del bombillo en función de la temperatura detectada por el sensor. Todo este proceso fue gestionado por un microcontrolador, que a través del siguiente código determina el estado de los actuadores y permite regular el sistema de forma automática perimitiendo un sistema de control automática de temperatura tipo ON/OFF.

```
#include <Wire.h>
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

De esta misma manera se permitió la visualización de la temperatura en forma real mediante una pantalla OLED de 3 segmentos y se implemetaron dos LEDs comosistema de alerta sobre el estado de la incubadora, en caso de estar por encimma o debajo del rango de la temperatura ideal se enciende el led rojo y por el contrario al encontrarse en el rango se encenderá el led verde.

#### Conexión de hardware / Peso 

Para la implementación del sistema de medición de peso, se utilizó una celda de carga (galga extensiométrica) de 5 kg en conjunto con el módulo amplificador HX711, con el objetivo de monitorear el peso del neonato dentro de la incubadora. Este sistema se basa en la deformación de la galga al aplicar una carga, generando una señal eléctrica muy pequeña que es amplificada por el módulo HX711 para ser leída por el microcontrolador a través de pines digitales.

<img width="1140" height="451" alt="image" src="https://github.com/user-attachments/assets/c9b117aa-4654-4aca-8337-bc72313f9b41" />
<img width="1598" height="984" alt="image" src="https://github.com/user-attachments/assets/8ec8b3d7-4c28-4cbd-949e-a7e8c28e0e01" />

Se realizó la conexión del módulo al microcontrolador y se implementó un proceso de calibración mediante un factor experimental. Además, se integró una pantalla OLED para visualizar el peso en tiempo real, mostrando los valores tanto en el monitor serial como en la pantalla. El código permite realizar múltiples lecturas y promediarlas con el fin de reducir el ruido en la señal.

Sin embargo, durante las pruebas del sistema, no se lograron obtener mediciones correctas, ya que el valor de peso permanecía constante en cero independientemente de la carga aplicada. Esto indica que el sistema no funcionó adecuadamente, posiblemente debido a errores en la conexión, problemas en la calibración o una mala distribución de la carga.

El siguiente código corresponde a la implementación realizada para la lectura y visualización del peso:
```
#include <HX711.h>
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define DT  4
#define SCK 5

HX711 scale;

// OLED
#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

float factor_calibracion = -6890; // usa el que encontraste

void setup() {
  Serial.begin(115200);

  // HX711
  scale.begin(DT, SCK);
  scale.set_scale(factor_calibracion);
  scale.tare();

  // OLED
  if (!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    Serial.println("Error OLED");
    while (true);
  }

  display.clearDisplay();
  display.setTextSize(2);
  display.setTextColor(WHITE);
}

void loop() {
  float peso = scale.get_units(10);

  Serial.print("Peso: ");
  Serial.println(peso);

  // Mostrar en OLED
  display.clearDisplay();
  display.setCursor(0, 10);
  display.print("Peso:");

  display.setCursor(0, 35);
  display.print(peso, 1);
  display.print(" g");

  display.display();

  delay(500);
}
```



## Costos del sistema y comparación con soluciones comerciales
El sistema desarrollado presenta un costo considerablemente bajo en comparación con las incubadoras neonatales comerciales. A continuación, se detalla el costo aproximado de cada uno de los componentes utilizados en el prototipo:

| Componente                         | Cantidad | Costo unitario (COP) |
|----------------------------------|---------|----------------------|
| ESP32                            | 1       | 35000                | 
| Sensor de temperatura (NTC/DHT)  | 1       | 1500                 | 
| Módulo HX711                     | 1       | 7000                 | 
| Pantalla OLED                    | 1       | 20000                | 
| Módulo relé                      | 2       | 7000                 | 
| Bombillo / resistencia           | 1       | 10000                | 
| Ventilador 12v                   | 1       | 8000                 | 
| Fuente de alimentación           | 1       | 15000                | 
| Material estructural             | 1       | 20000                |
| Galga 5kg                        | 1       | 11000                |
| **TOTAL**                        |         | **134.500**           | 

En contraste, las incubadoras neonatales comerciales presentan costos significativamente más elevados, como se muestra en la siguiente tabla, donde se comparan diferentes fabricantes reconocidos en el sector, estas diferencias de precio se deben a la integración de tecnologías más avanzadas, mayor precisión en los sensores, sistemas de control sofisticados y cumplimiento de normativas médicas.

| Marca                     | Tipo de equipo        | Rango de precio aproximado |
|--------------------------|---------------------|---------------------------|
| Dräger                   | Alta gama            | 6 – 80+ millones COP      |
| Instrumentalia S.A.S.    | Distribuidor clínico | 20 – 60 millones COP      |
| LEEX Medical             | Gama media           | 10 – 40 millones COP      |
| GE Healthcare            | Alta gama            | 15 – 70 millones COP      |
| Atom Medical             | Gama media-alta      | 12 – 50 millones COP      |
| **Prototipo desarrollado** | Bajo costo           | **~134.500 COP**          |

De esta manera, al comparar directamente las características del sistema desarrollado con las incubadoras comerciales, se observa que, aunque el prototipo cumple con funciones básicas como el control de temperatura y medición de peso, existen diferencias importantes en términos de precisión, seguridad y capacidades de monitoreo. Como se muestra en la siguiente tabla, los equipos comerciales incorporan múltiples variables fisiológicas, sistemas de control avanzados y certificaciones que garantizan su uso en entornos clínicos, mientras que el prototipo se limita a aplicaciones educativas o de investigación.

| Característica            | Prototipo desarrollado        | Incubadora comercial                      |
|--------------------------|------------------------------|------------------------------------------|
| Costo                    | ~155.000 COP                 | 7 – 30+ millones COP                     |
| Precisión                | Media / baja                 | Alta                                     |
| Tipo de control          | ON/OFF básico                | PID avanzado                             |
| Variables controladas    | Temperatura y peso           | Temp, humedad, O₂, signos vitales        |
| Seguridad                | Básica                       | Alta (alarmas, redundancia)              |
| Certificación médica     | No                           | Sí                                       |

El análisis de costos y características permite evidenciar una alta diferencia entre el prototipo desarrollado y las incubadoras neonatales comerciales, esta brecha económica se justifica por la incorporación, en los dispositivos comerciales, de sensores de alta precisión, sistemas de control avanzados como PID, monitoreo de múltiples variables fisiológicas y estrictos estándares de seguridad y certificación médica. 

En contraste, el prototipo desarrollado ofrece una solución funcional básica, capaz de controlar la temperatura y medir el peso, lo que lo hace adecuado para fines académicos y de investigación, in embargo carece de la robustez, confiabilidad y seguridad necesarias para su implementación en entornos clínicos.

## Resultados y conclusión

El resultado obtenido indica que el sistema de medición de peso funciona correctamente cuando se evalúa de manera independiente; sin embargo, al integrarlo dentro de la estructura de la incubadora no se obtuvieron resultados adecuados, lo que evidencia la necesidad de mejorar la parte mecánica del sistema, especialmente en aspectos como el soporte, la distribución de la carga y el acoplamiento de la celda de carga.

En cuanto al control de temperatura, se evidenciaron inconvenientes asociados al módulo relé, lo que sugiere la necesidad de optimizar este componente para garantizar un accionamiento más estable del sistema de calefacción. 

No obstante, el resto del sistema mostró un comportamiento satisfactorio, ya que tanto el sensor de temperatura, como el sistema de alarmas y la pantalla OLED, funcionaron correctamente, permitiendo el monitoreo y la visualización adecuada de las variables.

## Respuesta a las preguntas
- Pregunta 1: ¿Qué otras variables (y por qué) además de las aquí mencionadas son críticas en el monitoreo neonatal?
  
Otras variables críticas que podemos ecnotrar en el monitoreo neonatal incluyen la humedad relativa, la cual es fundamental porque los neonatos, especialmente los prematuros, presentan una piel inmadura y una alta relación superficie/volumen, lo que favorece una pérdida excesiva de agua por evaporación y puede llevar rápidamente a deshidratación y alteraciones electrolíticas.

Por otra parte se ecnuentra la saturación de oxígeno (SpO₂), que permite evaluar la adecuada oxigenación de la hemoglobina, siendo crucial ya que el sistema respiratorio del neonato es inmaduro y una baja oxigenación puede comprometer directamente la función celular y el metabolismo tisular.

Además la frecuencia cardíaca, que refleja el estado hemodinámico y la capacidad del sistema cardiovascular para mantener una perfusión adecuada de los órganos, siendo especialmente importante porque el control autónomo del corazón aún no está completamente desarrollado.

La frecuencia respiratoria, que permite evaluar la eficiencia del intercambio gaseoso, ya que los pulmones del neonato, en particular en prematuros, pueden no estar completamente desarrollados, lo que aumenta el riesgo de dificultad respiratoria.

El fallo de cualquiera de estas variables estaría comprometiendo la estabilidad del neonato.

- Pregunta 2: ¿Qué haría falta para convertir el sistema desarrollado en una incubadora neonatal real?
  
Para convertir el sistema desarrollado en una incubadora neonatal real sería necesario incorporar sensores médicos de alta precisión y calibración certificada, así como implementar sistemas de control más avanzados, como control proporcional o control PID, que permitan una regulación más estable y sin sobreoscilaciones. Además, se deben incluir sistemas de seguridad redundantes, como alarmas auditivas y visuales ante fallos, sobretemperatura o pérdida de energía, así como respaldo eléctrico.

También sería necesario integrar el control de variables adicionales como la humedad y la concentración de oxígeno, fundamentales para el bienestar del neonato, y diseñar una estructura física adecuada, con aislamiento térmico, filtrado de aire y materiales biocompatibles que garanticen condiciones higiénicas.

Finalmente, el sistema debe cumplir con normativas y certificaciones médicas internacionales, lo cual implica pruebas rigurosas de seguridad, confiabilidad y desempeño clínico, asegurando así su uso seguro en entornos hospitalarios.

- Pregunta 3: ¿Qué semejanzas hay entre una incubadora neonatal y una servo-cuna?
  
La incubadora neonatal y la servo-cuna tienen en común que ambas buscan regular la temperatura corporal del recién nacido mediante sistemas de control automático basados en sensores, ya que el neonato presenta una capacidad limitada de termorregulación debido a la inmadurez de su sistema nervioso y a la baja cantidad de tejido adiposo. En ambos sistemas se emplea retroalimentación (feedback) para ajustar el aporte de calor y mantener condiciones térmicas estables, evitando tanto la hipotermia como la hipertermia, las cuales pueden alterar el metabolismo, el consumo de oxígeno y la estabilidad hemodinámica.

Sin embargo, la diferencia principal radica en el tipo de control térmico y el entorno: la incubadora neonatal es un sistema cerrado que regula no solo la temperatura, sino también la humedad y el aislamiento del ambiente, reduciendo la pérdida de calor por convección, evaporación y radiación; mientras que la servo-cuna es un sistema abierto que utiliza calor radiante dirigido al neonato, permitiendo un acceso más fácil para intervenciones médicas, pero con menor control sobre variables ambientales como la humedad.
  
## REFERENCIAS 
Udocz. (2023). Incubadora neonatal.
https://www.udocz.com/apuntes/609413/incubadora-neonatal

Organización Mundial de la Salud. (2016). Manual de atención neonatal.
https://platform.who.int/docs/default-source/mca-documents/policy-documents/operational-guidance/pry-mn-48-03-operational-guidance-2016-esp-manual-de-atenci%C3%93n-neonatal--autorizado-mspbs-resoluci%C3%93n-ministerial-sg-n-816.pdf

Universidad Dr. Rafael Belloso Chacín. (2010). Sistema de control de temperatura, monitoreo de peso y humedad en incubadoras neonatales.
https://virtual.urbe.edu/tesispub/0091333/intro.pdf

Universidad Wiener. (2019). Incubadoras neonatales y control térmico.
https://repositorio.uwiener.edu.pe/bitstreams/c8ab8b70-7e2b-4a1f-8b2e-2c961d749436/download

Universitat Politècnica de Catalunya. (2013). Desarrollo histórico de incubadoras neonatales.
https://www.tdx.cat/bitstream/handle/10803/131097/TECJ.pdf
