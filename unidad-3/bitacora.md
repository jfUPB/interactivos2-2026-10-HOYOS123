# Unidad 3

## Bitácora de proceso de aprendizaje

Estas son las visuales de la unidad 2 sin el openstage control aún:

```html
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
  text(`Último error: ${lastDelta.toFixed(2)} ms`, 20, 30);
  text(`FPS: ${Math.round(frameRate())}`, 20, 50);
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

---
---


## Bitácora de aplicación 

El código de las visuales debe estar en la carpeta website public del servidor de strudel:

<img width="1086" height="356" alt="image" src="https://github.com/user-attachments/assets/7466bb8b-65ea-4646-b680-73a44852d9cf" />

El código de las visuales quedó así:

```html
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

// CONTROL EXTERNO (Puerto 8082)
let faderValue = 0.5; // Valor por defecto

let lastDelta = 0;
let history = [];

function setup() {
  createCanvas(windowWidth, windowHeight);
  frameRate(60);

  // SOCKET 1: Puerto 8081 (Eventos de audio/Tidal)
  const socket8081 = new WebSocket('ws://localhost:8081');
  socket8081.onmessage = (event) => {
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

  // SOCKET 2: Puerto 8082 (Mensajes de control/Faders)
  const socket8082 = new WebSocket('ws://localhost:8082');
  socket8082.onmessage = (event) => {
    try {
      const msg = JSON.parse(event.data);
      // Si el mensaje es de faderOne, actualizamos nuestro valor de control
      if (msg.address === "/faderOne" && msg.args && msg.args.length > 0) {
        faderValue = msg.args[0]; // El valor 0.379... del ejemplo
      }
    } catch (e) {
      console.error("Error parseando mensaje en 8082", e);
    }
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

  // Fondo pulsante
  if (bgPulse > 0) {
    noStroke();
    for (let i = 0; i < height; i += 5) {
      fill(50 + bgPulse, 0, 80 + bgPulse * 0.5, 60);
      rect(0, i, width, 5);
    }
    bgPulse *= 0.9;
  }

  // Warp circle
  if (warp > 0) {
    noFill();
    // stroke(150, 0, 255, 180);
    // strokeWeight(3);
    // console.log("Si entro a warp");
    stroke(255, 100 + (faderValue * 155), 0, 180);
    strokeWeight(1 + (faderValue * 10)); 

    circle(width/2, height/2, warp);
    warp *= 1.1;
    if (warp > width * 1.5) warp = 0;
  }

  // Partículas
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

  // Scanlines
  for (let i = scanLines.length - 1; i >= 0; i--) {
    let s = scanLines[i];
    stroke(0, 200, 255, s.alpha);
    line(0, s.y, width, s.y);
    s.y += 15;
    s.alpha -= 10;
    if (s.alpha <= 0) scanLines.splice(i, 1);
  }

  // Espirales (Afectadas por faderValue)
  push();
  translate(width/2, height/2);
  for (let i = spirals.length - 1; i >= 0; i--) {
    let sp = spirals[i];
    
    // El fader altera el color (más amarillo/rojo) y el grosor

    console.log("Si entro a espirales");

    stroke(255, 100 + (faderValue * 155), 0, sp.alpha);
    strokeWeight(1 + (faderValue * 10)); 
    
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

  // DEBUG INFO
  fill(255);
  noStroke();
  textSize(14);
  text(`Último error: ${lastDelta.toFixed(2)} ms`, 20, 30);
  text(`FPS: ${Math.round(frameRate())}`, 20, 50);
  // Visualización del valor del Fader
  text(`Fader 8082 (/faderOne): ${faderValue.toFixed(3)}`, 20, 70);
  
  // Barra visual del fader
  fill(100);
  rect(20, 80, 100, 10);
  fill(0, 255, 0);
  rect(20, 80, faderValue * 100, 10);
}

function ejecutarVisual(s) {
  if (!s) return;

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

---

### Actividad 1
Instalé Open Stage Control y probé el envío de datos básicos hacia el puerto 9000 para verificar que el Bridge.js los recibía correctamente.

Para que esto funcione necesité modificar el bridge de OpenStage control para adpatar los puertos. Recibo OSC por el 9000 y reenvío por el 8082:

Y el launcher del openstage control quedó configurado así:

<img width="652" height="338" alt="image" src="https://github.com/user-attachments/assets/e986bcd2-60f7-4938-ae6c-82fbae58f529" />

```js
const { WebSocket } = require("ws");
const osc = require("osc");

const WS_PORT = 8082;
const OSC_PORT = 9000;

const wss = new WebSocket.Server({ port: WS_PORT });
console.log("WS relay listening on ws://127.0.0.1:" + WS_PORT);

const udpPort = new osc.UDPPort({
  localAddress: "0.0.0.0",
  localPort: OSC_PORT,

  // Déjalo en false/omitido si quieres args crudos.
  // Si algún día activas metadata:true, el normalizador igual te deja números.
  // metadata: true,
});

udpPort.open();

function normalizeArg(a) {
  // Soporta ambos formatos:
  // - raw: 0.3
  // - metadata: { type: "f", value: 0.3 }
  if (a != null && typeof a === "object" && "value" in a) return a.value;
  return a;
}

udpPort.on("message", (msg, timeTag, info) => {
  const payload = {
    address: msg.address,
    args: Array.isArray(msg.args) ? msg.args.map(normalizeArg) : [],
    from: { address: info.address, port: info.port },
    timeTag,
  };

  const text = JSON.stringify(payload);
   console.log("Received OSC message:", text);

  for (const client of wss.clients) {
    if (client.readyState === WebSocket.OPEN) client.send(text);
  }
});

udpPort.on("ready", () => {
  console.log("OSC listening on udp://0.0.0.0:" + OSC_PORT);
});
```

---


### Actividad 2:

Al controlar las visuales decidí cambiarle el valor al grosor del pulso, vara que se viera un efecto de una especie de "bombo" claro. De ahí taté de cambiar un poco el color de los cuadrados pequeños que aparecían randomizados en la pantalla.

---

## Bitácora de reflexión



