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

### Actividad 3
![Testeo visuales del caso de estudio - SFI2 JJHP]([https://www.youtube.com/watch?v=ID_DEL_VIDEO](https://youtu.be/mkfk2rZK0xM))
---

## Bitácora de aplicación

## Bitácora de reflexión




