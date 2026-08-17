# Cheat Sheet — Fase 1: Foothold / acceso inicial

Objetivo oficial:

```text
Acceder a cualquier cuenta de usuario.
```

## Prioridad rápida

```text
1. Authentication / password reset / 2FA
2. XSS / DOM
3. HTTP Request Smuggling
4. Web Cache Poisoning / Deception
5. Host Header
6. OAuth
7. API / content discovery
8. CSRF/CORS según funcionalidad
```

La superficie real manda sobre este orden.

---

## Recon de 3–5 minutos

- [ ] recorrer aplicación completa;
- [ ] login / register / forgot password;
- [ ] search / comments;
- [ ] `robots.txt`, `sitemap.xml`;
- [ ] HTML source + JS;
- [ ] cookies;
- [ ] HTTP history;
- [ ] endpoints API;
- [ ] identificar víctima/admin/user names si aparecen;
- [ ] targeted scan de insertion points prometedores.

---

## Authentication

### Username enumeration

Comparar:

```text
mensaje
status
length
timing
```

Si hay bloqueo por IP, investigar si la aplicación confía en:

```http
X-Forwarded-For: 127.0.0.1
```

### Remember-me

Decodificar primero:

```text
Base64?
username:hash?
MD5?
```

### 2FA

Buscar si un parámetro elige el usuario:

```text
verify=
username=
mfa-code=
```

### Forgot password

Revisar:

```text
reset token
Host
X-Forwarded-Host
redirects
API fields
token reuse
```

---

## XSS / DOM

### Señales

```text
search
comment
profile fields
location.search
document.write
innerHTML
eval
postMessage
JSON.parse
ng-app
```

### POC mínima

```html
<script>alert(1)</script>
<img src=x onerror=alert(1)>
```

No basta para completar fase: el finding debe convertirse en acceso útil si ese es el vector.

Antes de diseñar ataque cross-user:

- [ ] probar en tu navegador;
- [ ] verificar HttpOnly;
- [ ] revisar secuencia HTTP;
- [ ] adaptar al contexto exacto.

---

## Host Header

Especialmente interesante en:

```text
Forgot password
absolute links
cache
routing
```

Headers:

```http
Host:
X-Forwarded-Host:
```

---

## Web Cache

Revisar:

```http
Age:
X-Cache:
Cache-Control:
Vary:
```

Pregunta:

> ¿Qué input influye en la response pero NO forma parte de la cache key?

---

## HTTP Request Smuggling

No empezar aquí sin haber estudiado Practitioner.

Cuando existan señales de desync:

- revisar HTTP version;
- CL/TE behavior;
- HTTP/2 downgrade;
- usar HTTP Request Smuggler/Scanner como apoyo;
- confirmar con POC controlada.

Objetivos posibles de Fase 1:

```text
capturar request de otro usuario
poison response
encadenar XSS
```

---

## OAuth

Checklist:

- [ ] redirect URI;
- [ ] state;
- [ ] account linking;
- [ ] identidad/email confiada;
- [ ] code/token leakage;
- [ ] endpoints secundarios.

---

## API

Buscar:

```text
/api/
/v1/
/v2/
/graphql
swagger
openapi
```

Revisar:

- métodos;
- parámetros ocultos;
- fields no visibles;
- datos de usuarios;
- autorización.

---

## Si te atascas

```text
¿Mapeé toda la funcionalidad?
¿Miré JS/source?
¿Comparé login responses?
¿Hice targeted scan?
¿Estoy intentando una técnica que realmente puede dar una sesión?
```

No saltar a Fase 2 hasta tener cuenta válida.
