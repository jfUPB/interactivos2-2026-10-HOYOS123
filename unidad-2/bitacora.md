# Unidad 2

## Bitácora de proceso de aprendizaje
### Actividad 1:

Durante el desarrollo de los pasos del caso de estudio, me pasaron algunas dificultades debido a las restricciones de los computadores de la universidad. Estas limitaciones me obligaron a modificar ciertos comandos que normalmente se usarían en Git Bash, con el fin de poder continuar el proceso sin errores.

Por ejemplo, no fue posible instalar pnpm directamente como se indicaba en el repositorio del profesor. Por esta razón, tuve que usar el siguiente comando:
```
npm install -g pnpm@latest-10
```

También intenté instalarlo usando este otro método:
```
npx install pnpm
```

Sin embargo, este comando no funcionó correctamente en el entorno restringido. Finalmente, logré resolver el problema utilizando el siguiente comando:
```
npx pnpm i
```

Este sí funcionó y me permitió completar exitosamente la instalación de pnpm.

De manera similar, para poder ejecutar el servidor en el entorno REPL, fue necesario utilizar nuevamente npx, ya que no era posible hacerlo de otra forma. El comando utilizado fue:
```
npx pnpm dev
```

Estos ajustes me permitieron continuar sin más problemas.

### Actividad 2 

La sincronización audio-visual se logra mediante la ejecución simultánea de tres componentes: Eñ REPL de Strudel como generador de audio y controlador temporal, el oscBridge como intermediario que transmite los eventos sonoros, y los visuales desarrollados en p5.js. El bridge permite que los datos del audio se envíen en tiempo real a los visuales, los cuales reaccionan de forma inmediata al pulso y a los cambios rítmicos, evidenciando una relación directa entre sonido y animación.

Los visuales no funcionan de manera autónoma, sino que dependen de los eventos enviados desde el sistema de audio.

También aprendí que puedo tener varias visuales activas al mismo tiempo. Esto es porque todas pueden recibir simultáneamente los datos enviados por el oscBridge, se vuelve un flujo de información compartida. Aunque, cada visual interpreta los datos de manera distinta, lo que permite que varias animaciones, distintas entre sí, reaccionen al mismo audio y pulso.

#### Evidencia en video de lo realizado:  
[Ver video en YouTube](https://youtu.be/mkfk2rZK0xM)

### Actividad 3

#### Descripción de la modificación realizada
Realicé una modificación al caso de estudio original incorporando una nueva capa sonora grave que no estaba presente inicialmente. Al patrón rítmico base, construido con sonidos de la banca **TR-909**, añadí una línea de bajo independiente generada a partir de un sintetizador tipo **GM synth bass**.  

Esta línea de bajo utiliza una secuencia de notas graves y se reproduce a una velocidad mucho más lenta que la percusión.

#### Relación entre audio y visuales reactivas
La inclusión del bajo cambia la forma en que el sonido y las visuales se relacionan, ya que agrega una capa sonora diferente al ritmo principal. Mientras la percusión marca el pulso y los golpes rápidos, el bajo se mueve de manera más lenta y constante.

#### Evidencia
- Capturas:
<img width="1919" height="1032" alt="image" src="https://github.com/user-attachments/assets/002ea7d8-6e83-4b99-a0f2-e3e8e5f15fee" />

- Evidencia en video de lo realizado:  
[Ver video en YouTube](https://youtu.be/1Ji4ibCflxk)


### Código utilizado
```
setcps(0.5)

const pat = s("[bd*2 sd hh oh]").bank("tr909")

bass: note("d#2 e2 g2 c2").slow(8)
    .sound("gm_synth_bass_1")
    .gain(.2)
    ._punchcard()

$: stack(
  pat
    .gain('0.5')
    .every(4, fast(2)),
  pat
    .osc()
    .every(6, slow(2)),
)
```





---

## Bitácora de aplicación

### Actividad 4

#### Mejora de pieza de audio interactivo con visuales reactivas

---

### Proceso de mejora

En la unidad anterior desarrollé una pieza de audio interactivo en
Strudel compuesta por cuatro capas:

-   Armonía con clarinet y supersaw
-   Bajo eléctrico
-   Percusión (bd + hh)

Para esta nueva fase, el objetivo fue incorporar **visuales reactivas
sincronizadas** con los eventos sonoros. La mejora consistió en:

-   Enviar eventos rítmicos desde Strudel mediante OSC.
-   Utilizar un bridge (oscBridge) para comunicar Strudel con p5.js.
-   Programar visuales que reaccionan en tiempo real a:
    -   Golpe de bombo
    -   Bajo
    -   Armonía
    -   Hi-hat

------------------------------------------------------------------------

## Implementación de la sincronización

La sincronización se implementó usando:

-   **Strudel → OSC**
-   **oscBridge (Node.js)**
-   **p5.js para visuales**

### Flujo de comunicación

Strudel genera audio → Strudel envía mensaje OSC → oscBridge recibe y reenvía → p5.js interpreta → Visual cambia en tiempo real

------------------------------------------------------------------------

## Código en Strudel

```
// Pulso grave
$: s("sbd!4")
  .gain(0.9)
  .osc("/kick", 1)
  ._scope()

// ARMONÍA
$: note(`<
         [0,3,7]
         [12,3,7]
         [12,15,7]
         [12,15,19]
       >*8`
     .add("<c4 d4 f4 c5>")
  )
  .room(0.4)
  .gain(0.35)
  .sound("gm_clarinet, supersaw")
  .osc("/chords", 1)

// BAJO
$: note("<c2>*2 <d2>*2 <f2>*2 <c3>*2")
  .sound("gm_electric_bass_finger")
  .gain(2)
  .legato(0.4)
  .osc("/bass", 1)

// Percusión
$: sound("<bd>*4, hh*8")
  .gain(0.6)
  .osc("/hihat", 1)
```


## Bitácora de reflexión












