# 01 — Server-side vulnerabilities

Primer bloque de estudio orientado a fundamentos de vulnerabilidades del lado servidor, siguiendo el learning path **Server-side vulnerabilities — Apprentice** de PortSwigger.

## Orden de estudio

1. [Path Traversal](01-Path-Traversal.md) — completado ✅
2. [Access Control](02-Access-Control.md) — completado ✅
3. [Authentication](03-Authentication.md) — completado ✅
4. [Server-side request forgery (SSRF)](04-SSRF.md) — completado ✅
5. [File Upload Vulnerabilities](05-File-Upload-Vulnerabilities.md) — completado ✅
6. [OS Command Injection](06-OS-Command-Injection.md) — en estudio 🟡
7. [SQL Injection](07-SQL-Injection.md) — preparado para continuar ⬜

## OS Command Injection — módulo actual

PortSwigger incluye **5 elementos** en este bloque y **1 lab Apprentice**:

1. **OS command injection, simple case**.

Procedimiento:

- [Lab — OS command injection, simple case](../Labs/OS-Command-Injection/01-OS-command-injection-simple-case.md)

Referencia básica:

- [OS Command Injection y separadores del shell](../Conceptos-Basicos-Clave/12-OS-Command-Injection-y-Shell-Separators.md)

## SQL Injection — siguiente módulo preparado

PortSwigger incluye **7 elementos** en SQL Injection dentro de este learning path y **2 labs Apprentice**:

1. **SQL injection vulnerability in WHERE clause allowing retrieval of hidden data**.
2. **SQL injection vulnerability allowing login bypass**.

Material preparado:

- [Teoría — SQL Injection](07-SQL-Injection.md)
- [Lab 1 — WHERE clause / hidden data](../Labs/SQL-Injection/01-WHERE-clause-retrieving-hidden-data.md)
- [Lab 2 — login bypass](../Labs/SQL-Injection/02-SQLi-login-bypass.md)
- [SQL básico para entender SQL Injection](../Conceptos-Basicos-Clave/13-SQL-Basico-para-SQL-Injection.md)

## Burp que se practicará en SQL Injection

En los dos labs Apprentice el workflow oficial principal es:

```text
Proxy → Intercept
identificar parámetro vulnerable
modificar request
Forward
observar cómo cambia la aplicación
```

En el primer lab se modifica `category`; en el segundo, `username`.

## Objetivo del bloque

Aprender a reconocer cuándo una aplicación permite que datos controlados por el usuario afecten directamente operaciones del servidor: lectura de archivos, autorización, autenticación, solicitudes internas, subida de archivos, comandos del sistema y consultas SQL.

## Cómo usar estas notas

No memorizar payloads aislados. Para cada vulnerabilidad debemos responder:

- ¿Qué entrada controla el usuario?
- ¿Qué intérprete procesa finalmente esa entrada?
- ¿Qué cambio pequeño puedo hacer para comprobar una hipótesis?
- ¿Qué respuesta confirma o descarta la vulnerabilidad?
- ¿Qué herramienta de Burp enseña el lab?
- ¿Qué defensa está fallando?

## Referencias comunitarias

Las referencias comunitarias se mantienen en `Preparacion-Examen/`. El aprendizaje de este módulo sigue primero PortSwigger y sus soluciones oficiales.
