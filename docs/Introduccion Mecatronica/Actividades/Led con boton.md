<div style="display: flex; flex-wrap: wrap; gap: 40px; justify-content: space-between;">

    <div style="flex: 1 1 45%; min-width: 300px;">
        <h3 style="color:#008080;">CÓDIGO</h3>
    
```cpp
const int LED = 33; // Define el pin del LED (o el pin que uses, como el 33)

void setup() {
  Serial.begin(115200); // Inicia la comunicación serie
  pinMode(LED, OUTPUT); // Configura el pin del LED como salida
}

void loop() {
  // 1. Enciende el LED
  digitalWrite(LED, 1); 
  delay(1000); // Espera 1 segundo (1000 milisegundos)
  
  // 2. Apaga el LED
  digitalWrite(LED, 0); 
  delay(1000); // Espera 1 segundo
}
```
    
</div>

<div style="flex: 1 1 45%; min-width: 300px;">
        <h3 style="color:#008080;">FUNCIÓN</h3>
        <p>El LED se enciende solo mientras el botón esté presionado y se apaga inmediatamente cuando se suelta.</p>
</div>


