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

## Código corregido en Strudel

```
// Pulso grave
let pulse = s("sbd!4")
  .gain(0.9)
  ._scope()
// ARMONÍA
let notes = note(`<
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

// BAJO
let bass = note("<c2>*2 <d2>*2 <f2>*2 <c3>*2")
  .sound("gm_electric_bass_finger")
  .gain(2)
  .legato(0.4)

// Percusión
let perc = sound("<bd>*4, hh*8")
  .gain(0.6)

$: stack(
  pulse.gain('0.5'),
  pulse.osc()
)
$: stack(
  notes.gain('0.5'),
  notes.osc()
)
$: stack(
  bass.gain('0.5'),
  bass.osc()
)
$: stack(
  perc.gain('0.5'),
  perc.osc()
)
```

---

### Código en Visual Studio Code:
```
<!DOCTYPE html>
<html>
<head>
  <script src="https://cdn.jsdelivr.net/npm/p5@1.9.0/lib/p5.min.js"></script>
  <style> body { margin: 0; overflow: hidden; background: black; } </style>
</head>
<body>
<script>

let eventQueue = [];

// VISUALES
let bgPulse = 0;
let warp = 0;
let particles = [];
let scanLines = [];
let spirals = [];

let lastDelta = 0;
let history = [];

function setup() {
  createCanvas(windowWidth, windowHeight);
  frameRate(60);

  const socket = new WebSocket('ws://localhost:8081');

  socket.onmessage = (event) => {
    const msg = JSON.parse(event.data);
    if (msg.address !== "/dirt/play") return;
    if (!msg.args) return;

    let s = "";

    for (let i = 0; i < msg.args.length; i += 2) {
      if (msg.args[i] === "s") {
        s = msg.args[i + 1];
      }
    }

    if (!s) return;

    eventQueue.push({
      timestamp: Math.floor(msg.timestamp),
      s: s
    });

    eventQueue.sort((a, b) => a.timestamp - b.timestamp);
  };
}

function draw() {
  background(0);

  let now = Date.now();
  drawDebugTimeline(now);

  while (eventQueue.length > 0 && now >= eventQueue[0].timestamp) {
    let ev = eventQueue.shift();

    lastDelta = now - ev.timestamp;
    history.push(lastDelta);
    if(history.length > 50) history.shift();

    ejecutarVisual(ev.s);
  }

  if (bgPulse > 0) {
    noStroke();
    for (let i = 0; i < height; i += 5) {
      fill(50 + bgPulse, 0, 80 + bgPulse * 0.5, 60);
      rect(0, i, width, 5);
    }
    bgPulse *= 0.9;
  }


  if (warp > 0) {
    noFill();
    stroke(150, 0, 255, 180);
    strokeWeight(3);
    circle(width/2, height/2, warp);
    warp *= 1.1;
    if (warp > width * 1.5) warp = 0;
  }


  for (let i = particles.length - 1; i >= 0; i--) {
    let p = particles[i];
    fill(0, 255, 120, p.alpha);
    noStroke();
    circle(p.x, p.y, p.size);

    p.x += p.vx;
    p.y += p.vy;
    p.alpha -= 5;

    if (p.alpha <= 0) particles.splice(i, 1);
  }


  for (let i = scanLines.length - 1; i >= 0; i--) {
    let s = scanLines[i];
    stroke(0, 200, 255, s.alpha);
    line(0, s.y, width, s.y);
    s.y += 15;
    s.alpha -= 10;
    if (s.alpha <= 0) scanLines.splice(i, 1);
  }


  push();
  translate(width/2, height/2);

  for (let i = spirals.length - 1; i >= 0; i--) {
    let sp = spirals[i];
    stroke(255, 200, 0, sp.alpha);
    noFill();
    beginShape();
    for (let a = 0; a < TWO_PI * 3; a += 0.1) {
      let r = sp.radius + a * 5;
      let x = cos(a + sp.rot) * r;
      let y = sin(a + sp.rot) * r;
      vertex(x, y);
    }
    endShape();

    sp.rot += 0.05;
    sp.alpha -= 3;

    if (sp.alpha <= 0) spirals.splice(i, 1);
  }

  pop();

  // DEBUG
  fill(255);
  noStroke();
  textSize(14);
  text(Último error: ${lastDelta.toFixed(2)} ms, 20, 30);
  text(FPS: ${Math.round(frameRate())}, 20, 50);
}

// ======================
// DISPARADOR
// ======================

function ejecutarVisual(s) {
  if (!s) return;

  // KICK
  if (s.includes('bd')) {
    bgPulse = 150;
    warp = 50;
  }


  if (s.includes('sd')) {
    for (let i = 0; i < 20; i++) {
      particles.push({
        x: width/2,
        y: height/2,
        vx: random(-5, 5),
        vy: random(-5, 5),
        size: random(5, 15),
        alpha: 255
      });
    }
  }


  if (s.includes('hh')) {
    scanLines.push({
      y: random(height),
      alpha: 200
    });
  }


  if (s.includes('rim') || s.includes('lt') || s.includes('rd')) {
    spirals.push({
      radius: random(20, 100),
      rot: random(TWO_PI),
      alpha: 255
    });
  }
}

function drawDebugTimeline(now) {
  push();
  translate(0, height - 120);

  stroke(255, 0, 0);
  line(width / 2, 0, width / 2, 80);

  let escala = 0.5;

  eventQueue.forEach(ev => {
    let x = width / 2 + (ev.timestamp - now) * escala;
    fill(0, 255, 255);
    ellipse(x, 40, 20, 20);
  });

  history.forEach((d, i) => {
    fill(255, 255, 255, i * 5);
    ellipse(width / 2 + d * escala, 60, 10, 10);
  });

  pop();
}

function windowResized() {
  resizeCanvas(windowWidth, windowHeight);
}

</script>
</body>
</html>
```

## Bitácora de reflexión

a












