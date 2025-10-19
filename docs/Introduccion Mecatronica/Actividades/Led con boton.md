
<div style="display: flex; flex-wrap: wrap; gap: 40px; justify-content: space-between;">

    <div style="flex: 1 1 45%; min-width: 300px;">
        <h3 style="color:#008080;">CÓDIGO</h3>
        

```cpp
const int LED=32;
const int btn=27;

void setup() {
  // put your setup code here, to run once:
  Serial.begin(115200);
  pinMode(LED,OUTPUT);
  pinMode(btn,INPUT);
}

void loop() {
  // put your main code here, to run repeatedly:
  int estado = digitalRead(btn);
  if(estado==1){
    digitalWrite(LED,1);
  }
  else{
    digitalWrite(LED,0);
  }
}
```

</div>

<div style="flex: 1 1 45%; min-width: 300px;">
        <h3 style="color:#008080;">FUNCIÓN</h3>
        <p>Este código implementa la función de Interruptor Digital, controlando el encendido y apagado de un LED al leer continuamente el estado de un botón conectado a la placa.</p>
        </div>
</div>