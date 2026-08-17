# 04 — Headers, cookies y sesiones

## Qué son los headers

Los headers transportan metadatos de una request o response.

Ejemplo:

```http
GET /my-account HTTP/1.1
Host: ejemplo.com
User-Agent: Mozilla/5.0
Cookie: session=abc123
Referer: https://ejemplo.com/
```

## Headers importantes para aprender

### Host

Indica el host al que va dirigida la petición.

```http
Host: ejemplo.com
```

Será especialmente importante en temas como Host Header Attacks.

### Cookie

Transporta cookies del navegador al servidor.

```http
Cookie: session=abc123
```

En muchos labs, la cookie `session` es lo que permite al servidor reconocer qué usuario está haciendo la petición.

### Referer

Puede indicar desde qué página se originó una navegación o acción.

```http
Referer: https://ejemplo.com/account
```

No debe usarse como único mecanismo de autorización.

### Origin

Indica el origen de ciertas solicitudes del navegador y será importante para CORS y CSRF.

### Content-Type

Describe el formato del body.

Ejemplos:

```text
application/x-www-form-urlencoded
application/json
multipart/form-data
```

## Cookies y sesión

HTTP por sí solo es stateless: cada request es independiente.

Las aplicaciones suelen usar una cookie de sesión para relacionar muchas requests con un usuario autenticado:

```text
Login correcto
   ↓
Servidor crea sesión
   ↓
Set-Cookie: session=XYZ
   ↓
Navegador envía Cookie: session=XYZ
   ↓
Servidor reconoce al usuario
```

## Por qué es importante en Burp

Cuando mandas una request a Repeater, normalmente estás copiando también la cookie de sesión.

Esto te permite comparar, por ejemplo:

```text
Request con sesión de usuario A
vs
Request con sesión de usuario B
vs
Request sin sesión
```

Esto es especialmente útil para Access Control y Authentication.

## Flags de cookies que debes reconocer

```text
Secure
HttpOnly
SameSite
```

- `Secure`: el navegador envía la cookie por HTTPS.
- `HttpOnly`: dificulta que JavaScript del navegador lea la cookie.
- `SameSite`: afecta cuándo el navegador envía cookies en solicitudes cross-site.

Estas flags son importantes, pero ninguna sustituye una autorización correcta en el servidor.

## Regla mental

```text
Cookie de sesión = ¿quién soy?
Autorización del endpoint = ¿puedo hacer esto?
```

## Checklist

- [ ] Identifico `Host`, `Cookie`, `Referer`, `Origin` y `Content-Type`.
- [ ] Entiendo para qué sirve una cookie de sesión.
- [ ] Sé comparar una request con y sin sesión en Repeater.
- [ ] Reconozco `Secure`, `HttpOnly` y `SameSite`.
