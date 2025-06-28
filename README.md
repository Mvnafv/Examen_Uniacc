# ExamenElectronicaUniacc

### Tu vida bajo un botón

## Descripción conceptual.

Bueno, para describir mi trabajo me hice unas preguntas base conforme todo iba avanzando respecto al trabajo. Lo primero, partiendo por la base, fue ¿qué hacer con tantas piezas?, ¿cuál pieza elegir? Todo moviéndose en mi mente para llegar a una idea final para usar entre tantas piezas. Una pantalla LCD 16x2 con adaptador posterior y una placa 4x4 de 16 botones. Aquí surge: ¿Hasta dónde puedo llegar con esto?, pensando en las posibilidades para usar las piezas. Luego de elegirlas nos dieron un pequeño tiempo para investigar sobre el uso de las piezas por separado y ver cómo unirlas en la protoboard con el Arduino. Aquí me nace una pregunta pensando en casa: "¿Puedo hacer algún juego?". Fue donde pensé en un juego clásico que solía jugar llamado "El ahorcado" o "Colgado" (tiene varios nombres). Hablando con mi profesor en clases y viendo cómo va mi código junto con lo que estoy creando, luego de buscar ideas, referencias y tomando en cuenta las respuestas de mi profesor, terminé formando un juego donde la máquina tiene varias formas de responder al usuario. Describiendo un poco, puede hablar sola, insultar y responder al usuario. Este "juego" puede dejarse sin interactuar o interactuando con los botones. La máquina tiene preguntas divertidas como "raras" en el buen sentido; la única incógnita es que las respuestas serán invisibles y un botón puede reiniciar la máquina, pero solo yo, "el creador", lo conozco.

Materiales

>-Pantalla LCD 16x2px.

>-Matriz 4x4 (16 Bótones).

>-Arduino Uno(USB).

>-Cables de coneccion.

## Descripción técnica

El funcionamiento de mi máquina está entre la interacción del usuario con la máquina o solamente la máquina. El usuario tendrá un panel con 16 botones; cada botón tendrá una respuesta que puede ser buena o mala dependiendo de cómo responda la máquina. La máquina tendrá la opción de responderle según las respuestas del usuario, como comenté antes, pero podrá hacer más. Si presionan muchas veces el mismo botón, esta se enojará y comenzará a insultar al usuario, pero si esta no recibe alguna interacción en los botones, comenzará a hablar sola con una particularidad. 

### Esquema del circuito.

![Imagen](./Imagenes/Esquema_page-0001.jpg)

### Foto del circuito armado

![Imagen](./Imagenes/Circuito.png)

Código
```
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <Keypad.h>

// LCD 16x2 I2C
LiquidCrystal_I2C lcd(0x27, 16, 2);

// Keypad 4x4
const byte ROWS = 4;
const byte COLS = 4;
char keys[ROWS][COLS] = {
  {'A','B','C','D'},
  {'1','2','3','4'},
  {'5','6','7','8'},
  {'9','0','#','*'}
};
byte rowPins[ROWS] = {2, 3, 4, 5};
byte colPins[COLS] = {6, 7, 8, 9};
Keypad keypad = Keypad(makeKeymap(keys), rowPins, colPins, ROWS, COLS);

// Pines LED RGB
const int pinR = 10;
const int pinG = 11;
const int pinB = 12;
```
En esta sección del código está lo Las librerías que se usaron para usar la pantalla LCD y la placa con 16 botones.
También los pines en donde van conectados con el Arduino.

En esta parte del código se puede configurar el texto. Respuestas, insultos y preguntas.
```
// Frases para cuando la máquina habla sola (nivel bajo)
const char* frasesInactivas[] = {
  "Silencio eterno",
  "¿Hay alguien?",
  "Hmm...",
  "Estoy pensando...",
  "La soledad me habla",
  "01001000 ayuda",
  "¿Botón roto?",
  "¿Y ahora qué?",
  "Esperando...",
  "Zzzz..."
};

// Insultos suaves (nivel medio)
const char* insultosSuaves[] = {
  "Patético intento",
  "Eso fue raro",
  "¿Eso crees que sirve?",
  "JA. No.",
  "Sin sentido",
  "Poco original",
  "Intenta algo nuevo"
};

// Insultos fuertes (nivel alto)
const char* insultosFuertes[] = {
  "Eres un inútil",
  "No me hagas reir",
  "¿En serio?",
  "¡Déjame en paz!",
  "Me aburres mucho",
  "¡Basta ya!",
  "No tienes remedio"
};
```
Esta parte es para ver la configuración de la historia en el código.
```
// Historia
struct Nodo {
  char linea1[17];
  char linea2[17];
  char respuestas[4];   // teclas válidas para este nodo
  int siguientes[4];    // a qué nodo lleva cada tecla
  bool esFinal;
};

Nodo historia[] = {
  {"Hola ser vivo", "¿Tienes alma?", {'A','B'}, {1,2}, false},
  {"Responde en otro", "idioma: Oui?", {'1','2'}, {3,4}, false},
  {"Zaltron dice sí", "Vruum no", {'3','4'}, {5,6}, false},
  {"Camina en luz", "o sombra?", {'A','B'}, {7,8}, false},
  {"Fin o inicio?", "Elige ya", {'A','B'}, {9,10}, false},
  {"Has vencido", "# reiniciar", {'#','#'}, {-1,-1}, true},
  {"Te apagas", "# reiniciar", {'#','#'}, {-1,-1}, true},
  {"Te espera paz", "# reiniciar", {'#','#'}, {-1,-1}, true},
  {"Más allá está", "# reiniciar", {'#','#'}, {-1,-1}, true},
  {"Sueña fuerte", "# reiniciar", {'#','#'}, {-1,-1}, true},
  {"No vuelvas", "# reiniciar", {'#','#'}, {-1,-1}, true}
};

const int totalNodos = sizeof(historia) / sizeof(historia[0]);
int nodoActual = 0;

// Contador de veces que se ha presionado cada tecla
char teclasValidas[] = {'A','B','C','D','1','2','3','4','5','6','7','8','9','0','#','*'};
int contadorTeclas[sizeof(teclasValidas)] = {0};
```
Y aquí está la configuración para insultar al presionar demasiado un botón.
```
 // Respuestas más variadas: cada vez que se presione la misma tecla, la máquina responde con diferente frase
  // Para simplificar, solo cambia la segunda línea del LCD con mensajes extra aleatorios
  if (nodoActual >= 0 && nodoActual < totalNodos) {
    lcd.clear();
    lcd.setCursor(0,0);
    lcd.print(nodo.linea1);
