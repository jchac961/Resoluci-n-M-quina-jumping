<img width="667" height="251" alt="Captura de pantalla 2026-06-29 002417" src="https://github.com/user-attachments/assets/487f640e-7e0b-49ac-b8dc-7e18372d760d" />

# Resoluci-n-M-quina-jumping

Máquina de la plataforma www.whoami-labs.com

Primeramente iniciamos la máquina vulnerable

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-28 213511" src="https://github.com/user-attachments/assets/41b0485c-b89e-4566-9c38-0dd98133cc7d" />

# RECONOCIMIENTO

Teniendo la máquina ejecutando se hizo un escaneo de puertos con la herramienta nmap para verificar con cuales puertos contaba la máquina abiertos y los servicios que se encontraban ejecutando si asi fuera el caso, dentro de ellos encontramos el puerto #8080 ejecutando un servicio Apache http 2.4.66

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-28 230725" src="https://github.com/user-attachments/assets/603003f4-2cf1-4115-86cd-02deb1fd9161" />

Ya sabiendo que el servicio que estaba ejecutando esta maquina era un hhtp, procedimos a verificar en un navegador a ver que contenia el mismo

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-28 231124" src="https://github.com/user-attachments/assets/c97df56d-d62a-412c-836d-bd5015a39af6" />

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-28 231130" src="https://github.com/user-attachments/assets/cbdb77c8-86a5-4297-a0b6-2ca5508e185d" />

Hice algunas pruebas utilizando credenciales comunes, y obtuve acceso a la plataforma que alojaba esta maquina

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-28 232333" src="https://github.com/user-attachments/assets/2651bdf2-fc20-445d-88fd-e6057d7a9e2e" />

Es importante mencionar que esta es una e las vulnerabilidades que podemos encontrar cuando un desarrollador deja credenciales por default en servicios de acceso a servidores, bases de datos etc.

Ya estando dentro del sistema seguimos buscando y pudimos encontrar un directorio que contenia acceso a subir archivos al sistema, pero limitado solo a archivos con extensiones .jpg, .png, .gif, .pdf

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-28 232350" src="https://github.com/user-attachments/assets/f320acd0-5ca4-4ea6-81c8-51cd0a864cd5" />

Procedimos a traernos el archivo php por defecto que trae la maquina kali y modificamos dentro de su  codigo la ip y el puerto de nuestra maquina pra que pudiera devolvernos la reverse shell y luego lo convertimos en un archivo con extensión .png

<img width="1920" height="473" alt="Captura de pantalla 2026-06-28 232811" src="https://github.com/user-attachments/assets/dac6218d-146e-4fc6-98b0-9d995b4b8414" />

# EXPLOTACIÓN

Subimos el archivo al sistema

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-28 233019" src="https://github.com/user-attachments/assets/4727a062-60ec-47b9-a51a-790ae95ccb3b" />

Ya habiamos dejado nuestra maquina en modo escucha y procedimos a abrir el archivo que habiamos subido, y obtuvimos acceso al sistema

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-28 233042" src="https://github.com/user-attachments/assets/11efafc7-fd2e-491a-9b07-40340bb2277f" />

# ESCALADA DE PRIVILEGIOS

Verificamos a ver si este usuario poseia algun binario con suid activo para tratar de conseguir escalar privilegios pero el mismo no contenia

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-28 233126" src="https://github.com/user-attachments/assets/60608f9d-13c7-44a6-94e6-7466f5553015" />

Verificamos sudo -l, verificamos crontab, y capabilities pero no encontramos nada que nos funcionara para escalar privilegiso

<img width="1920" height="246" alt="Captura de pantalla 2026-06-28 233747" src="https://github.com/user-attachments/assets/6c408140-0c2d-4c55-840c-3c451ebfde3b" />

Buscamos a ver si de casualidad encontrabamos algun archivo que iniciara con la palabra flag (no estaba de mas), en efecto encontramos que en la carpeta opt habia un archivo llamado flag1.txt

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-28 235337" src="https://github.com/user-attachments/assets/f7e5770b-aa6a-40c9-8af1-8a3930900709" />

Fuimos a la carpeta opt a ver si encontrabamos algo mas de importancia y si encontramos un archivo llamado db_backup.sh

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-28 235435" src="https://github.com/user-attachments/assets/e1e0e137-e3b6-4628-aba2-9b4504e92776" />

Visualizamos la flag y tambien el contenido del archivo que encontramos, dentro del cual habia unas credenciales

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-28 235643" src="https://github.com/user-attachments/assets/67f32dc7-5bc9-4180-a30e-d49ece2f2a14" />

Luego ya teniendo estas credenciales intentamos ingrersar por ssh pero recordamos que no teniamos puerto 22 abierto, sin embargo en el archivo anterior nos daba una ruta distinta a la de la maquina objetivo que teniamos, verificamos si la misma tenia el puerto 22 abierto, y en efecto si lo tenia, asi que procedimos a estabilizar la shell, luego enviamos el servicio nc que teniamos al fondo, seguido configuramos la shell en modo raw y volvimos a traer el nc de vuelta y ya teniendo la maquina de vuelta ingresamos por medio de ssh, cabe mencionar que los pases antes mencionados se utilizaron para realizar un salto de la maquina en la cual estabamos a la maquina victima que si contenia lo que necesitabamos (maquina objetivo real).

<img width="1920" height="588" alt="Captura de pantalla 2026-06-29 001258" src="https://github.com/user-attachments/assets/e0bb2206-9dcc-484c-8c53-e3405f5e129f" />

Ya estando dentro verificamos los binarios en busqueda de alguno que tuviera suid activo, encontrando el de python 3.9  

<img width="1920" height="323" alt="Captura de pantalla 2026-06-29 001622" src="https://github.com/user-attachments/assets/0563e728-da30-4797-9bba-e9eec59aa3c3" />

Lo utilizamos y obtuvimos acceso como root

<img width="1920" height="432" alt="Captura de pantalla 2026-06-29 001657" src="https://github.com/user-attachments/assets/ac2bd450-9f08-4ff7-8c0c-d890c81a94da" />

# RESULTADOS

Obtuvimos la flag

<img width="1920" height="781" alt="Captura de pantalla 2026-06-29 001723" src="https://github.com/user-attachments/assets/a1e037d3-4c76-4ab9-a9f2-48330af1b96c" />

Listo

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-29 002202" src="https://github.com/user-attachments/assets/59bf19e1-01a6-4a89-ab1b-2dc632c2bd72" />

*

<img width="2688" height="1596" alt="Gemini_Generated_Image_8i02238i02238i02" src="https://github.com/user-attachments/assets/8918420d-e2e2-4f1a-9883-09b6a14fe782" />


