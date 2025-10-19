<div style="display: flex; flex-wrap: wrap; gap: 40px; justify-content: space-between;">

    <div style="flex: 1 1 45%; min-width: 300px;">
        <h3 style="color:#008080;">CÓDIGO</h3>
        
        ```cpp
        const int LED=32; // Pin de Salida para el LED
const int btn=27; // Pin de Entrada para el Botón

void setup() {
  // Configuración inicial
  Serial.begin(115200);
  pinMode(LED,OUTPUT);
  pinMode(btn,INPUT); // Se configura el pin del botón como entrada
}

void loop() {
  // Lectura repetida del estado del botón
  int estado = digitalRead(btn);
  
  // Lógica de control:
  if(estado==1){ // Si el botón está presionado (estado HIGH)
    digitalWrite(LED,1); // Enciende el LED (HIGH)
  }
  else{ // Si no está presionado (estado LOW)
    digitalWrite(LED,0); // Apaga el LED (LOW)
  }
}
        ```
    </div>

    <div style="flex: 1 1 45%; min-width: 300px;">
        <h3 style="color:#008080;">FUNCIÓN</h3>
        <p>El LED se enciende solo mientras el botón esté presionado y se apaga inmediatamente cuando se suelta.</p>
        </div>


<video width="400" controls>
    <source src="../../../Videos/LED PARPADEO.mp4" type="video/mp4">
    Tu navegador no soporta video.
</video>