# Unidad 5
## Bitácora de proceso de aprendizaje

# Experiencia Interactiva (Strudel + TouchDesigner + Kinect)

### Actividad 01: Definición del Concepto

**1. ¿Qué quiero comunicar o provocar?**
La idea es que quiero mezclar la música con una estética de error digital o *glitch*. La intención es que la gente sienta que sus movimientos físicos tienen un impacto real en lo que escuchan y ven, rompiendo esa barrera entre el humano y la máquina.

**2. ¿En qué contexto se presentará?**
Se presentará en una **sala oscura**. Esto es clave para que las visuales proyectadas desde TouchDesigner sean la única fuente de luz, logrando que el público se sienta sumergido en el entorno digital y que las texturas se proyecten directamente sobre ellos.

**3. ¿Cuál es la experiencia que deseo para el público?**
Busco una experiencia **participativa e inmersiva**. No quiero que el público solo mire una pantalla; quiero que se sientan invitados a moverse y que descubran que, al bailar o desplazarse, el sistema les responde cambiando el sonido o deformando las visuales en tiempo real.

**4. ¿Qué rol tendrá el público?**
El público actúa como un **participante activo** o co-creador efímero. Yo programo las reglas y el código base en Strudel y TouchDesigner, pero ellos son los que "terminan" la obra. Sin su movimiento frente al kinect, la obra se queda estática; ellos son los que modulan la energía de la presentación.

**5. Diagrama de arquitectura:**

<img width="830" height="622" alt="image" src="https://github.com/user-attachments/assets/63c7704e-bc94-4126-8c24-5e39d87fc942" />


---

### Referentes de Inspiración
1.  **Ryoji Ikeda:** Me trama mucho cómo usa los datos crudos y el ruido para crear visuales minimalistas pero potentes. Es el referente máximo del "error" convertido en estética.
2.  **Chris Milk (The Treachery of Sanctuary):** Es el ejemplo perfecto de cómo usar la Kinect para que la sombra de la gente se transforme en algo artístico y simbólico.
3.  **Yaxu (Alex McLean):** Creador de TidalCycles. Me inspira porque demuestra que el *live coding* puede ser bailable y tener mucho *groove*.

---

### Actividad 02: Investigación de Referentes

**Obras y proyectos relacionados:**
* **Movimiento Algorave:** Ver cómo otros artistas usan código en vivo para que la gente baile en clubes. Es relevante porque mi proyecto busca esa misma energía de fiesta y movimiento.
* **Stanislav Glazov:** Sus técnicas de *feedback loops* en TouchDesigner son la base de las visuales orgánicas y abstractas que estoy desarrollando.

https://www.youtube.com/watch?v=ehjklqL6g84

https://www.youtube.com/watch?v=SEoyw8Slclc&list=PLyJbNObFGgIEkEV6_vf_J4pDolv81MzTp&index=9

https://youtube.com/shorts/0geQtNQQpx4?si=jBh7LQA_kkuGuqLA

---

## Bitácora de aplicación 

### 1. Proceso de creación del audio
Para armar esta pieza, empecé por el ritmo. Sabía que necesitaba un pulso constante que me sirviera de guía, así que usé un `bd` (bombo) grave. Después, quise meterle una capa melódica que no fuera aburrida, así que experimenté sumando notas y probando sonidos; el clarinete le dio un toque chévere. Al final, fui sumando el bajo y la percusión para que todo tuviera más cuerpo, ajustando los volúmenes de cada parte para que nada se escuchara "estallado".

### 2. Decisiones técnicas y estéticas
* **Combinar Clarinet con Supersaw:** Elegí estos dos porque el clarinete suena muy natural y el supersaw le da ese brillo digital. Me gusta el contraste entre lo orgánico y lo sintético.
* **Uso de `.add()` en la armonía:** Lo hice para que las notas no fueran siempre las mismas. Así, la melodía va subiendo y bajando de tono sola, lo que la hace sonar mucho más dinámica y "viva".
* **Estructura de Stacks:** Decidí separar todo en 4 bloques (`pulse`, `notes`, `bass`, `perc`). Técnicamente esto es clave porque en la próxima unidad me permitirá mandar señales separadas a TouchDesigner y que cada sonido mueva algo diferente.

### 3. Código completo de la pieza

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

## Bitácora de reflexión

### 1. ¿Cómo quedó el sonido?
El audio que armé en Strudel quedó con una base rítmica y armónica bastante **sólida**. Al principio quería que sonara más "roto" (glitch), pero decidí dejarlo así por ahora porque me gusta el ambiente más **tranquilo e inmersivo** que crea. El clarinete y el bajo profundo funcionan muy bien juntos. Prefiero dejar la parte del "error digital" para las visuales en **TouchDesigner**, que se rompan visualmente mientras el sonido mantiene la atmósfera.

### 2. ¿Sigue sirviendo el diagrama?
Sí, el primer diagrama conceptual sigue siendo totalmente válido. La forma en que organicé el código en Strudel (con esos 4 stacks separados) me confirma que voy por buen camino. Esto me va a permitir enviar datos distintos a TouchDesigner para que el bajo mueva una cosa, la melodía otra, y la percusión otra, logrando que audio y video vayan de la mano.

### 3. Principales problemas y cómo los arreglé
* **Que no sonara aburrido:** Al principio la melodía era muy repetitiva. Lo solucioné probando diferentes combinaciones de patrones en Strudel hasta encontrar variaciones que mantuvieran el movimiento sin perder la estructura.
* **El volumen:** Con todas las capas sonando a la vez, el audio se saturaba y sonaba mal. Tuve que ajustar los `gain` (volúmenes) de cada stack al final para que la mezcla quedara limpia.
* **Ver qué pasaba:** Necesitaba estar seguro de que cada sonido generaba un trigger claro para la visualización. Usé las herramientas visuales de Strudel (`_scope()`) para monitorear las ondas y confirmar que todo estaba en orden.
