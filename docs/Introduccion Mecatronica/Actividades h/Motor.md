<div style="display: flex; flex-wrap: wrap; gap: 40px; justify-content: space-between;">

    <div style="flex: 1 1 45%; min-width: 300px;">
        <h3 style="color:#008080;">CÓDIGO</h3>

```cpp        
#define in1 18
#define in2 19

void setup() {
  // put your setup code here, to run once:
  pinMode(in1, OUTPUT);
  pinMode(in2, OUTPUT);
}

void loop() {
  // put your main code here, to run repeatedly:
  /*
  1. enciende en una direccion 3 segs
  2. para
  3. enciende en direccion opuesta
  */
  
  // Encender en una direccion (in1 HIGH, in2 LOW)
  digitalWrite(in1, 1);
  digitalWrite(in2, 0); 
  delay(3000); // 3 segundos

  // Apagar/Parar (ambos LOW)
  digitalWrite(in1, 0); 
  digitalWrite(in2, 0); 
  delay(1000); // 1 segundo

  // Encender en direccion opuesta (in1 LOW, in2 HIGH)
  digitalWrite(in1, 0); 
  digitalWrite(in2, 1);
  delay(3000); // 3 segundos

  // Apagar/Parar de nuevo (ambos LOW)
  digitalWrite(in1, 0);
  digitalWrite(in2, 0);
  delay(1000); // 1 segundo
}
```

</div>

<div style="flex: 1 1 45%; min-width: 300px;">
        <h3 style="color:#008080;">FUNCIÓN</h3>
        <p>Este código implementa un ciclo de movimiento básico para un motor DC (o actuador). Hace que el motor gire en una dirección y luego gire en la dirección opuesta, con una breve parada entre cada cambio de sentido.</p>
        </div>
</div>