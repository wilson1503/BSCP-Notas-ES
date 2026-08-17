# 03 — Authentication

> **Método de estudio:** seguir primero el orden y los workflows oficiales de PortSwigger Web Security Academy. Los atajos para examen quedan separados en `Preparacion-Examen/`.

## Qué es Authentication

**Authentication** verifica que un usuario es quien afirma ser. No es lo mismo que **Authorization / Access Control**, que decide qué puede hacer un usuario una vez autenticado.

PortSwigger agrupa los mecanismos de autenticación en factores como:

- algo que sabes: contraseña, respuesta secreta;
- algo que tienes: teléfono, token;
- algo que eres/haces: biometría o comportamiento.

Una vulnerabilidad de autenticación puede permitir acceder a una cuenta sin conocer correctamente todos los factores que deberían ser necesarios.

---

# Orden oficial en Server-side vulnerabilities — Apprentice

El bloque Authentication de este learning path contiene actualmente 10 elementos:

1. Authentication vulnerabilities
2. What is the difference between authentication and authorization?
3. Brute-force attacks
4. Brute-forcing usernames
5. Brute-forcing passwords
6. Brute-forcing passwords — Continued
7. Username enumeration
8. **Lab: Username enumeration via different responses**
9. Bypassing two-factor authentication
10. **Lab: 2FA simple bypass**

En este bloque Apprentice haremos **2 labs**.

---

# 1. Brute-force attacks

Un brute-force attack consiste en probar repetidamente valores hasta encontrar credenciales válidas.

PortSwigger remarca que no siempre se trata de probar todas las combinaciones posibles. Las wordlists y los patrones humanos reducen mucho el espacio de búsqueda.

## Qué observar en un login

- mensajes de error;
- status codes;
- longitud de la respuesta;
- tiempos de respuesta;
- redirects;
- cambios en cookies/sesión.

---

# 2. Username enumeration

**Username enumeration** ocurre cuando la aplicación se comporta de forma diferente según si el username existe o no.

Ejemplo:

```text
usuario inexistente → Invalid username
usuario existente   → Incorrect password
```

La aplicación acaba revelando que el segundo username sí existe.

Diferencias que PortSwigger recomienda observar:

```text
status code
error message
response length
response timing
```

---

# Lab 1 — Username enumeration via different responses ⏳

> [Procedimiento completo paso a paso](../Labs/Authentication/01-Username-enumeration-via-different-responses.md)

## Qué enseña

Dos fases separadas:

```text
1. Enumerar username válido
2. Fijar ese username y brute-forcear password
```

La herramienta principal es **Burp Intruder**.

El indicador del username válido es la diferencia entre respuestas como:

```text
Invalid username
```

y:

```text
Incorrect password
```

Después, al probar passwords sobre el username válido, una respuesta `302` indica el login exitoso mientras los intentos fallidos responden normalmente `200`.

---

# 3. Two-factor authentication / 2FA

2FA pretende requerir dos factores distintos. Un error frecuente de implementación es validar el segundo factor en una página concreta, pero considerar al usuario autenticado después de haber introducido correctamente username/password.

Si la aplicación permite navegar directamente a una página autenticada sin haber completado el segundo factor, existe un **2FA bypass**.

---

# Lab 2 — 2FA simple bypass

> [Procedimiento completo paso a paso](../Labs/Authentication/02-2FA-simple-bypass.md)

## Qué enseña

El flujo vulnerable es conceptualmente:

```text
username + password correctos
        ↓
servidor crea estado autenticado
        ↓
/ login2 pide 2FA
        ↓
pero /my-account no comprueba que 2FA terminó
        ↓
forced browsing a /my-account
        ↓
2FA bypass
```

PortSwigger resuelve este lab navegando directamente a `/my-account` mientras estamos en la pantalla del código 2FA de `carlos`.

---

# Burp que debemos aprender en Authentication

Este bloque es especialmente importante porque introduce **Intruder** de forma práctica.

Debemos dominar:

- `Proxy → HTTP history`;
- localizar `POST /login`;
- `Send to Intruder`;
- payload positions `§...§`;
- `Clear §` y `Add §`;
- attack type **Sniper**;
- payload type **Simple list**;
- pegar una wordlist;
- `Start attack`;
- ordenar por `Length`;
- revisar `Status`;
- abrir y comparar Responses.

---

# Método mental para un login

```text
¿Qué cambia entre un intento válido e inválido?
        ↓
¿el username existe?
        ↓
¿puedo observar mensaje / length / status / timing?
        ↓
identifico username válido
        ↓
¿puedo probar passwords de forma automatizada?
        ↓
¿qué señal confirma el login?
```

Para MFA:

```text
¿cada recurso protegido verifica que TODOS los factores se completaron?
        ↓
¿o solo la página del código 2FA hace esa comprobación?
```

---

# Checklist Apprentice

- [ ] Entiendo Authentication vs Authorization.
- [ ] Entiendo qué es brute force.
- [ ] Entiendo username enumeration.
- [ ] Sé reconocer diferencias por mensaje, length, status y timing.
- [ ] Sé enviar `POST /login` a Intruder.
- [ ] Sé usar payload positions.
- [ ] Sé usar Sniper + Simple list.
- [ ] Sé interpretar un ataque de Intruder.
- [ ] Entiendo por qué se enumera primero el username.
- [ ] Entiendo qué es un 2FA bypass por forced browsing.
- [ ] Lab 1 completado.
- [ ] Lab 2 completado.

---

# Registro de labs

## Lab 1 — Username enumeration via different responses

- Estado: ⏳ siguiente lab.
- Herramienta principal: **Burp Intruder**.
- Objetivo: encontrar un username válido, encontrar su password y entrar a su cuenta.

## Lab 2 — 2FA simple bypass

- Estado: ⬜ pendiente.
- Técnica: **forced browsing** después del primer factor.
- Objetivo: acceder a la cuenta de `carlos` sin introducir su código 2FA.

---

# Regla rápida

**En Authentication no mirar solo si el login falla: mirar exactamente CÓMO falla. Las diferencias de comportamiento pueden revelar credenciales válidas o estados de autenticación incompletos.**
