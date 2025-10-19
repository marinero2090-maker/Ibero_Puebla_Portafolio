<div style="display: flex; flex-wrap: wrap; gap: 40px; justify-content: space-between;">

    <div style="flex: 1 1 45%; min-width: 300px;">
        <h3 style="color:#008080;">CÓDIGO</h3>
        
 ```cpp
const int LED=32;
const int btn=27;

void setup() {
    Serial.begin(115200);
    pinMode(LED,OUTPUT);
    pinMode(btn,INPUT);
}

void loop(){
    int estado = digitalRead(btn);
    if(estado==1){
        digitalWrite(LED,1);
    }
    else{
        digitalWrite(LED,0);
    }
}
```

<video width="300" controls>
    <source src="../Videos/Led.mp4" type="video/mp4">
    Tu navegador no soporta video.
</video>