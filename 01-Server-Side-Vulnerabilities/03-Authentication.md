# 03 — Authentication

> **Método de estudio:** seguir primero el orden y los workflows oficiales de PortSwigger Web Security Academy. Los atajos para examen quedan separados en `Preparacion-Examen/`.

## Estado del bloque

**✅ Authentication — Apprentice completado.**

Labs realizados:

- ✅ Username enumeration via different responses.
- ✅ 2FA simple bypass.

---

## Qué es Authentication

**Authentication** verifica que un usuario es quien afirma ser. No es lo mismo que **Authorization / Access Control**, que decide qué puede hacer un usuario una vez autenticado.

PortSwigger agrupa los mecanismos de autenticación en factores como:

- algo que sabes: contraseña, respuesta secreta;
- algo que tienes: teléfono, token;
- algo que eres/haces: biometría o comportamiento.

Una vulnerabilidad de autenticación puede permitir acceder a una cuenta sin conocer correctamente todos los factores que deberían ser necesarios.

---

# Orden oficial en Server-side vulnerabilities — Apprentice

El bloque Authentication contiene 10 elementos:

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

---

# 1. Brute-force attacks

Un brute-force attack consiste en probar repetidamente valores hasta encontrar credenciales válidas.

No siempre significa probar todas las combinaciones posibles. Las wordlists y patrones humanos reducen mucho el espacio de búsqueda.

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

Las diferencias pueden aparecer en:

```text
status code
error message
response length
response timing
```

---

# Lab 1 — Username enumeration via different responses ✅

> [Procedimiento completo paso a paso](../Labs/Authentication/01-Username-enumeration-via-different-responses.md)

## Workflow aprendido

```text
POST /login
    ↓
Send to Intruder
    ↓
username=§payload§
    ↓
Simple list de usernames
    ↓
comparar Length / mensaje
    ↓
username válido
    ↓
username fijo + password=§payload§
    ↓
Simple list de passwords
    ↓
buscar Status 302
    ↓
login correcto
```

## Qué aprendimos de Burp

- `Send to Intruder`;
- payload positions `§...§`;
- `Clear §` / `Add §`;
- attack type **Sniper**;
- payload type **Simple list**;
- ordenar resultados por `Length` y `Status`;
- comparar respuestas.

---

# 3. Two-factor authentication / 2FA

2FA pretende requerir dos factores distintos.

Un fallo aparece cuando la aplicación valida el segundo factor en una pantalla concreta, pero los recursos protegidos no comprueban que el proceso completo haya terminado.

---

# Lab 2 — 2FA simple bypass ✅

> [Procedimiento completo paso a paso](../Labs/Authentication/02-2FA-simple-bypass.md)

## Workflow aprendido

```text
username + password correctos
        ↓
servidor crea estado parcialmente autenticado
        ↓
/login2 pide código 2FA
        ↓
/my-account no comprueba que 2FA terminó
        ↓
forced browsing a /my-account
        ↓
2FA bypass
```

La lección principal es que **cada recurso protegido debe verificar que el proceso de autenticación completo se haya satisfecho**.

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
¿o solamente la pantalla de 2FA lo comprueba?
```

---

# Checklist Apprentice

- [x] Entiendo Authentication vs Authorization.
- [x] Entiendo qué es brute force.
- [x] Entiendo username enumeration.
- [x] Sé reconocer diferencias por mensaje, length, status y timing.
- [x] Sé enviar `POST /login` a Intruder.
- [x] Sé usar payload positions.
- [x] Sé usar Sniper + Simple list.
- [x] Sé interpretar un ataque de Intruder.
- [x] Entiendo por qué se enumera primero el username.
- [x] Entiendo qué es un 2FA bypass por forced browsing.
- [x] Lab 1 completado.
- [x] Lab 2 completado.

---

# Registro de labs

## Lab 1 — Username enumeration via different responses

- Estado: ✅ resuelto.
- Herramienta principal: **Burp Intruder**.
- Concepto: username enumeration + brute force de password.

## Lab 2 — 2FA simple bypass

- Estado: ✅ resuelto.
- Técnica: **forced browsing** después del primer factor.
- Concepto: validación incompleta del estado 2FA.

---

# Regla rápida

**En Authentication no mirar solo si el login falla: mirar exactamente CÓMO falla. Las diferencias de comportamiento pueden revelar credenciales válidas o estados de autenticación incompletos.**
