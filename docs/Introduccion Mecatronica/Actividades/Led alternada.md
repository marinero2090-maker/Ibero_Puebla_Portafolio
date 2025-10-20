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

<style>
/* ---------------------------------------------------- */
/* --- CONTENIDO DE TU ANTIGUO Estilos.css.md --- */
/* ---------------------------------------------------- */

.contenedor-columnas {
  display: flex;
  gap: 40px;
  padding: 20px;
  border-radius: 8px;
}

.columna-codigo {
  flex: 1; 
  background-color: #f7f7f9; /* Fondo gris */
  padding: 20px;
  border-left: 5px solid #007bff; /* Barra azul */
  border-radius: 6px;
}

.columna-funcion {
  flex: 1;
  padding: 20px 0;
  border-left: 1px solid #ddd; /* Separador sutil */
  padding-left: 40px;
}
</style>

# Led alternada

<div class="contenedor-columnas">

    <div class="columna-codigo">
        <h3>CÓDIGO</h3>
        <pre><code>
            const int LED = 13;
            // ... resto del código Arduino aquí ...
        </code></pre>
    </div>

    <div class="columna-funcion">
        <h3>FUNCIÓN</h3>
        <p>Este código implementa la función básica de Parpadeo (Blink), repitiendo el ciclo de encendido y apagado...</p>
    </div>
</div>

<div align="center">
    <video width="700" controls>
        <source src="../Videos/Led.mp4" type="video/mp4">
        Tu navegador no soporta video.
    </video>
</div>

