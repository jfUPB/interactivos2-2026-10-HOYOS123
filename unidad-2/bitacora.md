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

## Bitácora de aplicación 



## Bitácora de reflexión
