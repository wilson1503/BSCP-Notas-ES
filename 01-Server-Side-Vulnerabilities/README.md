# 01 — Server-side vulnerabilities — Apprentice

Primer learning path completado siguiendo **Server-side vulnerabilities — Apprentice** de PortSwigger.

## Estado

✅ **Completado**

## Orden completado

1. [Path Traversal](01-Path-Traversal.md) — ✅
2. [Access Control](02-Access-Control.md) — ✅
3. [Authentication](03-Authentication.md) — ✅
4. [Server-side request forgery (SSRF)](04-SSRF.md) — ✅
5. [File Upload Vulnerabilities](05-File-Upload-Vulnerabilities.md) — ✅
6. [OS Command Injection](06-OS-Command-Injection.md) — ✅
7. [SQL Injection](07-SQL-Injection.md) — ✅

## Qué se practicó en Burp

Durante este path se usaron y reforzaron:

```text
Proxy → Intercept
Proxy → HTTP history
Repeater
Intruder
requests GET y POST
cookies y parámetros
multipart/form-data
URL encoding básico
lectura de Response
```

## Conceptos principales cubiertos

- path traversal;
- autorización vertical/horizontal;
- autenticación y 2FA;
- SSRF contra loopback y redes internas;
- file upload y MIME bypass;
- OS command injection;
- SQL injection básica y login bypass.

## Siguiente etapa

Pasamos a **Practitioner**, comenzando por SQL Injection para profundizar inmediatamente lo aprendido.

➡️ [SQL Injection — Practitioner](../02-Practitioner/01-SQL-Injection.md)

➡️ [Labs SQL Injection Practitioner](../Labs/SQL-Injection-Practitioner/README.md)

## Regla mantenida

Las referencias comunitarias y los atajos se mantienen en `Preparacion-Examen/`. El aprendizaje técnico sigue primero PortSwigger y sus soluciones oficiales.
