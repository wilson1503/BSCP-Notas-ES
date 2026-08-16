# 01 — Server-side vulnerabilities

Primer bloque de estudio orientado a fundamentos de vulnerabilidades del lado servidor.

## Orden de estudio

1. [Path Traversal](01-Path-Traversal.md)
2. Access Control
3. Authentication
4. SSRF
5. File Upload Vulnerabilities
6. OS Command Injection
7. SQL Injection

## Objetivo del bloque

Aprender a reconocer cuándo una aplicación permite que datos controlados por el usuario afecten directamente operaciones del servidor: lectura de archivos, autorización, autenticación, solicitudes internas, subida de archivos, comandos del sistema y consultas SQL.

## Cómo usar estas notas

No memorizar payloads aislados. Para cada vulnerabilidad debemos responder:

- ¿Qué entrada controla el usuario?
- ¿Qué hace el servidor con esa entrada?
- ¿Qué cambio pequeño puedo hacer para comprobar una hipótesis?
- ¿Qué respuesta confirma o descarta la vulnerabilidad?
- ¿Qué defensa está intentando impedir el ataque?
- ¿Qué bypass tiene sentido y por qué?

## Referencias comunitarias

DCKento organiza varias de estas técnicas alrededor de las etapas prácticas del BSCP. Por ejemplo, incluye **Path Traversal**, SSRF, File Upload y Command Injection entre las técnicas útiles para alcanzar acceso al sistema de archivos/servidor. Esta organización se usa como referencia secundaria; el orden de aprendizaje de este repositorio sigue PortSwigger.
