# Authentication Lab 2 — 2FA simple bypass

> Procedimiento basado en la solución oficial actual de PortSwigger.

## Objetivo

Acceder a la cuenta de `carlos` **sin introducir su código 2FA**.

PortSwigger proporciona:

```text
Tu cuenta:     wiener:peter
Víctima:       carlos:montoya
```

---

# Qué quiere enseñar este lab

Una aplicación puede pedir 2FA en una página, pero cometer el error de considerar al usuario autenticado demasiado pronto.

Flujo correcto esperado:

```text
username/password
      ↓
2FA correcto
      ↓
sesión completamente autenticada
      ↓
/my-account
```

Flujo vulnerable:

```text
username/password correctos
      ↓
sesión ya acepta recursos protegidos
      ↓
página /login2 pide código
      ↓
pero /my-account no verifica 2FA
```

---

# Parte A — Aprender la URL de la cuenta

1. Iniciar sesión con:

```text
wiener:peter
```

2. La aplicación pedirá el código 2FA.
3. Pulsar el botón **Email client** del laboratorio.
4. Abrir el correo recibido.
5. Copiar el código 2FA.
6. Volver al lab e introducirlo.
7. Entrar a **My account**.
8. Observar la URL de la cuenta.

PortSwigger indica tomar nota de ella. Normalmente será:

```text
/my-account
```

9. Hacer **Log out**.

---

# Parte B — Iniciar el login de Carlos

1. Hacer login con:

```text
carlos:montoya
```

2. Llegaremos a la pantalla que solicita el código 2FA.

**No tenemos el código de Carlos.**

Aquí está la vulnerabilidad.

---

# Parte C — Bypass por forced browsing

Mientras estás en la pantalla de 2FA, modificar manualmente la barra de direcciones y navegar a:

```text
/my-account
```

No envíes un código.

Si la implementación es vulnerable, la página de Carlos cargará directamente y el lab quedará:

```text
Solved
```

---

# ¿Necesito Burp en este lab?

La solución oficial de PortSwigger puede hacerse directamente desde el navegador.

No hay que inventar un uso obligatorio de Repeater o Intruder.

Sin embargo, para aprender Burp conviene observar el flujo en:

```text
Proxy
→ HTTP history
```

Deberías poder identificar conceptualmente:

```text
POST /login
→ credenciales correctas

GET /login2
→ pantalla 2FA

GET /my-account
→ recurso protegido que debería verificar 2FA
```

La vulnerabilidad es que el último endpoint permite el acceso aunque el segundo factor no se haya completado.

---

# Concepto nuevo — Forced browsing

**Forced browsing** consiste en acceder directamente a una URL o recurso sin seguir el flujo de navegación que la aplicación espera.

Aquí:

```text
flujo esperado:
/login → /login2 → /my-account

flujo atacante:
/login → /login2
          ↓
       escribir /my-account manualmente
```

El problema no es poder escribir una URL manualmente.

El problema es que el servidor de `/my-account` **no vuelve a comprobar que el 2FA fue completado**.

---

# Checklist

- [ ] Entré como `wiener`.
- [ ] Recuperé mi código desde Email client.
- [ ] Anoté `/my-account`.
- [ ] Cerré sesión.
- [ ] Introduje `carlos:montoya`.
- [ ] Llegué a la pantalla 2FA.
- [ ] No introduje ningún código.
- [ ] Navegué directamente a `/my-account`.
- [ ] Entiendo por qué esto es un fallo de autenticación y no simplemente una URL oculta.
- [ ] Puedo explicar qué comprobación debería realizar el servidor.
