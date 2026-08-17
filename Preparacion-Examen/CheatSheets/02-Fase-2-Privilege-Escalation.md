# Cheat Sheet — Fase 2: Privilege Escalation

Objetivo oficial:

```text
Usar la cuenta obtenida para acceder a /admin,
ya sea elevando privilegios o comprometiendo al administrador.
```

## Prioridad rápida

```text
1. Broken Access Control
2. SQL Injection
3. Authentication / password reset
4. CSRF
5. JWT
6. OAuth
7. API / GraphQL
8. CORS
9. Prototype Pollution
10. NoSQL Injection
```

---

## Primero: comparar anónimo vs usuario autenticado

Después del login:

- [ ] revisar nuevas rutas;
- [ ] nuevas cookies;
- [ ] `Set-Cookie`;
- [ ] parámetros `id/user/role/admin`;
- [ ] endpoints AJAX/API;
- [ ] funciones solo visibles autenticado;
- [ ] source/JS nuevamente.

---

## Broken Access Control

### Quick tests

```text
/admin
id=wiener → id=carlos
user=wiener → user=administrator
Admin=false → Admin=true
role=user → role=admin
```

Revisar también:

```text
robots.txt
JS
UUIDs expuestos en otra funcionalidad
HTTP methods
X-Original-URL
X-Rewrite-URL
Referer
multi-step actions
```

### Regla

Ocultar UI o usar IDs impredecibles **no reemplaza autorización server-side**.

---

## SQL Injection

### POC barata

```sql
' AND 1=1-- -
' AND 1=2-- -
```

### UNION

```sql
' ORDER BY 1-- -
' UNION SELECT NULL,NULL-- -
```

### Blind/time

```sql
';SELECT pg_sleep(5)-- -
```

Al confirmar:

```text
identificar DBMS
→ localizar users table
→ obtener credencial/token requerido
→ usarlo para avanzar
```

No perder tiempo dumpeando información irrelevante.

---

## Password reset / Authentication

Aunque Fase 1 haya terminado, password reset puede ser la vía para comprometer al admin.

Revisar:

```text
username parameter
reset token
Host/X-Forwarded-Host
reutilización de token
API fields
logic flaws
```

---

## CSRF

Buscar acciones de impacto como:

```text
change email
change password
change role
account linking
```

Checklist:

- [ ] token ausente;
- [ ] quitar token;
- [ ] token de otro usuario;
- [ ] token no vinculado a sesión;
- [ ] método alternativo;
- [ ] Referer/Origin débil;
- [ ] SameSite.

Pensar en cadenas:

```text
CSRF + password/email change → account takeover
XSS + CSRF → acción privilegiada
```

---

## JWT

Inspeccionar:

```text
alg
kid
jwk
jku
sub
username
role
admin
```

Preguntas:

- ¿firma verificada?
- ¿confía en key controlable?
- ¿claims modificables?
- ¿key débil o reutilizada?

Usar Decoder/JWT tooling de Burp después de estudiar el tema.

---

## OAuth

Objetivo de Fase 2:

```text
linkear cuenta / robar code / confiar en identidad incorrecta
→ comprometer admin
```

Revisar redirect URI, state, account linking y claims de identidad.

---

## API / GraphQL

Pruebas de alta rentabilidad:

- mass assignment;
- fields ocultos (`role`, `isAdmin`);
- IDOR;
- endpoints/versiones antiguas;
- métodos no usados por la UI;
- GraphQL introspection;
- mutations privilegiadas;
- autorización por objeto.

---

## CORS

Solo vale la pena si la response sensible puede leerse cross-origin.

Confirmar combinación de:

```http
Access-Control-Allow-Origin: attacker-controlled
Access-Control-Allow-Credentials: true
```

Y que el endpoint realmente devuelva algo útil para comprometer la cuenta.

---

## Prototype Pollution

No atacar a ciegas.

```text
confirmar pollution
→ encontrar gadget
→ convertirlo en impacto útil
```

DOM Invader puede acelerar discovery cuando ya se domina el tema.

---

## Criterio de salida de Fase 2

La fase está realmente superada cuando puedes acceder a:

```text
/admin
```

No basta con demostrar que un parámetro parece vulnerable si todavía no tienes acceso administrativo.
