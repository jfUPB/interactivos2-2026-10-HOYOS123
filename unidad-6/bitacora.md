# Unidad 6

## Bitácora de proceso de aprendizaje

### 1. Estética visual de la obra
Las visuales de mi obra se basan en **arte generativo abstracto** con una fuerte carga de **estética glitch**. Utilizo generadores de ruido (Noise) que proceso para obtener texturas que parecen "líquidas" pero digitalizadas. La idea es que la imagen se sienta como una señal de video fallando o una computadora procesando datos de forma errática, usando bloques pixelados y bordes muy marcados.

### 2. Paleta de colores
He decidido usar una paleta **monocromática (Blanco y Negro)**. 
* **Por qué:** Esta decisión se inspira en el minimalismo de Ryoji Ikeda. Al quitar el color, obligo al espectador a centrarse en el ritmo, el contraste y la forma. Además, el blanco y negro refuerza la sensación de "dato puro" y de sistema informático crudo, lo cual encaja perfectamente con el concepto de error digital.

### 3. Mapeo: ¿Cómo reacciona el video al audio?
La conexión entre Strudel y TouchDesigner se dará mediante el WebSocket (puerto 8081). El mapeo principal será:
* **Bombo (pulse/bd) → Contraste y Brillo:** Cada vez que suene el pulso grave, la pantalla tendrá un "flash" o un salto fuerte en el contraste.
* **Armonía (clarinete/saw) → Complejidad del Ruido:** Las notas más agudas o los cambios en la melodía modificarán la semilla (seed) o la escala del ruido, haciendo que las formas se vuelvan más complejas o simples.
* **Bajo (bass) → Pixelación (Quantize):** Las frecuencias bajas controlarán qué tan "pixelada" se ve la imagen a través del operador Limit, haciendo que el glitch sea más agresivo cuando el bajo tiene más presencia.

### 4. Coherencia con el concepto
Estas decisiones sirven al concepto porque buscan que la obra se sienta como un organismo digital vivo pero imperfecto. Al sincronizar los "errores" visuales con los instrumentos de Strudel, logro que el público no solo escuche la música, sino que vea cómo el sonido "rompe" la imagen en tiempo real, creando esa experiencia de co-creación efímera que planteé desde el inicio.

[Ver video en YouTube](https://youtu.be/CXrWt6s8i6E)

## Bitácora de aplicación 


## Bitácora de reflexión
