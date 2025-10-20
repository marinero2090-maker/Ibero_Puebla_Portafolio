<div style="display: flex; flex-wrap: wrap; gap: 40px; justify-content: space-between;">

    <div style="flex: 1 1 45%; min-width: 300px;">
        <h3 style="color:#008080;">CÓDIGO</h3>

```cpp
#include "BluetoothSerial.h"

BluetoothSerial SerialBT; 
const int LED = 14; 
const int On = 1;
const int Off = 0;

void setup() {
  Serial.begin(115200); 
  SerialBT.begin("Adrian"); 
  Serial.println("El dispositivo Bluetooth esta listo para emparejarse.");
  pinMode(LED, OUTPUT); 
}

void loop() {
  if (SerialBT.available()) { 
    String mensaje = SerialBT.readStringUntil('\n');
    mensaje.trim(); 
    Serial.print("Recibido: ");
    Serial.println(mensaje);

    int comando = mensaje.toInt(); 
    
    if (comando == On) {
      digitalWrite(LED, HIGH); 
      Serial.println("LED Encendido (HIGH)"); 
      
    } else if (comando == Off) {
      digitalWrite(LED, LOW); 
      Serial.println("LED Apagado (LOW)"); 
      
    } else {
      Serial.println("Comando no reconocido.");
    }
  }
  
  if (Serial.available()) {
    SerialBT.write(Serial.read());
  }
}
```
</div>

<div style="flex: 1 1 45%; min-width: 300px;">
        <h3 style="color:#008080;">FUNCIÓN</h3>
        <p>Su función es recibir comandos numéricos desde la conexión Bluetooth para controlar el estado de un LED conectado al Pin 14.</p>
</div>