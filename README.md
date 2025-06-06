# 🌊 Alerta de Alagamentos com Arduino

## 🚨 Descrição do Problema

Nas cidades do **Rio Grande do Sul**, enchentes e alagamentos decorrentes de chuvas intensas têm causado sérios transtornos:
- ⚠️ Riscos à segurança de moradores e transeuntes  
- 🏚️ Danos a residências, comércios e infraestrutura  
- ⏱️ Dificuldade na tomada de decisão em tempo hábil  

❌ Em muitas regiões, **não há um sistema de aviso em tempo real** sobre níveis críticos de água ou umidade.

---

## 💡 Visão Geral da Solução

Este projeto utiliza um **Arduino** para monitorar:
1. 📏 **Nível de água** (sensor ultrassônico **HC-SR04**)  
2. 🌡️ **Umidade do ar e temperatura** (sensor **DHT22**)  

Quando detecta condições de perigo:
- 🟢 **Acende LED verde** (alerta moderado)  
- 🔴 **Acende LED vermelho + ativa buzzer** (alerta crítico)  
- 📟 **Exibe valores em tempo real** no display **LCD I2C**  

---

### 🧰 Componentes Necessários

- 1× 🧠 Arduino Uno (ou compatível)  
- 1× 📏 Sensor ultrassônico HC-SR04  
- 1× 🌡️ Sensor DHT22 (umidade/temperatura)  
- 1× 📟 Display LCD 16×2 com interface I2C  
- 1× 🟢 LED verde  
- 1× 🔴 LED vermelho  
- 1× 🔊 Buzzer piezoelétrico  
- 🔌 Jumpers e protoboard  

---

### 🔌 Diagrama de Ligação

![Diagrama de Ligação](figuras/diagrama_circuito.png)

📌 Conexões:
- HC-SR04: `TRIG → pino 9`, `ECHO → pino 10`  
- DHT22 → `pino digital 2`  
- LCD I2C → `SDA (A4)`, `SCL (A5)`  
- LED verde → `pino 3` (com resistor de 220 Ω)  
- LED vermelho → `pino 4` (com resistor de 220 Ω)  
- Buzzer → `pino 5`  

---

### 📚 Bibliotecas Utilizadas

- `Wire.h`  
- `LiquidCrystal_I2C.h`  
- `DHT.h`  

🔧 Instale-as via **Library Manager** da Arduino IDE antes de compilar.

---

## 🔗 Links Úteis

- 🔌 **Projeto no Wokwi:** [COLE AQUI O LINK DO SEU PROJETO](https://wokwi.com/projects/432973600071955457)  
- 🎥 **Vídeo Demonstrativo:** [COLE AQUI O LINK DO VÍDEO](https://youtube.com/SEU_LINK_AQUI)  

---

## 🧪 Guia de Simulação no Wokwi

1. Acesse o [Wokwi](https://wokwi.com/) e faça login (ou crie uma conta grátis).  
2. Crie um novo projeto **Arduino Uno**.  
3. No painel esquerdo, adicione:  
   - 📏 HC-SR04  
   - 🌡️ DHT22  
   - 📟 LCD I2C 16×2  
   - 🟢 LED verde e 🔴 LED vermelho  
   - 🔊 Buzzer  
4. Monte o circuito conforme o **Diagrama de Ligação**.  
5. Copie e cole o código abaixo no editor do Wokwi.  
6. ▶️ Clique em **Start Simulation** e ajuste manualmente a distância do sensor ultrassônico para testar os níveis de alerta.

---

```cpp
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <DHT.h>

#define DHTPIN 2
#define DHTTYPE DHT22
DHT dht(DHTPIN, DHTTYPE);

LiquidCrystal_I2C lcd(0x27, 16, 2);

#define TRIG_PIN 9
#define ECHO_PIN 10
#define LED_VERDE 3
#define LED_VERMELHO 4
#define BUZZER 5

void setup() {
  Serial.begin(9600);
  dht.begin();
  lcd.init();
  lcd.backlight();
  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);
  pinMode(LED_VERDE, OUTPUT);
  pinMode(LED_VERMELHO, OUTPUT);
  pinMode(BUZZER, OUTPUT);
  lcd.setCursor(0, 0);
  lcd.print("Sistema Iniciado");
  delay(2000);
  lcd.clear();
}

void loop() {
  float umidade = dht.readHumidity();
  float temperatura = dht.readTemperature();

  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);

  long duracao = pulseIn(ECHO_PIN, HIGH);
  float distancia = duracao * 0.034 / 2;

  lcd.setCursor(0, 0);
  lcd.print("U:");
  lcd.print(umidade);
  lcd.print("% T:");
  lcd.print(temperatura);

  lcd.setCursor(0, 1);
  lcd.print("Dist:");
  lcd.print(distancia);
  lcd.print(" cm   ");

  if (distancia < 10) {
    digitalWrite(LED_VERDE, LOW);
    digitalWrite(LED_VERMELHO, HIGH);
    digitalWrite(BUZZER, HIGH);
  } else if (distancia < 20) {
    digitalWrite(LED_VERDE, HIGH);
    digitalWrite(LED_VERMELHO, LOW);
    digitalWrite(BUZZER, LOW);
  } else {
    digitalWrite(LED_VERDE, LOW);
    digitalWrite(LED_VERMELHO, LOW);
    digitalWrite(BUZZER, LOW);
  }

  delay(2000);
}
