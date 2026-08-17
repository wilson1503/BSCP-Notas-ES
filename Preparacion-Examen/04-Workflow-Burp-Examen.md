# Workflow rápido de Burp para preparación de examen

> Usar solo después de dominar los workflows oficiales de PortSwigger. Esta versión está pensada como referencia de agilidad.

## Flujo base

```text
Browser
  ↓
Proxy / HTTP history
  ↓
seleccionar request prometedora
  ↓
Repeater para confirmar hipótesis
  ↓
Intruder / Scanner / Collaborator / DOM Invader según necesidad
  ↓
confirmar impacto
  ↓
explotar solo lo necesario para avanzar de fase
```

## Proxy / HTTP history

Usar para:

- construir mapa de endpoints;
- comparar requests antes/después de login;
- identificar cookies y roles;
- detectar parámetros no visibles en la UI;
- encontrar requests API/AJAX;
- revisar respuestas 30x y `Set-Cookie`;
- localizar contenido JS/XML/JSON.

## Repeater

Uso principal:

- crear una baseline;
- cambiar un solo parámetro;
- probar IDs/roles/métodos/headers;
- validar SQLi/SSRF/path traversal/command injection;
- inspeccionar respuestas completas aunque el navegador redirija.

Regla:

```text
POC mínima primero → explotación después
```

## Intruder

Usos de alto valor:

- username/password enumeration;
- MFA codes en labs autorizados;
- IDs o rutas;
- caracteres/tags/atributos permitidos;
- blind SQLi;
- internal IP/port discovery en SSRF;
- fuzzing de traversal/bypasses;
- variantes de encoding.

Antes de usarlo, definir qué diferencia vas a medir:

```text
status
length
word count
error string
response time
redirect
```

## Burp Scanner / targeted scan

Recomendación comunitaria repetida: usar escaneo **dirigido** sobre requests e insertion points interesantes para reducir trabajo manual.

Workflow:

```text
request sospechosa
→ seleccionar parámetro/insertion point
→ Scan selected insertion point / targeted scan
→ revisar issue detail
→ reproducir manualmente
```

No confiar ciegamente en scanner para:

- access control de lógica;
- workflow flaws;
- cadenas complejas;
- autorización contextual.

## Collaborator / OAST

Pensar en Collaborator cuando la vulnerabilidad puede ser ciega:

- blind SSRF;
- blind XXE;
- blind OS command injection;
- blind SQLi OOB;
- Host Header/password reset poisoning;
- XSS con víctima;
- interacción de backend con headers/URLs.

Placeholder recomendado:

```text
COLLABORATOR.oastify.com
```

No guardar dominios reales de sesiones anteriores en cheatsheets.

## DOM Invader

Útil para:

- DOM XSS;
- web messages;
- prototype pollution;
- sources/sinks difíciles de seguir manualmente.

Primero entender el JS; después usar la herramienta para acelerar.

## Decoder

Para:

- URL encoding;
- double URL encoding;
- Base64;
- HTML encoding;
- hex;
- JWT segments para inspección rápida.

## Comparer

Útil cuando dos respuestas son parecidas pero hay pequeñas diferencias:

- user válido vs inválido;
- boolean blind SQLi;
- autorización;
- cache behavior.

## Match and Replace

Preparar más adelante reglas reutilizables para entornos de práctica, por ejemplo:

- reemplazar un header;
- añadir header de prueba;
- cambiar valores repetitivos.

No activar reglas globales si pueden alterar requests que no quieres tocar.

## Response interception

Recordatorio importante: algunos labs enseñan específicamente a modificar una **response** antes de que llegue al navegador.

Ejemplo conceptual:

```text
Set-Cookie: Admin=false
        ↓ intercept response
Set-Cookie: Admin=true
```

No convertir automáticamente todo en Repeater si el objetivo de entrenamiento es practicar interceptación de responses.

## Organización durante una sesión

Nombres útiles de tabs de Repeater:

```text
BASELINE
AUTH
ADMIN
SQLI
SSRF
TRAVERSAL
UPLOAD
OAST
```

Guardar una request limpia y duplicarla antes de modificaciones grandes.

## Regla final

Burp ayuda a responder:

```text
¿Qué cambia si controlo esta parte de la request?
```

No usar herramientas sin una hipótesis concreta.
