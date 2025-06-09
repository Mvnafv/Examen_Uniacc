# ExamenElectronicaUniacc
### Tu Vida Bajo un Botón
## Descripcion Conceptual.
Bueno, para describir mi trabajo me hice unas preguntas base conforme todo iba avanzando respecto al trabajo. Lo primero, partiendo por la base, fue ¿qué hacer con tantas piezas?, ¿cuál pieza elegir? Todo moviéndose en mi mente para llegar a una idea final para usar entre tantas piezas. Una pantalla LCD 16x2 con adaptador posterior y una placa 4x4 de 16 botones. Aquí surge: ¿Hasta dónde puedo llegar con esto?, pensando en las posibilidades para usar las piezas. Luego de elegirlas nos dieron un pequeño tiempo para investigar sobre el uso de las piezas por separado y ver cómo unirlas en la protoboard con el Arduino. Aquí me nace una pregunta pensando en casa: "¿Puedo hacer algún juego?". Fue donde pensé en un juego clásico que solía jugar llamado "El ahorcado" o "Colgado" (tiene varios nombres). Comenzando con eso, me puse a investigar para crear el código del juego y, a medida que avanzaba, tenía buenas y malas correcciones. Luego, hablando en clases y con varias dudas personales, decidí dejarlo en una caja de frases para animar el día. En esa misma clase, mi profesor me cuestionó de la siguiente manera: "¿Cómo sacas de forma artística esa caja?" ¿Cómo harás para volverla más llamativa artísticamente? Cuestionando lo que dijo, me dio un referente como tal, que esas frases se volvieran un juego al estilo Fallout (él me recomendó otro juego que no recuerdo muy bien, pero sigues la misma temática). La toma de decisiones lleva algo nuevo. ¿Que pasa si hago esto? ¿O que pasa si no hago nada? Por lo que modifiqué el código para tener un juego de frases dependiendo de cómo iba tu día, en donde respondiendo según cómo te sientes te daba respuestas y agregando que ciertos botones den con un LED RGB.

**Materiales**
>-Pantalla LCD 16x2px.

>-Matriz 4x4 (16 Bótones).

>-Arduino Uno(USB).

>-Cables de coneccion.

## Descripcion Tecnica
El funcionamiento de mi máquina se basa en las decisiones que toma el jugador dependiendo de su día. Como base, partió en un juego totalmente diferente, en donde se completaba la palabra para avanzar. Por errores y complejidades en el código se terminó cambiando para que sean frases motivacionales según cada botón. Luego de modificar el código para tal, me dieron una idea más llamativa en donde pasar las frases en respuestas dependiendo de las decisiones. Lo cual acepté porque me pareció más llamativo y donde terminé hasta ahora.

### Esquema Del Circuito.
![Imagen](./Imagenes/Esquema_page-0001.jpg)

### Foto del Circuito Armado
![Imagen](./Imagenes/Circuito.png)
Codigo
```
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <Keypad.h>

// LCD 16x2 I2C
LiquidCrystal_I2C lcd(0x27, 16, 2);

// Configuración keypad 4x4
const byte ROWS = 4;
const byte COLS = 4;
char keys[ROWS][COLS] = {
  {'A','B','C','D'},
  {'1','2','3','4'},
  {'5','6','7','8'},
  {'9','0','#','*'}
};
byte rowPins[ROWS] = {2,3,4,5};
byte colPins[COLS] = {6,7,8,9};
Keypad keypad = Keypad(makeKeymap(keys), rowPins, colPins, ROWS, COLS);

// Pines LED RGB (cambia según tu conexión)
const int pinR = 10;
const int pinG = 11;
const int pinB = 12;

// Estructura para nodo
struct Nodo {
  char linea1[17];
  char linea2[17];
  int nodoSi;
  int nodoNo;
  bool esFinal;
};

// Historia con 20 nodos, todos con línea <16 chars y bien enlazados
Nodo historia[] = {
  {"Hola, despiertas",    "Te sientes bien?", 1, 2, false},    //0
  {"Tu dia es claro",     "Quieres seguir?", 3, 4, false},    //1
  {"Dia nublado",         "Quieres parar?", 5, 6, false},    //2

  {"Sigue adelante",      "Siente paz",       7, 8, false},    //3
  {"Busca alegria",       "Intenta mas",      9, 10, false},   //4

  {"Descansa hoy",        "Habla contigo",    11, 12, false},  //5
  {"Espera y ve",         "Piensa bien",      13, 14, false},  //6

  {"Respira hondo",       "Sonrie mas",       15, 16, false},  //7
  {"Lucha un poco",       "No te rindas",     17, 18, false},  //8

  {"Encuentra paz",       "Renace luz",       19, 19, true},   //9 final
  {"Nunca pares",         "# para reiniciar", -1, -1, true},   //10 final

  {"Habla claro",         "Escucha bien",     19, 19, true},   //11 final
  {"Medita ya",           "# para reiniciar", -1, -1, true},   //12 final

  {"Piensa en ti",        "# para reiniciar", -1, -1, true},   //13 final
  {"Sigue fuerte",        "# para reiniciar", -1, -1, true},   //14 final

  {"Sonrie hoy",          "# para reiniciar", -1, -1, true},   //15 final
  {"Piensa en luz",       "# para reiniciar", -1, -1, true},   //16 final

  {"Lucha hoy",           "# para reiniciar", -1, -1, true},   //17 final
  {"Nunca cedas",         "# para reiniciar", -1, -1, true},   //18 final

  {"Paz interior",        "# para reiniciar", -1, -1, true}    //19 final
};

const int totalNodos = sizeof(historia)/sizeof(historia[0]);
int nodoActual = 0;

// Función para mostrar el nodo actual
void mostrarNodo(int index) {
  lcd.clear();
  if (index < 0 || index >= totalNodos) {
    lcd.print("Nodo invalido");
    return;
  }
  lcd.setCursor(0,0);
  lcd.print(historia[index].linea1);
  lcd.setCursor(0,1);
  lcd.print(historia[index].linea2);
}

// Función para prender LED RGB según botón
void prenderColor(char tecla) {
  switch(tecla) {
    case '1': // Rojo
      analogWrite(pinR, 255);
      analogWrite(pinG, 0);
      analogWrite(pinB, 0);
      break;
    case '2': // Verde
      analogWrite(pinR, 0);
      analogWrite(pinG, 255);
      analogWrite(pinB, 0);
      break;
    case '3': // Azul
      analogWrite(pinR, 0);
      analogWrite(pinG, 0);
      analogWrite(pinB, 255);
      break;
    case '4': // Amarillo
      analogWrite(pinR, 255);
      analogWrite(pinG, 255);
      analogWrite(pinB, 0);
      break;
    case '5': // Cyan
      analogWrite(pinR, 0);
      analogWrite(pinG, 255);
      analogWrite(pinB, 255);
      break;
    case '6': // Magenta
      analogWrite(pinR, 255);
      analogWrite(pinG, 0);
      analogWrite(pinB, 255);
      break;
    case '7': // Blanco
      analogWrite(pinR, 255);
      analogWrite(pinG, 255);
      analogWrite(pinB, 255);
      break;
    case '8': // Apaga LED
      analogWrite(pinR, 0);
      analogWrite(pinG, 0);
      analogWrite(pinB, 0);
      break;
    default:
      break;
  }
}

void setup() {
  lcd.init();
  lcd.backlight();

  pinMode(pinR, OUTPUT);
  pinMode(pinG, OUTPUT);
  pinMode(pinB, OUTPUT);
  analogWrite(pinR, 0);
  analogWrite(pinG, 0);
  analogWrite(pinB, 0);

  mostrarNodo(nodoActual);
}

void loop() {
  char tecla = keypad.getKey();
  if (tecla) {
    if (tecla == '#') {
      nodoActual = 0;
      mostrarNodo(nodoActual);
    }
    else if (tecla == 'A') {
      int siguiente = historia[nodoActual].nodoSi;
      if (siguiente == -1) {
        lcd.clear();
        lcd.print("Fin final");
      } else {
        nodoActual = siguiente;
        mostrarNodo(nodoActual);
      }
    }
    else if (tecla == 'B') {
      int siguiente = historia[nodoActual].nodoNo;
      if (siguiente == -1) {
        lcd.clear();
        lcd.print("Fin final");
      } else {
        nodoActual = siguiente;
        mostrarNodo(nodoActual);
      }
    }
    else {
      // Otros botones para LED RGB
      prenderColor(tecla);
    }
  }
}
```
