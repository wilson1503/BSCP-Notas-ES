# Checklist rápido por vulnerabilidad

> Índice de reconocimiento para la fase final de preparación. No reemplaza las notas de estudio.

## Content Discovery / Information Disclosure

- [ ] `robots.txt`
- [ ] `sitemap.xml`
- [ ] HTML source / comments
- [ ] JavaScript files
- [ ] `/.git/`
- [ ] backups / archivos `~`
- [ ] endpoints API ocultos
- [ ] nombres de rutas/params en responses

## Authentication

- [ ] ¿username enumeration por mensaje?
- [ ] ¿por status/length?
- [ ] ¿por timing?
- [ ] ¿bloqueo depende de IP?
- [ ] ¿`X-Forwarded-For` altera el bloqueo?
- [ ] ¿remember-me/stay-logged cookie predecible?
- [ ] ¿2FA selecciona usuario con parámetro controlable?
- [ ] ¿se puede saltar el segundo paso?
- [ ] ¿password reset token reusable/predecible/filtrable?
- [ ] ¿Host/X-Forwarded-Host afecta reset link?

## Access Control

- [ ] acceso sin login
- [ ] acceso como usuario normal
- [ ] cambiar `id/user/username`
- [ ] cambiar `role/admin/isAdmin`
- [ ] cookies de rol
- [ ] JSON fields ocultos
- [ ] método HTTP alternativo
- [ ] `X-Original-URL` / `X-Rewrite-URL`
- [ ] Referer
- [ ] pasos de workflow que no revalidan
- [ ] 30x con datos sensibles en body

## XSS / DOM

- [ ] ¿input reflejado?
- [ ] contexto: HTML / attribute / JS / URL / template literal
- [ ] ¿qué caracteres se codifican?
- [ ] source / sink DOM
- [ ] `document.write`
- [ ] `innerHTML`
- [ ] `location.search`
- [ ] `postMessage`
- [ ] `eval`
- [ ] `JSON.parse`
- [ ] Angular `ng-app`
- [ ] DOM Invader cuando aplique
- [ ] ¿cookie tiene HttpOnly?

## CSRF

- [ ] ¿acción sensible usa cookie auth?
- [ ] ¿hay token?
- [ ] quitar token
- [ ] token de otro usuario
- [ ] token vinculado a cookie/sesión
- [ ] cambiar método
- [ ] revisar SameSite
- [ ] revisar Referer/Origin
- [ ] generar POC con Burp cuando proceda

## SQL Injection

- [ ] identificar input candidato
- [ ] comilla / error
- [ ] true vs false
- [ ] ORDER BY / UNION count
- [ ] string-compatible column
- [ ] identificar DBMS
- [ ] visible/error/boolean/time/OOB
- [ ] `information_schema` o equivalente
- [ ] Intruder para blind solo después de confirmar condición
- [ ] targeted scan como apoyo
- [ ] sqlmap solo si aporta velocidad y ya entendiste el vector

## SSRF

- [ ] parámetro contiene URL/path
- [ ] callback a Collaborator
- [ ] loopback
- [ ] `/admin` interno
- [ ] blacklist/whitelist
- [ ] encoding/normalización
- [ ] open redirect útil
- [ ] protocolo http/https
- [ ] red interna si ya confirmaste SSRF
- [ ] headers backend que generen callbacks

## Path Traversal

- [ ] parámetro filename/path
- [ ] `../../../etc/passwd`
- [ ] absolute path
- [ ] nested traversal
- [ ] URL encoding
- [ ] double encoding
- [ ] prefijo obligatorio
- [ ] extensión obligatoria
- [ ] null byte si tecnología lo permite
- [ ] cambiar objetivo a `/home/carlos/secret` tras confirmar

## OS Command Injection

- [ ] input que probablemente llega a comando del SO
- [ ] `;`, `|`, `||`, `&`, `&&`
- [ ] `whoami` reflejado
- [ ] delay con `sleep`
- [ ] OAST con `nslookup`
- [ ] salida redirigible a archivo servido
- [ ] leer secret solo después de POC

## File Upload

- [ ] extensión
- [ ] Content-Type
- [ ] magic bytes
- [ ] filename/path traversal
- [ ] ¿servidor renombra?
- [ ] ¿archivo es accesible?
- [ ] ¿se ejecuta/interpreta?
- [ ] extensiones alternativas
- [ ] `.htaccess` si Apache/contexto
- [ ] metadata/polyglot si lab lo sugiere
- [ ] race condition si hay validación tardía

## XXE / XML

- [ ] XML real o parser XML implícito
- [ ] entity local file
- [ ] blind OAST
- [ ] external DTD
- [ ] XInclude cuando no controlas DOCTYPE
- [ ] import/upload XML
- [ ] objetivo final `file:///home/carlos/secret`

## SSTI

- [ ] input renderizado por template
- [ ] expresión aritmética inocua
- [ ] identificar engine
- [ ] distinguir SSTI de simple reflexión
- [ ] consultar sintaxis específica
- [ ] avanzar de detection → info disclosure → file/RCE según lab

## JWT

- [ ] header/payload/signature
- [ ] `alg`
- [ ] `kid`
- [ ] `jwk` / `jku`
- [ ] claims de rol/usuario
- [ ] verificación de firma
- [ ] claves débiles solo si contexto lo indica

## OAuth

- [ ] redirect URI validation
- [ ] state
- [ ] account linking
- [ ] email/identity trust
- [ ] token/code leakage
- [ ] endpoints auxiliares

## CORS

- [ ] reflejo arbitrario de Origin
- [ ] `null`
- [ ] subdominios confiables vulnerables
- [ ] `Access-Control-Allow-Credentials: true`
- [ ] respuesta contiene información sensible

## Host Header

- [ ] Host reflejado
- [ ] absolute URLs generadas
- [ ] password reset link
- [ ] X-Forwarded-Host
- [ ] duplicate Host behavior
- [ ] routing virtual host
- [ ] cache interaction

## Web Cache

- [ ] identificar qué se cachea
- [ ] `Age`, `X-Cache`, `Cache-Control`, `Vary`
- [ ] keyed vs unkeyed input
- [ ] headers ignorados por cache
- [ ] cache key normalization
- [ ] poisoning vs deception

## HTTP Request Smuggling

- [ ] confirmar HTTP version/backend behavior
- [ ] CL vs TE inconsistencies
- [ ] HTTP/2 downgrade cuando aplique
- [ ] usar extensiones/scanner especializado después de estudiar la técnica
- [ ] validar con POC no destructiva
- [ ] pensar en qué objetivo de Fase 1/2 puede habilitar la desync

## Prototype Pollution

- [ ] propiedades `__proto__`, `constructor.prototype`
- [ ] cambios visibles en client-side behavior
- [ ] DOM Invader
- [ ] server-side reflection/behavior
- [ ] gadgets antes de intentar impacto

## APIs / GraphQL

- [ ] documentación/endpoints
- [ ] métodos HTTP
- [ ] versionado
- [ ] parámetros ocultos
- [ ] mass assignment
- [ ] authorization por objeto
- [ ] GraphQL introspection
- [ ] fields sensibles
- [ ] aliases/batching cuando el lab lo enseñe
