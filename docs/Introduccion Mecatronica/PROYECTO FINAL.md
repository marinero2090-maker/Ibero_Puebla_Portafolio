#PROYECTO FINAL

Para el proyecto final se construyó una plataforma móvil diseñada para mantener una pelota equilibrada en el centro. Para lograrlo, se emplearon actuadores lineales que ajustan la posición de la superficie. El sistema reacciona de manera automática a cualquier movimiento de la pelota y combina principios de electrónica, programación y mecánica para formar un mecanismo de control plenamente operativo.

<div style="flex: 1 1 45%; min-width: 300px;">
        <h3 style="color:#008080;">MATERIALES</h3>
        Materiales
        MDF
        Filamento
        ESP32
        Puentes H
        Actuadores lineales
        Pintura en aerosol
</div>
<div style="flex: 1 1 45%; min-width: 300px;">
        <h3 style="color:#008080;">METODOS DE CONSTRUCCION</h3>
        Métodos de construcción
        1.impresion a 3D
        2.Cortadora laser
</div>
# CODIGOS
```cpp
import cv2                 # Librería para la cámara, ventanas y procesamiento de imagen
import numpy as np        # Usada aquí para crear rangos HSV
import serial             # Para comunicación serial con Arduino
import time               # Para medir tiempos y hacer delays precisos

# ----------------------------------------------------------------------------------
# ---------------------------- CONFIGURACIÓN GENERAL --------------------------------
# ----------------------------------------------------------------------------------

PUERTO = 'COM4'    # Puerto serial donde está conectado el Arduino
BAUD   = 115200    # Velocidad de comunicación

VELOCIDAD_BAJADA = 150  # Velocidad fija para bajar plataforma en la fase de calibración

# Intento de conectarse al Arduino
try:
    # Se abre el puerto con el baudrate y pequeños timeouts
    ser = serial.Serial(PUERTO, BAUD, timeout=0.05, write_timeout=0)
    print(f"CONECTADO A {PUERTO} (MODO PID)")
except Exception as e:
    # Si falla, se imprime el error y se sale del programa
    print(f"ERROR: {e}")
    exit()

# Se inicializa la cámara. El segundo parámetro obliga a usar DirectShow en Windows
cap = cv2.VideoCapture(1, cv2.CAP_DSHOW)

# ----------------------------------------------------------------------------------
# ----------------------------- RANGO DE COLOR ROJO ---------------------------------
# ----------------------------------------------------------------------------------
# Estos valores corresponden al color rojo en espacio HSV.
# El rojo está dividido en dos rangos porque HSV da un salto en H (0 a 180).
bajo1 = np.array([0, 120, 70])
alto1 = np.array([10, 255, 255])

bajo2 = np.array([170, 120, 70])
alto2 = np.array([180, 255, 255])

# ----------------------------------------------------------------------------------
# -------------------------- CREACIÓN DE INTERFAZ GRÁFICA ---------------------------
# ----------------------------------------------------------------------------------

def nothing(x):
    """Función vacía necesaria para los sliders de OpenCV."""
    pass

cv2.namedWindow("CONTROL PID")
cv2.resizeWindow("CONTROL PID", 600, 600)

# ----------------------------- SLIDERS DE CONTROL PID -----------------------------

cv2.createTrackbar("Kp", "CONTROL PID", 150, 500, nothing)   # 150 -> 1.5
cv2.createTrackbar("Kd", "CONTROL PID", 300, 1000, nothing)  # 300 -> 3.0
cv2.createTrackbar("Ki", "CONTROL PID", 0, 100, nothing)     # 0 -> 0.000

# ----------------------------------------------------------------------------------
# ------------------------- INVERSIONES DE SENTIDO DE GIRO --------------------------
# ----------------------------------------------------------------------------------

pol_m1 = 1; pol_m2 = 1; pol_m3 = 1; pol_m4 = 1

def toggle_m1(v):
    global pol_m1
    pol_m1 = -1 if v==1 else 1

def toggle_m2(v):
    global pol_m2
    pol_m2 = -1 if v==1 else 1

def toggle_m3(v):
    global pol_m3
    pol_m3 = -1 if v==1 else 1

def toggle_m4(v):
    global pol_m4
    pol_m4 = -1 if v==1 else 1

cv2.createTrackbar("Inv M1 (InfIzq)", "CONTROL PID", 0, 1, toggle_m1)
cv2.createTrackbar("Inv M2 (InfDer)", "CONTROL PID", 0, 1, toggle_m2)
cv2.createTrackbar("Inv M3 (SupDer)", "CONTROL PID", 0, 1, toggle_m3)
cv2.createTrackbar("Inv M4 (SupIzq)", "CONTROL PID", 0, 1, toggle_m4)

# ==================================================================================
#                           FASE 1: CALIBRACIÓN INICIAL
# ==================================================================================

print("BAJANDO PLATAFORMA...")
start_cal = time.time()

while (time.time() - start_cal) < 15:
    ret, frame = cap.read()
    if not ret: break

    frame = cv2.resize(frame, (640, 480))

    if ser.is_open:
        msg = f"{VELOCIDAD_BAJADA},{VELOCIDAD_BAJADA},{VELOCIDAD_BAJADA},{VELOCIDAD_BAJADA}\n"
        ser.write(msg.encode())

    time_left = 15 - int(time.time() - start_cal)
    cv2.putText(frame, f"CALIBRANDO: {time_left}s", (50, 240),
                cv2.FONT_HERSHEY_SIMPLEX, 1.5, (0, 0, 255), 3)

    cv2.imshow("CONTROL PID", frame)

    if cv2.waitKey(10) & 0xFF == ord('q'):
        exit()

if ser.is_open:
    ser.write(b"0,0,0,0\n")
    time.sleep(0.2)

print("INICIANDO CONTROL PID.")

# ==================================================================================
#                              FASE 2: BUCLE PID
# ==================================================================================

last_send = 0
last_err_x = 0
last_err_y = 0
integ_x = 0
integ_y = 0
last_time_pid = time.time()

while True:
    ret, frame = cap.read()
    if not ret: break

    proc_frame = cv2.resize(frame, (320, 240))
    h, w, _ = proc_frame.shape
    cx, cy = w // 2, h // 2

    Kp = cv2.getTrackbarPos("Kp", "CONTROL PID") / 100.0
    Kd = cv2.getTrackbarPos("Kd", "CONTROL PID") / 100.0
    Ki = cv2.getTrackbarPos("Ki", "CONTROL PID") / 1000.0

    hsv = cv2.cvtColor(proc_frame, cv2.COLOR_BGR2HSV)
    mask = cv2.add(cv2.inRange(hsv, bajo1, alto1), cv2.inRange(hsv, bajo2, alto2))
    mask = cv2.erode(mask, None, iterations=1)
    mask = cv2.dilate(mask, None, iterations=2)

    cnts, _ = cv2.findContours(mask, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)

    m1=m2=m3=m4=0
    ball_found = False

    current_time = time.time()
    dt = current_time - last_time_pid
    if dt == 0: dt = 0.001
    last_time_pid = current_time

    if len(cnts) > 0:
        c = max(cnts, key=cv2.contourArea)
        ((x, y), r) = cv2.minEnclosingCircle(c)

        if r > 4:
            ball_found = True

            cv2.circle(frame, (int(x)*2, int(y)*2), int(r)*2, (0, 255, 0), 2)
            cv2.line(frame, (cx*2, cy*2), (int(x)*2, int(y)*2), (255, 0, 0), 2)

            err_x = int(x - cx)
            err_y = int(y - cy)

            d_x = (err_x - last_err_x) / dt
            integ_x = max(min(integ_x + (err_x * dt), 200), -200)
            output_x = (err_x * Kp) + (d_x * Kd) + (integ_x * Ki)
            last_err_x = err_x

            d_y = (err_y - last_err_y) / dt
            integ_y = max(min(integ_y + (err_y * dt), 200), -200)
            output_y = (err_y * Kp) + (d_y * Kd) + (integ_y * Ki)
            last_err_y = err_y

            m1 = int( -output_y + output_x )
            m2 = int( -output_y - output_x )
            m3 = int(  output_y - output_x )
            m4 = int(  output_y + output_x )

            m1 = max(min(m1, 255), -255)
            m2 = max(min(m2, 255), -255)
            m3 = max(min(m3, 255), -255)
            m4 = max(min(m4, 255), -255)

    if ser.is_open and (time.time() - last_send > 0.015):
        try:
            if not ball_found:
                msg = "0,0,0,0\n"
            else:
                msg = f"{m1*pol_m1},{m2*pol_m2},{m3*pol_m3},{m4*pol_m4}\n"

            ser.write(msg.encode())
            last_send = time.time()

            info = f"P:{Kp:.2f} D:{Kd:.2f} | OutX:{int(output_x)}"
            cv2.putText(frame, info, (10, 450),
                        cv2.FONT_HERSHEY_SIMPLEX, 0.7, (0, 255, 255), 2)
        except:
            pass

    cv2.imshow("CONTROL PID", frame)

    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

if ser.is_open:
    ser.write(b"0,0,0,0\n")
    ser.close()

cv2.destroyAllWindows()
```


# CODIGO ARDUINO
```cpp
#include "BluetoothSerial.h"  
// Esta librería permite comunicar el ESP32 como si fuera un puerto inalámbrico.

// -----------------------------------------------------------------------------
// ----------------------------- VARIABLES GLOBALES -----------------------------
// -----------------------------------------------------------------------------

float Kp = 0.2;  
// Constante de Kp

String device_name = "ESP32_ANA";  
// Nombre que tendrá el dispositivo cuando se vea desde el Bluetooth de la computadora

// --------------------------- VALIDACIONES DEL ESP32 ---------------------------
// Estas validaciones revisan que el Bluetooth esté habilitado en la configuración
// del ESP32 antes de compilar. Si no lo está, el programa no compilará y marcará error.

#if !defined(CONFIG_BT_ENABLED) || !defined(CONFIG_BLUEDROID_ENABLED)
#error Bluetooth is not enabled! Please run `make menuconfig` and enable it
#endif

// Verifica si el perfil SPP (Serial Port Profile) está disponible
#if !defined(CONFIG_BT_SPP_ENABLED)
#error Serial Port Profile for Bluetooth is not available or not enabled. It is only available for the ESP32 chip.
#endif

// -----------------------------------------------------------------------------
// ----------------------------- OBJETOS Y STRINGS ------------------------------
// -----------------------------------------------------------------------------

BluetoothSerial SerialBT;
// Objeto principal que permite enviar/recibir datos por Bluetooth como si fuera Serial

String msj = "";       // Guardará la línea completa recibida desde Serial
String errorx = "";    // Parte del mensaje que contiene el valor en X
String errory = "";    // Parte del mensaje que contiene el valor en Y

// -----------------------------------------------------------------------------
// ---------------------------------- SETUP ------------------------------------
// -----------------------------------------------------------------------------

void setup() {
  Serial.begin(115200);  
  // Inicializa la comunicación serial por USB a 115200 baudios

  SerialBT.begin(device_name);  
  // Inicia el Bluetooth con el nombre especificado
  // Ahora el ESP32 aparecerá como "ESP32_ANA" en la lista de dispositivos BT

  // SerialBT.deleteAllBondedDevices();  
  // (Opcional) Si se descomenta, borra dispositivos emparejados anteriormente

  // Mensaje inicial que confirma que el Bluetooth está activo
  Serial.printf(
    "The device with name \"%s\" is started.\n"
    "Now you can pair it with Bluetooth!\n",
    device_name.c_str()
  );
}

// -----------------------------------------------------------------------------
// ----------------------------------- LOOP ------------------------------------
// -----------------------------------------------------------------------------

void loop() {

  // ---------------------------------------------------------------
  // Si llegan datos desde el puerto Serial USB se procesarán aquí.
  // ---------------------------------------------------------------

  if (Serial.available()) {
    // Lee hasta encontrar un salto de línea
    // Ejemplo esperado: "120,-35\n"
    msj = Serial.readStringUntil('\n');

    // Envía lo recibido también por Bluetooth
    SerialBT.println(msj);
  }

  // ---------------------------------------------------------------
  // Si llegan datos desde Bluetooth (PC o teléfono)
  // ---------------------------------------------------------------
  if (SerialBT.available()) {
    // Recibir línea completa desde Bluetooth
    msj = SerialBT.readStringUntil('\n');

    // Imprimir en consola USB
    Serial.println(msj);

    // Buscar coma que separa X e Y
    int coma = msj.indexOf(',');
    if (coma != -1) {
      errorx = msj.substring(0, coma);
      errory = msj.substring(coma + 1);

      Serial.print("Error X = ");
      Serial.println(errorx);

      Serial.print("Error Y = ");
      Serial.println(errory);
    }
  }
}
```

# EXPLICACION DEL PROCEDIMIENTO

Entre la plataforma superior y los actuadores se instalaron cuatro soportes articulados, ubicados en cada una de las esquinas. Cada soporte incorpora un cilindro rematado con una esfera que facilita tanto el giro como la rotación. Este mecanismo permite que la plataforma se incline con libertad, evitando esfuerzos mecánicos innecesarios y previniendo cualquier tipo de bloqueo en su movimiento.

Para la parte inferior y que aseguraran la correcta instalación de los motores en la estructura, se diseñaron e imprimieron en 3D cuatro soportes específicos para motores lineales, creados en SolidWorks. Estos soportes permiten mantener una alineación adecuada de los actuadores y aseguran que el movimiento se transfiera a la plataforma con precisión.

Estos son los modelos de los soportes que hicimos para las plataformas:

<div style="display: flex; flex-wrap: wrap; gap: 40px; justify-content: space-between;">

<div style="flex: 1 1 45%; min-width: 300px;">
<h3 style="color:#008080;">UNION ESFERICA</h3>
<img src="../Imagenes/SOPORTE.png" width="400">
</div>

<div style="flex: 1 1 45%; min-width: 300px;">
<h3 style="color:#008080;">SOPORTE INFERIOR</h3>
<img src="../Imagenes/PARTE%20UNFERIOIR.png" width="400">
</div>

</div>

---

La estructura central del proyecto se formó utilizando dos placas de MDF de 40×40 cm. La primera actúa como base, sirviendo para montar los motores lineales y los elementos electrónicos. La segunda constituye la plataforma superior, donde se sitúa la pelota. En conjunto, ambas piezas aportan la rigidez y estabilidad necesarias para el funcionamiento del sistema.

Este es el diseño de nuestra plataforma para cortarla por láser:

<div style="display: flex; flex-wrap: wrap; gap: 40px; justify-content: space-between;">

<div style="flex: 1 1 45%; min-width: 300px;">
<h3 style="color:#008080;">Plataforma superior</h3>
<img src="../Imagenes/PLATAFORMA.png" width="400">
</div>

<div style="flex: 1 1 45%; min-width: 300px;">
<h3 style="color:#008080;">Plataforma inferior</h3>
<img src="../Imagenes/PLATAFORMA%20INF.png" width="400">
</div>

</div>

# RESULTADOS

Esta es la versión final de cómo quedó nuestro proyecto:

<div style="display: flex; flex-wrap: wrap; gap: 40px; justify-content: space-between;">

<div style="flex: 1 1 45%; min-width: 300px;">
<h3 style="color:#008080;">EN PRÁCTICA</h3>

<video width="400" controls>
<source src="../VIDEO/Resultados.mp4" type="video/mp4">
Tu navegador no soporta video.
</video>

</div>

<div style="flex: 1 1 45%; min-width: 300px;">
<h3 style="color:#008080;">FINAL</h3>
<img src="../Imagenes/FINISH.png" width="400">
</div>

</div>



