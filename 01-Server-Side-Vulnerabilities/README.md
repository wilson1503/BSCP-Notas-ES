# 01 — Server-side vulnerabilities

Primer bloque de estudio orientado a fundamentos de vulnerabilidades del lado servidor, siguiendo el learning path **Server-side vulnerabilities — Apprentice** de PortSwigger.

## Orden de estudio

1. [Path Traversal](01-Path-Traversal.md) — completado ✅
2. [Access Control](02-Access-Control.md) — completado ✅
3. [Authentication](03-Authentication.md) — completado ✅
4. [Server-side request forgery (SSRF)](04-SSRF.md) — completado ✅
5. [File Upload Vulnerabilities](05-File-Upload-Vulnerabilities.md) — en estudio 🟡
6. OS Command Injection
7. SQL Injection

## File Upload — siguiente paso

PortSwigger incluye actualmente 9 elementos en este bloque del learning path y **2 labs Apprentice**:

1. **Remote code execution via web shell upload** — siguiente lab.
2. **Web shell upload via Content-Type restriction bypass**.

Procedimientos preparados:

- [Lab 1 — Remote code execution via web shell upload](../Labs/File-Upload/01-RCE-via-web-shell-upload.md)
- [Lab 2 — Web shell upload via Content-Type restriction bypass](../Labs/File-Upload/02-Web-shell-via-Content-Type-bypass.md)

Referencia básica añadida:

- [Multipart/form-data y File Upload](../Conceptos-Basicos-Clave/11-Multipart-Form-Data-y-File-Upload.md)

## Burp que se practicará

En este bloque reforzaremos:

```text
Proxy → HTTP history
filtrar/ver requests de imágenes
identificar GET /files/avatars/...
Send to Repeater
leer multipart/form-data
identificar filename=
identificar Content-Type de una parte
modificar MIME en Repeater
leer ejecución PHP en Response
```

## Objetivo del bloque

Aprender a reconocer cuándo una aplicación permite que datos controlados por el usuario afecten directamente operaciones del servidor: lectura de archivos, autorización, autenticación, solicitudes internas, subida de archivos, comandos del sistema y consultas SQL.

## Cómo usar estas notas

No memorizar payloads aislados. Para cada vulnerabilidad debemos responder:

- ¿Qué entrada controla el usuario?
- ¿Qué hace el servidor con esa entrada?
- ¿Qué cambio pequeño puedo hacer para comprobar una hipótesis?
- ¿Qué respuesta confirma o descarta la vulnerabilidad?
- ¿Qué herramienta de Burp enseña el lab?
- ¿Qué defensa está fallando?

## Referencias comunitarias

Las referencias comunitarias se mantienen en `Preparacion-Examen/`. El aprendizaje de este módulo sigue primero PortSwigger y sus soluciones oficiales.
