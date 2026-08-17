# 02 — Access Control

> **Método de estudio:** este archivo sigue primero el orden, conceptos y workflows de **PortSwigger Web Security Academy**. Si existe una solución oficial para un lab, ese procedimiento se documenta primero. Los atajos y métodos alternativos quedan separados para la sección de preparación del examen.

## Qué es Access Control

PortSwigger separa tres ideas:

- **Authentication** → confirma quién eres.
- **Session management** → identifica qué requests posteriores pertenecen a esa sesión.
- **Access control / Authorization** → decide qué recursos y acciones puede usar ese usuario.

Pregunta central:

> **¿El servidor comprueba que este usuario está autorizado para ESTE recurso o acción?**

---

# Orden oficial del bloque Apprentice

1. What is access control?
2. Vertical privilege escalation
3. Unprotected functionality
4. **Lab: Unprotected admin functionality**
5. Unprotected functionality — Continued
6. **Lab: Unprotected admin functionality with unpredictable URL**
7. Parameter-based access control methods
8. **Lab: User role controlled by request parameter**
9. Horizontal privilege escalation
10. **Lab: User ID controlled by request parameter, with unpredictable user IDs** ← **actual**
11. Horizontal to vertical privilege escalation
12. **Lab: User ID controlled by request parameter with password disclosure**

---

# 1. Vertical privilege escalation

Un usuario de menor privilegio consigue acceder a funcionalidad reservada para un rol superior.

```text
usuario normal → función de administrador
```

## Lab 1 — Unprotected admin functionality ✅

### Objetivo

Encontrar el panel administrativo y eliminar a `carlos`.

### Workflow oficial

1. Abrir el lab.
2. Visitar:

```text
/robots.txt
```

3. Revisar `Disallow`.
4. Encontrar una ruta como:

```text
/administrator-panel
```

5. Abrir esa ruta directamente.
6. Eliminar a `carlos`.

### Lección

```text
robots.txt revela una ruta
≠
robots.txt protege una ruta
```

---

# 2. Unprotected functionality — unpredictable URL

Una URL difícil de adivinar tampoco constituye control de acceso.

```text
/administrator-panel-yb556
```

Puede filtrarse en HTML o JavaScript enviado al navegador.

## Lab 2 — Unprotected admin functionality with unpredictable URL ✅

### Workflow oficial

1. Revisar el source/JavaScript de la página.
2. Encontrar la URL administrativa embebida en el script.
3. Abrirla.
4. Eliminar a `carlos`.

### Lección

```text
Security by Obscurity ≠ Authorization
```

---

# 3. Parameter-based access control methods

Una aplicación puede confiar en información controlable por el cliente para decidir el rol:

- hidden field;
- cookie;
- query parameter.

Ejemplos:

```text
?admin=true
?role=1
```

```http
Cookie: Admin=true
```

## Lab 3 — User role controlled by request parameter ✅

Credenciales proporcionadas:

```text
wiener:peter
```

Panel:

```text
/admin
```

### Workflow oficial de PortSwigger

Este lab enseña específicamente **response interception**.

1. Visitar `/admin` y comprobar que no tenemos acceso.
2. Ir al login.
3. En **Burp → Proxy**, activar interception.
4. Activar también **response interception**.
5. Enviar el login con `wiener:peter`.
6. Hacer Forward de la request.
7. Interceptar la response.
8. Encontrar:

```http
Set-Cookie: Admin=false
```

9. Modificarla a:

```http
Set-Cookie: Admin=true
```

10. Forward.
11. El navegador guarda `Admin=true`.
12. Abrir `/admin`.
13. Eliminar a `carlos`.

### Request vs Response interception

```text
REQUEST
Browser ──> Burp ──> Server

RESPONSE
Browser <── Burp <── Server
```

---

# 4. Horizontal privilege escalation

Ocurre cuando un usuario accede a recursos pertenecientes a **otro usuario del mismo nivel de privilegios**.

Ejemplo conceptual:

```text
/my-account?id=123
```

Cambiar:

```text
id=123
```

por:

```text
id=124
```

puede permitir acceder a otro usuario si el servidor no comprueba la propiedad del recurso.

Los identificadores pueden ser números, usernames, emails o GUID/UUID.

Un GUID difícil de adivinar **no sustituye la autorización** si la aplicación lo revela en otro lugar.

---

# Lab 4 — User ID controlled by request parameter, with unpredictable user IDs ⏳

> **Guía detallada:** [abrir procedimiento completo paso a paso](../Labs/Access-Control/04-User-ID-controlled-by-request-parameter-unpredictable-GUIDs.md)

## Objetivo

```text
Encontrar GUID de carlos
→ acceder a su cuenta
→ obtener su API key
→ Submit solution
```

Credenciales:

```text
wiener:peter
```

## Workflow oficial de PortSwigger

### Paso 1 — Encontrar el GUID de `carlos`

1. Navegar por los blog posts.
2. Encontrar una publicación escrita por `carlos`.
3. Hacer clic sobre el nombre `carlos`.
4. Observar que la URL contiene su user ID/GUID.
5. Copiar el GUID.

```text
blog post
→ carlos
→ URL contiene GUID
→ guardar GUID
```

### Paso 2 — Login

Entrar a **My account** con:

```text
wiener:peter
```

### Paso 3 — Identificar el parámetro

La cuenta propia utiliza una URL similar a:

```text
/my-account?id=GUID_DE_WIENER
```

### Paso 4 — Cambiar solamente `id`

Sustituir:

```text
id=GUID_DE_WIENER
```

por:

```text
id=GUID_DE_CARLOS
```

### Paso 5 — Obtener API key

Si el control de acceso falla, la aplicación mostrará la cuenta de `carlos`.

Copiar su API key.

### Paso 6 — Submit solution

Enviar la API key de `carlos` mediante **Submit solution**.

---

## Qué usar de Burp en este lab

El workflow oficial se puede completar desde el navegador, pero mientras lo hacemos debemos practicar Burp para aprender a leer las requests.

### Proxy → HTTP history

Mantener abierto:

```text
Proxy
→ HTTP history
```

Cuando abrimos nuestra cuenta debemos localizar algo parecido a:

```http
GET /my-account?id=GUID_DE_WIENER HTTP/2
```

Debemos poder identificar:

```text
Endpoint = /my-account
Parámetro = id
Valor = GUID_DE_WIENER
```

### Repeater — práctica complementaria

> Esto es **opcional para aprender Burp** y no sustituye el método oficial.

1. Clic derecho sobre `GET /my-account?id=...`.
2. **Send to Repeater**.
3. Ir a **Repeater**.
4. Cambiar solo el valor de `id` por el GUID de `carlos`.
5. **Send**.
6. Revisar la Response y buscar:

```text
carlos
API key
```

---

## Qué está enseñando este lab

```text
GUID impredecible
      ↓
no puedo adivinarlo fácilmente
      ↓
pero la app lo expone en los blog posts
      ↓
obtengo GUID de carlos
      ↓
lo uso en /my-account?id=
      ↓
servidor no comprueba ownership
      ↓
Horizontal Privilege Escalation
```

### Relación con IDOR

Este patrón es un ejemplo típico relacionado con **IDOR**: una referencia controlada por el usuario permite acceder directamente a otro objeto sin autorización adecuada.

El fallo real no es que exista un GUID.

El fallo es:

```text
usuario autenticado = wiener
objeto solicitado    = cuenta de carlos
servidor             = la devuelve igualmente
```

---

# 5. Horizontal → vertical privilege escalation

Una escalación horizontal puede terminar comprometiendo un usuario de mayor privilegio.

```text
acceso a cuenta ajena
        ↓
cuenta privilegiada
        ↓
dato sensible / contraseña / función
        ↓
vertical privilege escalation
```

## Próximo lab

**User ID controlled by request parameter with password disclosure**

Cuando lo abramos, añadiremos **antes de resolverlo**:

- objetivo exacto;
- procedimiento oficial de PortSwigger;
- clics en Burp necesarios;
- request/response que debemos reconocer;
- qué resultado esperamos;
- por qué funciona.

---

# Regla nueva para TODOS los labs

A partir de este punto, ningún lab se deja solamente con teoría.

Cada laboratorio debe tener en GitHub:

```text
1. Objetivo
2. Datos que proporciona PortSwigger
3. Procedimiento oficial paso a paso
4. Qué botones/pestañas usar en Burp
5. Request o response importante
6. Qué valor modificar
7. Resultado esperado
8. Explicación de por qué funciona
9. Checklist de lo que tengo que aprender
```

Si el lab oficial **no necesita Burp**, se indicará claramente y añadiremos una práctica complementaria de Burp solo cuando ayude a aprender la herramienta sin reemplazar el workflow oficial.

---

# Qué debo dominar en Burp durante Access Control

- [ ] Proxy → Intercept.
- [ ] Request interception.
- [ ] Response interception.
- [ ] Proxy → HTTP history.
- [ ] Leer método, endpoint y query parameters.
- [ ] Identificar cookies.
- [ ] Send to Repeater.
- [ ] Modificar un parámetro en Repeater.
- [ ] Comparar Request y Response.
- [ ] Entender cuándo PortSwigger pide Burp y cuándo el navegador es suficiente.

---

# Registro de labs

## Lab 1 — Unprotected admin functionality

- Estado: ✅ resuelto.
- Concepto: función admin sin protección / `robots.txt`.

## Lab 2 — Unprotected admin functionality with unpredictable URL

- Estado: ✅ resuelto.
- Concepto: Security by Obscurity / ruta filtrada en JavaScript.

## Lab 3 — User role controlled by request parameter

- Estado: ✅ resuelto.
- Concepto: cookie de rol manipulable.
- Técnica Burp: **response interception**.

## Lab 4 — User ID controlled by request parameter, with unpredictable user IDs

- Estado: ⏳ en progreso.
- Concepto: horizontal privilege escalation con GUID filtrado en otra funcionalidad.
- Parámetro clave: `id`.
- Objetivo final: API key de `carlos`.
- [Procedimiento completo](../Labs/Access-Control/04-User-ID-controlled-by-request-parameter-unpredictable-GUIDs.md)

---

# Regla rápida

**No basta con conseguir `Solved`: tengo que saber qué request/response estaba usando, dónde estaba el dato controlable y cómo reproducir el workflow que PortSwigger estaba enseñando.**
