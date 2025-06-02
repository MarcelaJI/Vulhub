# 📄 Kibana - Local File Inclusion (LFI)

[Kibana](https://www.elastic.co/kibana/) es una herramienta de visualización de datos utilizada para explorar y analizar logs almacenados en Elasticsearch. Es comúnmente usada en entornos de monitoreo, seguridad y análisis de grandes volúmenes de datos.

## 🐞 Descripción de la vulnerabilidad

Esta máquina explota una vulnerabilidad de **Local File Inclusion (LFI)** en Kibana, que permite a un atacante leer archivos locales del sistema donde se está ejecutando el servicio.

Una LFI ocurre cuando una aplicación web permite incluir archivos del sistema local sin la debida validación. En este caso, mediante una petición especialmente construida, se pueden leer archivos como `/etc/passwd`, `.bash_history`, o archivos sensibles de configuración.

> 💥 Este tipo de vulnerabilidad puede ser aprovechado para obtener credenciales, secretos, o incluso escalar privilegios si se combina con otros vectores de ataque.

## ✅ Objetivo

Explotar la vulnerabilidad LFI en Kibana para acceder a archivos sensibles y comprender el impacto de esta falla en un entorno real.

---

## El primer paso es irnos a vulhub:

![máquina](./images/1.png)
---

Luego, filtramos con **ctrl + f** y buscamos la máquina vulnerable **kibana**

![máquina](./images/2.png)

---
## Clonar la máquina en nuestro equipo

![máquina](./images/3.png)

---

Desglose del comando
**git clone**:

Clona un repositorio remoto en tu máquina local.

--filter=blob:none
Esta opción dice:

No descargues ningún contenido de archivo (blob) al clonar; solo descarga la estructura del repositorio (commits, árboles, etc.).

Es parte de lo que se llama un "partial clone". El objetivo es reducir el tamaño del clon inicial. Luego, los blobs (contenido real de los archivos) se descargan solo cuando son necesarios.

👉 Esto es útil si el repo tiene muchos archivos pesados o históricos que no necesitas de inmediato.

--no-checkout
Esto evita que Git haga el "checkout" (es decir, que cree una copia de trabajo con los archivos en el disco).
Solo se clona el contenido del repositorio .git, sin extraer nada al sistema de archivos.

---

![máquina](./images/4.png)

Esto se usa en combinación con un clone parcial para decirle a Git que solo quieres ciertos archivos o carpetas, no todo el repositorio. Es ideal para repos grandes o si solo necesitas una parte específica.

🔍 ¿Qué significa cada línea?
1. git sparse-checkout init --cone
🔹 Inicializa el modo de sparse checkout, que es una forma de decirle a Git:

"Quiero trabajar solo con una parte del árbol del repositorio."

--cone activa un modo simplificado y eficiente que solo permite incluir rutas completas (como carpetas o archivos directamente), sin patrones avanzados. Es el modo recomendado.

👉 En resumen: Preparo Git para trabajar solo con una parte del repo.

2. git sparse-checkout set ruta/al/archivo.txt
🔹 Indica qué archivos o carpetas quieres que Git descargue y te muestre en el sistema de archivos.

Aquí, ruta/al/archivo.txt es la ruta relativa del archivo que me interesa dentro del repo.

👉 Git no descargará ni mostrará nada más. Solo ese archivo (o carpeta si fuera una ruta de directorio).

3. git checkout
🔹 Esto hace el checkout aplicando las reglas del sparse checkout que acabo de configurar.

Como antes usé --no-checkout, hasta ahora no había extraído nada.

Este paso descarga y muestra en tu disco solo el archivo que descargué, no todo el repositorio.

---

![máquina](./images/5.png)

---

![máquina](./images/6.png)

---

Ahora ejecutamos **docker-compose**: 

Es una herramienta indispensable en hacking porque permite construir laboratorios dinámicos, reproducibles y controlados con facilidad. Esto mejora la eficiencia en pruebas y aumenta tu capacidad para simular situaciones del mundo real.

---

![máquina](./images/7.png)

---

Ahora que ya tenemos el laboratorio desplegado, vemos que en el contenedor tenemos el puerto **5601** la aplicación de kibana, el siguiente paso es ir a nuestro **localhost**:

![máquina](./images/8.png)
---

## Ingeniería Inversa

![máquina](./images/9.png)

---

Según el repo de github de vulhub de **kibana** podemos utilizar ingeniería inversa, entonces lo primero que haré es entrar en la máquina para crear un archivo **.js**

![máquina](./images/10.png)