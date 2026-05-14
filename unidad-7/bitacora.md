# Unidad 7

## Bitácora de proceso de aprendizaje

### ¿Qué parámetros de la obra se van a controlar en tiempo real? (parámetros de audio, de visuales, o ambos)**
Se controlarán parámetros de ambos mundos para lograr una sinestesia completa:
* **Audio (Strudel):** Control de densidades armónicas, frecuencias de sintetizadores y ritmos generativos (específicamente la interacción entre el Kick, el Bajo y el Clarinete).
* **Visuales (TouchDesigner):** La resolución y degradación de la imagen a través del parámetro *Position Step* (Limit TOP) y la reactividad del ruido (Noise TOP) basada en el análisis de frecuencias de los instrumentos.

### ¿Quién controla qué? (¿Qué controla el artista/performer y qué controla el público?)**
**Artista (Yo, Juan José Hoyos Peláez):** Como performer, controlo la ejecución del código en Strudel, determinando la estructura musical, los cambios de sección y la base de la composición visual reactiva.
**Público:** Tiene el control sobre la **textura y fidelidad visual**. Mediante su interacción, pueden decidir si la obra se ve nítida o si se fragmenta en bloques digitales (efecto de pixelación), interviniendo directamente en la estética final de la pieza.

### ¿Cómo participará el público? 
**Mecanismo:** El público participará a través de sus celulares.
**Interacción:** Escanearán un código QR para acceder a una interfaz web personalizada.
**Acción:** Deslizarán un **fader vertical** en su pantalla.
**Datos:** El dispositivo enviará valores normalizados (de 0 a 1) mediante el protocolo **OSC**, que luego son mapeados en TouchDesigner para alterar el procesamiento de imagen en tiempo real.

### ¿Qué dispositivos se usarán? 
**Servidor de Interfaz:** Se utiliza **Open Stage Control** para alojar la interfaz web y gestionar el envío de datos.
**Hardware del público:** Celulares de los asistentes conectados a la red local.
**Recepción de datos:** Un nodo **OSC In CHOP** en TouchDesigner configurado en el puerto 9000, procesado a través de nodos *Math* y *Lag* para asegurar una respuesta fluida y estética.

---

### Actividad 02: Implementación de subsistemas de control

**1. Proceso de implementación:**
**Superficie de control (Artista):** Se diseñó una interfaz en **Open Stage Control** para gestionar parámetros críticos de la síntesis en Strudel y la mezcla en TouchDesigner. Se priorizó un diseño minimalista sobre fondo oscuro para reducir la fatiga visual durante el performance.
**Participación del público:** Se implementó un **fader vertical** accesible vía web. La comunicación se estableció mediante el protocolo OSC, enviando datos desde los smartphones hacia el puerto 9000 de TouchDesigner.

**2. Pruebas de funcionamiento independiente:**
- Se verificó la llegada de paquetes OSC mediante el nodo *OSC In CHOP*, confirmando que el fader del celular entrega valores normalizados (0.0 a 1.0) sin pérdida notable de paquetes.
- Se probó la estabilidad de la red local (Hotspot) para asegurar que el servidor de Open Stage Control fuera visible desde dispositivos externos.

**3. Problemas encontrados y soluciones:**
**Conflicto de Puertos:** Inicialmente, TouchDesigner y el servidor web de Open Stage Control intentaban usar el puerto 8086. **Solución:** Se reconfiguró el puerto de datos OSC a **9000** en ambos sistemas, dejando el 8086 exclusivamente para la interfaz web.
**Bloqueo de Firewall:** El celular no lograba cargar la interfaz. **Solución:** Se habilitaron reglas de entrada en el Firewall de Windows para Open Stage Control y se utilizó el Hotspot del celular para saltar el aislamiento de clientes de la red universitaria.

---

## Bitácora de aplicación 

### Actividad 03: Integración de los componentes

**1. Proceso de integración:**
Se conectó la salida de audio de Strudel a TouchDesigner mediante una tarjeta de sonido virtual para el análisis de espectro. Los datos del público (fader) se mapearon al parámetro *Position Step* del nodo *Limit TOP* para controlar la pixelación de los visuales rítmicos generados.

**2. Pruebas con público simulado:**
Se realizaron pruebas con compañeros de clase. El "público" interactuó con el visual mientras yo ejecutaba el código musical. Se observó una respuesta inmediata entre el movimiento del dedo y la fragmentación de la imagen en pantalla.

**3. Problemas de usabilidad y latencia:**
**Latencia visual:** El cambio de pixelación era muy brusco. **Solución:** Se insertó un nodo *Lag CHOP* con un valor de 0.2 para suavizar la transición.
**Sensibilidad:** Valores de pixelación cercanos a 1.0 hacían desaparecer la imagen. **Solución:** Se utilizó un *Math CHOP* para limitar el rango de salida (To Range: 0.0 - 0.2).

**4. Instrucciones para reproducir la obra:**
1.  Ejecutar el servidor de **Open Stage Control** cargando el archivo de sesión `.json`.
2.  Abrir el archivo de **TouchDesigner** y verificar que el nodo *OSC In* esté en el puerto 9000.
3.  Abrir **Strudel.cc**, cargar el script musical y activar el audio.
4.  Escanear el código QR con el celular y deslizar el fader para intervenir la visual.

---

## Bitácora de reflexión

## Reflect: Ensayo general y preparación 🤔

### Actividad 04: Reflexión y Ajustes

**1. Resultados del ensayo completo:**
**Funcionó:** La sincronía entre el bajo y el movimiento de la forma base es perfecta. La pixelación controlada por el público añade una capa de "glitch" que encaja con la estética Hoyos/Holes.
**Falló:** Al inicio, el clarinete saturaba demasiado la ganancia armónica de los visuales.
**Ajuste:** Se refinó el rango del *Math CHOP* conectado al clarinete para que el efecto fuera más sutil y no "quemara" la imagen.

**2. Diagrama de sistema final:**
El sistema fluye desde **Strudel (Audio)** -> **TouchDesigner (Visuales)** <- **Open Stage Control (Público)**. Toda la mezcla final se visualiza en un *Composite TOP* que integra todas las capas.
