# Unidad 6

## Bitácora de proceso de aprendizaje

### 1. Estética visual de la obra (Actividad 1)
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

---

## 2. Investigación y Prototipado (Actividad 02)

### Técnicas exploradas
* **Cuantización (Limit TOP):** Probé cómo reducir la profundidad de posición de los píxeles para generar bloques geométricos. 
* **Bucles de Retroalimentación (Feedback TOP):** Experimenté con la persistencia de la imagen para que los "errores" dejaran estelas cortas.
* **Análisis de JSON:** Investigué cómo filtrar la ráfaga de datos que envía Strudel para convertirla en señales útiles en TouchDesigner.

### Resultados
* **Lo que funcionó:** El **Limit TOP** fue clave; dio la identidad visual de "glitch" necesaria. El **Edge TOP** ayudó a mantener un look técnico resaltando contornos.
* **Ajustes:** Tuve que moderar el Feedback con un **Level TOP** para evitar que la pantalla se saturara de blanco demasiado rápido.
* **Elección final:** La combinación de **Noise + Feedback + Limit** es la que mejor representa mi concepto de "organismo digital" imperfecto.

---

## Bitácora de aplicación 

### Implementación del audio
La integración cambió radicalmente: ya no necesito correr Strudel localmente de forma obligatoria para la comunicación. Utilizo el componente **TDStrudelSync** en TouchDesigner, el cual se conecta vía WebSocket para recibir los eventos programados. 

Este componente maneja una **cola ordenada por timestamp**, lo que me permite capturar los eventos que Strudel envía al futuro y disparar las visuales en el momento exacto en que el sonido sale por los parlantes.

### Instrucciones para reproducción
1. Abrir el proyecto en **TouchDesigner**.
2. Configurar el componente **TDStrudelSync** para apuntar a la URL de Strudel.
3. Ejecutar el código de audio en el navegador (Strudel.cc).
4. El componente en TD detectará automáticamente los eventos `/dirt/play` y disparará los triggers visuales sincronizados.

## Bitácora de reflexión

### Evaluación de coherencia
Gracias a **TDStrudelSync**, la coherencia es total. Ahora la reacción visual es instantánea y precisa, cumpliendo mi concepto de un "organismo digital" que falla en sincronía perfecta.

### Desafíos de integración
El mayor reto de esta unidad fue lograr que el audio de Strudel y las visuales de TouchDesigner fueran realmente de la mano. Al principio intenté usar el mismo método de las unidades pasadas, corriendo el servidor de Strudel de forma local, pero era un proceso muy pesado y largo. Gracias al componente TDStrudelSync que nos dio el profesor, la sincronización se volvió muchísimo más sencilla y precisa. Esto me permitió dejar de pelear con la conexión técnica y concentrarme en lo importante: diseñar cómo cada sonido de Strudel transforma las visuales para que la experiencia sea realmente impactante.

### Arquitectura del Sistema Final (V2)
