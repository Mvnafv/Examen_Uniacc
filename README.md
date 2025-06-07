# Juego RPG con Frases.
Trabajo de Universidad.
> [!NOTE]
> _Este es un trabajo aun en proceso para la universidad, Clase Electronica_

**Materiales**
>-Pantalla LCD 16x2px.

>-Matriz 4x4 (16 Bótones).

>-Arduino Uno(USB).

>-Cables de coneccion.

## "En busca de Aquella Frase"
>-Juego basado en las desiciones de la persona que tiene los botones.

### Esquema Del Circuito.

![Imagen](./Imagenes/Esquema_page-0001.jpg)

### Foto del Circuito Armado

![Imagen](./Imagenes/Circuito.png)
Codigo
```
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <Keypad.h>

LiquidCrystal_I2C lcd(0x27, 16, 2); // Dirección del LCD y tamaño

// Teclado 4x4
const byte ROWS = 4;
const byte COLS = 4;
char keys[ROWS][COLS] = {
  {'D','F','G','H'},
  {'9','A','B','C'},
  {'5','6','7','8'},
  {'1','2','3','4'}
};
byte rowPins[ROWS] = {2, 3, 4, 5}; 
byte colPins[COLS] = {6, 7, 8, 9};

Keypad keypad = Keypad(makeKeymap(keys), rowPins, colPins, ROWS, COLS);

// Frases por botón — parte 1 y parte 2
String frasesParte1[16] = {
  "Eres una luz",         // '1'
  "Sigue tu camino",      // '2'
  "Nunca te rindas",      // '3'
  "Tu puedes lograrlo",   // '4'
  "Cree en tu fuerza",    // '5'
  "Ama sin medida",       // '6'
  "Eres valiente",        // '7'
  "Hazlo con amor",       // '8'
  "Brilla con fuerza",    // '9'
  "Respira profundo",     // 'A'
  "Hoy es tu dia",        // 'B'
  "Todo pasara",          // 'C'
  "Sigue adelante",       // 'D'
  "Confia en ti",         // 'E'
  "Sueña en grande",      // 'F'
  "La calma llega"        // 'G'
};

String frasesParte2[16] = {
  "en dias oscuros.",         // '1'
  "con paso firme.",          // '2'
  "sigue intentandolo.",      // '3'
  "todo es posible.",         // '4'
  "y en tu valor.",           // '5'
  "sin esperar nada.",        // '6'
  "aunque dudes.",            // '7'
  "sin miedo.",               // '8'
  "como el sol.",             // '9'
  "y sigue andando.",         // 'A'
  "hazlo tuyo.",              // 'B'
  "todo mejora.",             // 'C'
  "sin mirar atras.",         // 'D'
  "siempre lo haras.",        // 'F'
  "sin limites.",             // 'G'
  "si lo permites."           // 'H'
};

void setup() {
  lcd.init();
  lcd.backlight();
  lcd.setCursor(0, 0);
  lcd.print("Caja de frases");
  delay(2000);
  lcd.clear();
}

void loop() {
  char tecla = keypad.getKey();
  if (tecla) {
    int index = keyToIndex(tecla);
    if (index != -1) {
      mostrarFrase(frasesParte1[index], frasesParte2[index]);
    }
  }
}

int keyToIndex(char key) {
  switch (key) {
    case '1': return 0;
    case '2': return 1;
    case '3': return 2;
    case 'A': return 3;
    case '4': return 4;
    case '5': return 5;
    case '6': return 6;
    case 'B': return 7;
    case '7': return 8;
    case '8': return 9;
    case '9': return 10;
    case 'C': return 11;
    case '*': return 12;
    case '0': return 13;
    case '#': return 14;
    case 'D': return 15;
    default: return -1;
  }
}

void mostrarFrase(String linea1, String linea2) {
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print(linea1);
  delay(2500);

  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print(linea2);
  delay(2500);

  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Presiona otro");
  lcd.setCursor(0, 1);
  lcd.print("boton :)");
}
```
