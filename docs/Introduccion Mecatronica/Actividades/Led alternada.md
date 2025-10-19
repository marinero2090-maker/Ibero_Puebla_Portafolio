<div style="display: flex; flex-wrap: wrap; gap: 40px; justify-content: space-between;">

    <div style="flex: 1 1 45%; min-width: 300px;">
        <h3 style="color:#008080;">CÓDIGO</h3>
        
        ```cpp
        const int LED = 33; 

        void setup() {
          Serial.begin(115200); 
          pinMode(LED, OUTPUT); 
        }

        void loop() {
          digitalWrite(LED, 1); 
          delay(1000);          
          digitalWrite(LED, 0); 
          delay(1000);         
        }
        ```
    </div>

    <div style="flex: 1 1 45%; min-width: 300px;">
        <h3 style="color:#008080;">FUNCIÓN</h3>
        <p>Este código implementa la función básica de Parpadeo (Blink), repitiendo el ciclo de encendido y apagado cada segundo.</p>
        </div>


<video width="300" controls>
    <source src="../Videos/Parpadeo.mp4" type="video/mp4">
    Tu navegador no soporta video.
</video>