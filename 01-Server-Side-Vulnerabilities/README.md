# 01 — Server-side vulnerabilities

Primer bloque de estudio orientado a fundamentos de vulnerabilidades del lado servidor, siguiendo el learning path **Server-side vulnerabilities — Apprentice** de PortSwigger.

## Orden de estudio

1. [Path Traversal](01-Path-Traversal.md) — completado ✅
2. [Access Control](02-Access-Control.md) — completado ✅
3. [Authentication](03-Authentication.md) — completado ✅
4. [Server-side request forgery (SSRF)](04-SSRF.md) — en estudio 🟡
5. File Upload Vulnerabilities
6. OS Command Injection
7. SQL Injection

## SSRF — siguiente paso

PortSwigger incluye actualmente 6 elementos en el bloque SSRF de este learning path y **2 labs Apprentice**:

1. **Basic SSRF against the local server** — siguiente lab.
2. **Basic SSRF against another back-end system**.

Procedimientos preparados:

- [Lab 1 — Basic SSRF against the local server](../Labs/SSRF/01-Basic-SSRF-against-local-server.md)
- [Lab 2 — Basic SSRF against another back-end system](../Labs/SSRF/02-Basic-SSRF-against-backend-system.md)

Referencia básica añadida:

- [SSRF: loopback, localhost y redes internas](../Conceptos-Basicos-Clave/10-SSRF-Loopback-y-Redes-Internas.md)

## Burp que se practicará

En este bloque reforzaremos:

```text
Proxy → Intercept
Send to Repeater
modificar stockApi
leer HTML en Responses
Send to Intruder
Payload type: Numbers
ordenar por Status code
Intruder → Repeater
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
