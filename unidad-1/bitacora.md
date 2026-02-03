# Unidad 1

# Bitácora de proceso de aprendizaje
<img width="1083" height="977" alt="image" src="https://github.com/user-attachments/assets/26e34fc6-4292-4b27-8066-b12766f2e270" />

<img width="463" height="871" alt="image" src="https://github.com/user-attachments/assets/bef180c7-4881-4f8c-86fa-2c176a160744" />



# Bitácora de aplicación

## 🛠️ Actividad 04 – Creación de una pieza de audio interactiva

## Descripción general de la pieza

Desarrollé una pieza de audio interactiva utilizando **Strudel**, enfocada en la exploración de patrones rítmicos, armónicos y de bajo que se sincronizan en un mismo ciclo temporal. La pieza está construida por capas independientes que se ejecutan simultáneamente y generan una estructura musical coherente y repetitiva.

El objetivo principal fue crear una base rítmica sólida, acompañada por una progresión armónica clara y un bajo que refuerza el ciclo musical.

---

## Proceso de creación

El proceso comenzó buscando referencias de personas en internet que hicieran este tipo de cosas en Strudel, y me encontré con un video corto que me enseñó como realizar la base característica de la pieza que es la base armónica. A partir de esto, la base armónica lleva acordes construidos por intervalos, los cuales se transponen siguiendo un ciclo de notas base. Despues, le añadí la base rítmica, que simula el bombo de una batería. Finalmente, se incorporó un bajo que sigue el recorrido armónico que aporta movimiento y groove.

logré esto:
```
$: note(`<
         [0,3,7]
         [12,3,7]
         [12,15,7]
         [12,15,19]
         >*8`
       .add("<c3 d3 f3 c4>")
    )
    .room(.4)
    .sound("gm_clarinet, supersaw")
```

Durante el desarrollo fue necesario ajustar el registro, la ganancia y la duración de las notas para evitar que las capas se enmascararan entre sí y lograr que cada elemento fuera claramente audible dentro de la mezcla.

---

## Explicación detallada del código

### 1. Pulso grave

```js
$: s("sbd!4")
  .gain(0.9)
  ._scope()
```

Esta capa establece un pulso grave constante mediante la repetición del sonido `sbd`. Funciona como ancla rítmica de la pieza.
El parámetro `gain(0.9)` controla el volumen para que no sature la mezcla, y `_scope()` permite visualizar la forma de onda, lo cual facilita la observación del comportamiento del audio en tiempo real.

---

### 2. Capa armónica

```js
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
```

Esta sección corresponde a la armonía de la pieza.
Los acordes están construidos a partir de intervalos, lo que permite generar distintas sonoridades manteniendo una estructura coherente. Estos acordes se repiten ocho veces antes de cambiar, lo que crea una sensación de ciclo.

La función `.add("<c4 d4 f4 c5>")` transpone la progresión a diferentes notas base, generando movimiento armónico.
Se utilizan dos timbres (`gm_clarinet` y `supersaw`) para combinar un sonido acústico con uno sintético, aportando riqueza tímbrica.
El efecto `room(0.4)` añade reverberación ligera, y `gain(0.35)` mantiene esta capa en un plano medio dentro de la mezcla.

---

### 3. Bajo

```js
$: note("<c2>*2 <d2>*2 <f2>*2 <c3>*2")
  .sound("gm_electric_bass_finger")
  .gain(2)
  .legato(0.4)
```

El bajo sigue el mismo ciclo armónico que la capa de acordes, pero reducido a la nota fundamental y ubicado en un registro grave. Esto refuerza la estructura musical y aporta estabilidad.

Se utiliza el sonido `gm_electric_bass_finger` para lograr un carácter más realista, similar al de un bajo eléctrico tocado con los dedos.
El parámetro `legato(0.4)` hace que las notas sean cortas y definidas, generando un bajo rítmico y preciso.
La ganancia se incrementa para asegurar que el bajo sea claramente audible frente a la percusión.

---

### 4. Percusión

```js
$: sound("<bd>*4, hh*8")
  .gain(0.6)
```

La percusión combina un bombo constante con hi-hats rápidos, creando un patrón rítmico estable y bailable.
El bombo marca el pulso principal, mientras que los hi-hats aportan sensación de movimiento.
La ganancia se mantiene moderada para no interferir con el bajo ni la armonía.

---

## Reflexión final

Esta actividad permitió comprender cómo Strudel maneja el tiempo, los patrones y la superposición de capas sonoras. A través del proceso identifiqué la importancia de la mezcla, el registro y la duración de las notas para que cada elemento sea perceptible. Como posible mejora futura, se podría aumentar la interactividad modificando parámetros en tiempo real durante la ejecución, siguiendo el enfoque de live coding.

---

## Códico del tema logrado:

```
// Pulso grave
$: s("sbd!4")
  .gain(0.9)
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

// BAJO
$: note("<c2>*2 <d2>*2 <f2>*2 <c3>*2")
  .sound("gm_electric_bass_finger")
  .gain(2)
  .legato(0.4)

// Percusión
$: sound("<bd>*4, hh*8")
  .gain(0.6)
```

# Bitácora de reflexión

