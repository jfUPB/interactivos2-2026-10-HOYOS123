# Unidad 4

## Bitácora de proceso de aprendizaje

Al analizar el repositorio de ejemplo (sfiSocketioDesktopMobile), comprendí que la clave para integrar la interacción del público en tiempo real s el uso de WebSocketsm específicamente a través de la librería Socket.io.

Aprení que el sistema requiere de un servidor web adicional (En este caso, corriendo en Node.js) que actúe como intermediario. Este servidor cumple condos funciones:

- Servir la interfaz móvil: Expone una página web simple (mobile/index.html) que el público abre en sus celulares.

- Gestionar la comunicación bidireccional: Captura los eventos táctiles (Touch X; Touch Y) Del celular y los reenvía instantáneamente a nuestra visualización principal en p5.js.

Un aspecto técnico crcucial que identifiqué en mis pruebas (y documenté en las siguientes capturas de pantalla de la bitácora de aplicación) es la gestión de los puertos. Como ya estoy utilizando el puerto 8081 para Strudel y el 8082 para Open Stage ontrol, configuré el servidor de Socket.io para correr en el puerto 3000. Además, utilicé la funcionalidad Port Forwarding de Visual Studio Code para generar una URL pública segura (devtunnels.ms), permitiendo que cualquier celular conectado a internet pueda interactuar con la obra sin necesidad de estar en la misma red local.

## Bitácora de aplicación 

Aquí se clona el servidor de Strudel, se instalan las dependencias y se inicializa el servidor
<img width="626" height="943" alt="image" src="https://github.com/user-attachments/assets/9cec0ad8-1464-49c1-a737-47cb3bef66a8" />

Aquí se clona el OpenStageControlUItest, se instalan las dependencias y se lanza el BridgeUI
<img width="628" height="974" alt="image" src="https://github.com/user-attachments/assets/b16d9a8f-8d98-447d-8b7d-5bdacd8c7599" />

Después de instalar el SocketIoDesktopMobile se abre el Visual Studio Code
<img width="588" height="365" alt="image" src="https://github.com/user-attachments/assets/e7f39aeb-1275-4a7f-9ba5-e53955c7474f" />

Aquí se instalan las dependencias en el caso de estudio StrudelP5-tests y se lanza el Bridge
<img width="579" height="334" alt="image" src="https://github.com/user-attachments/assets/a9526d0e-f604-40dd-9f21-cd1a6a0b59b4" />

Aquí en el Visual Studio Code se abre la terminal y se crea un puerto para que los celulares del público puedan conectarse
<img width="1279" height="1028" alt="image" src="https://github.com/user-attachments/assets/e8e135c3-c891-403b-8ea3-4e9fbdd81815" />

Esta es una imagen de la aplicación Open tage Control
<img width="1919" height="1029" alt="image" src="https://github.com/user-attachments/assets/406481f5-8f99-4aae-9550-a0569798671c" />

## Bitácora de reflexión
