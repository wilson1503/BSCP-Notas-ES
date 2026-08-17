# 01 — Server-side vulnerabilities

Primer bloque de estudio orientado a fundamentos de vulnerabilidades del lado servidor, siguiendo el learning path **Server-side vulnerabilities — Apprentice** de PortSwigger.

## Orden de estudio

1. [Path Traversal](01-Path-Traversal.md) — completado ✅
2. [Access Control](02-Access-Control.md) — completado ✅
3. [Authentication](03-Authentication.md) — en estudio 🟡
4. SSRF
5. File Upload Vulnerabilities
6. OS Command Injection
7. SQL Injection

## Authentication — siguiente paso

PortSwigger incluye actualmente 10 elementos en el bloque Authentication de este learning path y **2 labs Apprentice**:

1. **Username enumeration via different responses** — siguiente lab.
2. **2FA simple bypass**.

Procedimientos preparados:

- [Lab 1 — Username enumeration via different responses](../Labs/Authentication/01-Username-enumeration-via-different-responses.md)
- [Lab 2 — 2FA simple bypass](../Labs/Authentication/02-2FA-simple-bypass.md)

El primer lab introduce de forma práctica **Burp Intruder**, por lo que además existe una referencia básica:

- [Burp Intruder — conceptos básicos](../Conceptos-Basicos-Clave/09-Burp-Intruder-Basico.md)

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
