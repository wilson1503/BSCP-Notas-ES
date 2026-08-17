# Reconocimiento y enumeración rápida

Recopilación orientada a no perder tiempo al inicio de cada aplicación. Inspirada en botesjuan/nazori, DingyShark, DCKento y n3oari.

## 1. Navegación inicial

Antes de lanzar payloads:

- recorrer Home, productos, login, registro, My account y funciones visibles;
- abrir páginas de producto y formularios;
- identificar parámetros GET/POST/JSON;
- observar cookies nuevas;
- revisar redirects;
- revisar respuestas y JavaScript cargado;
- generar suficiente tráfico para trabajar desde **Proxy → HTTP history**.

## 2. Archivos/rutas de descubrimiento baratos

Comprobar cuando tenga sentido:

```text
/robots.txt
/sitemap.xml
/.git/
/backup
/backup/
/.well-known/
```

Además:

- HTML source;
- comentarios de desarrollador;
- archivos `.js`;
- rutas reveladas en scripts;
- endpoints llamados por AJAX/fetch;
- nombres de parámetros interesantes.

## 3. Palabras que merecen atención en HTTP history

```text
admin
account
user
username
role
id
uid
password
reset
token
api
graphql
stock
url
image
filename
file
path
upload
import
xml
template
report
pdf
redirect
callback
search
query
email
```

## 4. Señales por funcionalidad

### Login / auth

Buscar:

- mensajes diferentes para usuario inexistente vs contraseña incorrecta;
- diferencias de tiempo;
- bloqueo por IP;
- `X-Forwarded-For`;
- cookies `remember`, `stay-logged-in`, role/admin;
- 2FA y parámetros que identifiquen al usuario;
- password reset tokens;
- Host usado para generar enlaces.

### Perfil / cuenta

Buscar:

```text
id=
user=
username=
role=
admin=
isAdmin=
```

También JSON con propiedades que la UI no muestra.

### Search / comentarios

Buscar:

- reflexión del input;
- contexto HTML/attribute/JS;
- DOM sources/sinks;
- SQL-like errors;
- comportamiento de cache.

### Stock checker / URL fetcher

Buscar:

```text
stockApi=
url=
uri=
endpoint=
```

Probar primero si el servidor hace la petición; después razonar SSRF.

### Upload

Registrar:

- extensión;
- Content-Type;
- magic bytes;
- filename;
- ruta final;
- si se renombra;
- si el archivo es accesible;
- si el servidor lo interpreta o solo lo sirve.

### XML / import

Señales:

```text
Content-Type: application/xml
text/xml
<?xml
```

Pensar en XXE/XInclude cuando corresponda.

### Template / report / PDF / image processing

Pensar en:

- SSTI;
- SSRF;
- command injection;
- file path handling.

## 5. Source code y JavaScript

Regla rápida de varias guías comunitarias:

> Si algo está oculto en la UI, buscar primero si el navegador ya recibió la información.

Palabras útiles al buscar en JS:

```text
admin
isAdmin
role
fetch
XMLHttpRequest
postMessage
addEventListener
location
innerHTML
document.write
eval
JSON.parse
redirect
api
```

Para DOM, revisar sources/sinks y utilizar **DOM Invader** cuando el tema ya esté dominado.

## 6. Scanner dirigido

Varias guías de candidatos recomiendan **targeted scanning** para ganar tiempo, no un escaneo indiscriminado de todo.

Idea:

```text
request interesante
    ↓
identificar insertion point
    ↓
scan selected insertion point / targeted scan
    ↓
validar manualmente findings
```

Especialmente útil como apoyo para:

- SQLi;
- XSS;
- Path Traversal;
- XXE;
- SSTI;
- OS Command Injection;
- Host Header issues.

El scanner **no sustituye** la comprensión ni todos los bugs de lógica.

## 7. Baseline antes de mutar

Para cada request prometedora guardar mentalmente:

```text
status
length
redirect
body clave
cookies
response time
```

Después cambiar **una sola cosa**.

## 8. Señales de que estás en un rabbit hole

- llevas varios payloads sin ningún cambio de comportamiento;
- no sabes qué parser/sink estás intentando atacar;
- el objetivo de la fase no encaja con la técnica;
- estás automatizando antes de confirmar una hipótesis;
- sigues modificando cinco cosas a la vez;
- no has revisado HTML/JS/HTTP history básico.

En ese punto: volver a enumerar.
