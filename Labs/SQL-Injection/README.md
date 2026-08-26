# SQL Injection — Labs Apprentice

> Procedimientos basados primero en las soluciones oficiales de PortSwigger Web Security Academy.

## Labs

1. [SQL injection vulnerability in WHERE clause allowing retrieval of hidden data](01-WHERE-clause-retrieving-hidden-data.md)
2. [SQL injection vulnerability allowing login bypass](02-SQLi-login-bypass.md)

## Qué aprenderemos

```text
Lab 1
category
→ cerrar string
→ OR 1=1
→ comentar resto
→ recuperar productos ocultos

Lab 2
username
→ seleccionar administrator
→ cerrar string
→ comentar password
→ bypass de login
```

## Burp principal

Los dos labs usan como workflow oficial:

```text
Proxy → Intercept
modificar request
Forward
observar resultado
```

Repeater queda como práctica complementaria para comparar respuestas después de entender el procedimiento oficial.
