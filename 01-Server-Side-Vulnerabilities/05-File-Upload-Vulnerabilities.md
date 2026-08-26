# 05 — File Upload Vulnerabilities

> **Método de estudio:** seguir primero el orden, conceptos y workflows oficiales de PortSwigger Web Security Academy. Los bypasses avanzados, payload collections y atajos de examen quedan separados en `Preparacion-Examen/`.

## Qué son las vulnerabilidades de File Upload

Una vulnerabilidad de **file upload** aparece cuando una aplicación permite subir archivos al servidor sin validar correctamente aspectos como:

- nombre;
- extensión;
- tipo MIME / `Content-Type`;
- contenido;
- tamaño;
- ubicación final;
- si el servidor ejecutará o interpretará el archivo.

El riesgo más grave es que el servidor termine ejecutando un archivo controlado por el usuario.

La idea central es:

```text
usuario sube archivo
        ↓
servidor lo guarda
        ↓
archivo queda accesible
        ↓
servidor lo interpreta como código
        ↓
Remote Code Execution
```

---

# Orden oficial en Server-side vulnerabilities — Apprentice

PortSwigger incluye actualmente 9 elementos en este bloque:

1. What are file upload vulnerabilities?
2. How do file upload vulnerabilities arise?
3. Exploiting unrestricted file uploads to deploy a web shell
4. **Lab: Remote code execution via web shell upload**
5. Exploiting flawed validation of file uploads
6. Flawed file type validation
7. Flawed file type validation — Continued
8. Flawed file type validation — Continued
9. **Lab: Web shell upload via Content-Type restriction bypass**

En este bloque Apprentice haremos **2 labs**.

---

# 1. Unrestricted file upload

Si la aplicación permite subir un archivo ejecutable y después acceder a él por URL, el servidor puede llegar a ejecutar ese código.

En los labs de PortSwigger el ejemplo típico usa PHP:

```php
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

La finalidad del payload es leer el archivo objetivo del lab.

## Concepto de web shell

Una **web shell** es un archivo ejecutable desde la web que permite ejecutar código en el servidor.

En este bloque Apprentice no necesitamos todavía construir shells complejas. El objetivo es entender que un archivo PHP subido puede ejecutarse si:

```text
1. el servidor acepta el archivo;
2. lo almacena en una ubicación accesible;
3. esa ubicación interpreta PHP.
```

---

# Lab 1 — Remote code execution via web shell upload ✅

> [Procedimiento completo paso a paso](../Labs/File-Upload/01-RCE-via-web-shell-upload.md)

## Objetivo

Subir un archivo PHP que lea:

```text
/home/carlos/secret
```

acceder al archivo subido y enviar el valor mediante **Submit solution**.

## Qué enseña PortSwigger

```text
Upload de avatar
      ↓
archivo PHP aceptado sin restricciones
      ↓
archivo guardado en /files/avatars/
      ↓
GET al archivo
      ↓
servidor ejecuta PHP
      ↓
secret de Carlos
```

Este lab enseña el caso más directo: **no hay que evadir validaciones**.

---

# 2. Flawed file type validation

Muchas aplicaciones intentan proteger la subida validando el tipo del archivo.

Una comprobación común es mirar el header MIME de la parte multipart:

```http
Content-Type: image/jpeg
```

El problema es que este valor viene de la **request del cliente** y puede ser modificado.

Ejemplo conceptual:

```text
archivo = shell.php
Content-Type = application/x-php
        ↓
rechazado

archivo = shell.php
Content-Type = image/jpeg
        ↓
aceptado si el servidor confía solo en ese header
```

El nombre del archivo sigue siendo `.php`; únicamente falsificamos el MIME declarado.

---

# Lab 2 — Web shell upload via Content-Type restriction bypass ⏳

> [Procedimiento completo paso a paso](../Labs/File-Upload/02-Web-shell-via-Content-Type-bypass.md)

## Objetivo

Subir el mismo tipo de archivo PHP, pero esta vez superar una validación que solo permite imágenes modificando:

```http
Content-Type: application/x-php
```

por:

```http
Content-Type: image/jpeg
```

## Flujo que debemos aprender

```text
upload shell.php
        ↓
server rechaza por MIME
        ↓
Burp Repeater
        ↓
Content-Type: image/jpeg
        ↓
server acepta
        ↓
GET /files/avatars/shell.php
        ↓
PHP ejecutado
```

---

# Multipart/form-data

Las subidas de archivos suelen usar requests como:

```http
POST /my-account/avatar HTTP/2
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary...
```

Dentro del body aparece una parte similar a:

```http
Content-Disposition: form-data; name="avatar"; filename="exploit.php"
Content-Type: application/x-php

<?php echo file_get_contents('/home/carlos/secret'); ?>
```

Debemos aprender a reconocer tres cosas:

```text
filename="exploit.php"     ← nombre/extensión
Content-Type: ...          ← MIME declarado
contenido del archivo      ← payload real
```

---

# Qué mirar en Burp

En File Upload será especialmente importante:

- `Proxy → HTTP history`;
- localizar `POST /my-account/avatar`;
- revisar el body `multipart/form-data`;
- identificar `filename=`;
- identificar el `Content-Type` de la parte del archivo;
- `Send to Repeater`;
- editar solo el dato que PortSwigger está enseñando;
- localizar posteriormente el `GET /files/avatars/<archivo>`.

---

# Método mental

```text
¿puedo subir un archivo?
        ↓
¿qué restricciones existen?
        ↓
¿qué comprueba realmente el servidor?
        ↓
¿extensión? ¿MIME? ¿contenido?
        ↓
¿puedo controlar ese dato desde Burp?
        ↓
¿dónde queda almacenado?
        ↓
¿el servidor lo ejecuta cuando lo solicito?
```

---

# Diferencia importante: guardar vs ejecutar

No basta con que un archivo se suba correctamente.

```text
archivo almacenado como texto
→ no necesariamente RCE

archivo almacenado + interpretado como PHP
→ ejecución server-side
```

Por eso después de cada upload debemos comprobar **cómo sirve el servidor ese archivo**.

---

# Qué NO mezclaremos todavía

El learning path completo de File Upload contiene técnicas Practitioner y temas más avanzados como:

- path traversal en el filename;
- blacklist de extensiones;
- `.htaccess`;
- extensiones ofuscadas;
- polyglot files;
- validación del contenido;
- race conditions;
- PUT uploads.

Los estudiaremos cuando PortSwigger los introduzca. No los usaremos para sustituir los workflows Apprentice.

---

# Burp que debemos dominar en este bloque

- [x] Reconocer `multipart/form-data`.
- [x] Localizar la parte del archivo dentro del body.
- [x] Identificar `filename=`.
- [ ] Identificar el `Content-Type` de una parte multipart.
- [ ] Enviar un upload a Repeater.
- [ ] Modificar el MIME sin alterar innecesariamente el resto.
- [x] Encontrar la URL final del archivo subido.
- [x] Distinguir upload exitoso de ejecución exitosa.

---

# Checklist Apprentice

- [x] Entiendo qué es una vulnerabilidad de file upload.
- [x] Entiendo qué significa web shell.
- [x] Entiendo por qué un `.php` puede producir RCE.
- [x] Sé leer una request `multipart/form-data`.
- [ ] Entiendo que `Content-Type` es controlable por el cliente.
- [ ] Sé usar Repeater para modificar el MIME de una parte multipart.
- [x] Sé encontrar y abrir el archivo subido.
- [x] Lab 1 completado.
- [ ] Lab 2 completado.

---

# Registro de labs

## Lab 1 — Remote code execution via web shell upload

- Estado: ✅ resuelto.
- Objetivo: leer `/home/carlos/secret`.
- Idea: upload PHP sin restricciones.

## Lab 2 — Web shell upload via Content-Type restriction bypass

- Estado: ⏳ en progreso.
- Objetivo: leer `/home/carlos/secret`.
- Técnica: modificar el MIME declarado a `image/jpeg`.

---

# Regla rápida

**En File Upload no preguntarnos solo “¿me deja subirlo?”. También: “¿dónde lo guarda y qué hace el servidor cuando alguien solicita ese archivo?”.**
