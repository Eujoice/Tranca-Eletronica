#include <Keypad.h>
#include <LiquidCrystal_I2C.h>
#include <Wire.h>

LiquidCrystal_I2C lcd(0x27, 16, 2);

const byte LINHAS = 4;
const byte COLUNAS = 3;

char matriz_teclas[LINHAS][COLUNAS] = {
  {'1','2','3'},
  {'4','5','6'},
  {'7','8','9'},
  {'*','0','#'}
};

byte PinosLinhas[LINHAS] = {4, 5, 6, 7};
byte PinosColunas[COLUNAS] = {8, 9, 10};

Keypad meuteclado = Keypad(makeKeymap(matriz_teclas), PinosLinhas, PinosColunas, LINHAS, COLUNAS);

String senhaPadrao = "123";
String senhaDigitada = "";

const int pinLed = 13;
unsigned long tempoAbertura = 0;

void setup() {
  pinMode(pinLed, OUTPUT);
  digitalWrite(pinLed, LOW);

  lcd.init();
  lcd.backlight();
  lcd.setCursor(0, 0);
  lcd.print("Digite a senha:");
}

void loop() {
  char tecla = meuteclado.getKey();

  if (digitalRead(pinLed) == HIGH) {
    if (millis() - tempoAbertura >= 1000) {
      digitalWrite(pinLed, LOW);
      lcd.clear();
      lcd.print("Digite a senha:");
    }
  }

  if (tecla) {
    if (tecla == '*') {
      lcd.clear();
      if (senhaDigitada == senhaPadrao) {
        lcd.print("Acesso");
        lcd.setCursor(0, 1);
        lcd.print("autorizado");

        digitalWrite(pinLed, HIGH); 
        tempoAbertura = millis();
      } else {
        lcd.print("Acesso nao");
        lcd.setCursor(0, 1);
        lcd.print("autorizado");
        delay(2000); 
        lcd.clear();
        lcd.print("Digite a senha:");
      }
      senhaDigitada = ""; 
    } 
    else {
      if (senhaDigitada.length() < 3) {
        senhaDigitada += tecla;
        lcd.setCursor(senhaDigitada.length() - 1, 1);
        lcd.print("*");
      }
    }
  }
}
