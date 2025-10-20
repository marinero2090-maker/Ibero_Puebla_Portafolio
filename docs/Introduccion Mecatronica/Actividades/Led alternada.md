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
        <p>Este código implementa la función básica de Parpadeo (Blink), repitiendo el ciclo de encendido y apagado de un LED de forma automática a intervalos de tiempo definidos.</p>
</div>

.contenedor-columnas {
  display: flex; /* Para ponerlos lado a lado */
  gap: 40px; /* Buen espacio entre las columnas */
  padding: 20px;
  border-radius: 8px; /* Borde redondeado suave para todo el contenedor */
}

.columna-codigo {
  flex: 1; /* Ocupa el mismo espacio */
  background-color: #f7f7f9; /* Gris muy claro para el código */
  padding: 20px;
  border-left: 5px solid #007bff; /* Una barra de color sutil a la izquierda (azul, verde, etc.) */
  border-radius: 6px;
}

.columna-funcion {
  flex: 1;
  padding: 20px 0; /* Espacio vertical, sin fondo */
  border-left: 1px solid #ddd; /* Separador vertical muy fino */
  padding-left: 40px; /* Para que el texto no toque la línea */
}

<video width="300" controls>
    <source src="../Videos/Led.mp4" type="video/mp4">
    Tu navegador no soporta video.
</video>
