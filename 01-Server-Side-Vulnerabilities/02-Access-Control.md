# 02 — Access Control

> **Método de estudio:** este archivo sigue primero el orden, conceptos y workflows de **PortSwigger Web Security Academy**. Si existe una solución oficial para un lab, ese procedimiento es el que se estudia primero. Las alternativas se reservarán para la futura sección de preparación para examen.

## Qué es Access Control

En una aplicación web, PortSwigger separa tres ideas relacionadas:

- **Authentication** → confirma que el usuario es quien dice ser.
- **Session management** → permite identificar qué peticiones HTTP posteriores pertenecen a ese usuario.
- **Access control / Authorization** → decide si ese usuario tiene permiso para ejecutar una acción o acceder a un recurso.

Una vulnerabilidad de control de acceso aparece cuando un usuario puede acceder a recursos o realizar acciones que no debería tener permitidas.

La pregunta central es:

> **¿El servidor comprueba correctamente que este usuario está autorizado para esta acción o recurso?**

---

# Orden oficial del bloque Apprentice

En el learning path **Server-side vulnerabilities — Apprentice**, PortSwigger presenta Access Control en este orden:

1. What is access control?
2. Vertical privilege escalation
3. Unprotected functionality
4. **Lab: Unprotected admin functionality**
5. Unprotected functionality — Continued
6. **Lab: Unprotected admin functionality with unpredictable URL**
7. Parameter-based access control methods
8. **Lab: User role controlled by request parameter**
9. Horizontal privilege escalation
10. **Lab: User ID controlled by request parameter, with unpredictable user IDs**
11. Horizontal to vertical privilege escalation
12. **Lab: User ID controlled by request parameter with password disclosure**

Este es el orden que debemos seguir mientras hacemos la ruta Apprentice.

---

# 1. Vertical privilege escalation

Ocurre cuando un usuario con menos privilegios obtiene acceso a funciones reservadas para un rol superior.

Ejemplo:

```text
usuario normal → función de administrador
```

Un caso típico es un panel administrativo que existe en una URL como:

```text
/admin
/administrator-panel
```

Ocultar el enlace de la interfaz **no protege el endpoint**. La autorización debe ser aplicada por el servidor.

---

# 2. Unprotected functionality

Una aplicación puede tener funciones sensibles que no están realmente protegidas. El usuario no ve el enlace, pero puede navegar directamente a la URL si consigue descubrirla.

PortSwigger enseña que una ruta administrativa puede filtrarse, por ejemplo, mediante:

```text
/robots.txt
```

## Lab oficial 1 — Unprotected admin functionality

### Objetivo

Encontrar el panel administrativo y eliminar a `carlos`.

### Workflow oficial

1. Abrir el lab.
2. Añadir `/robots.txt` al dominio.
3. Revisar la línea `Disallow`.
4. Observar que revela la ruta del panel, por ejemplo:

```text
Disallow: /administrator-panel
```

5. Sustituir `/robots.txt` por `/administrator-panel` en la barra del navegador.
6. Abrir el panel.
7. Eliminar a `carlos`.

### Qué estaba enseñando PortSwigger

`robots.txt` puede revelar una ruta, pero **no es un mecanismo de autorización**.

```text
robots.txt
    ↓
revela /administrator-panel
    ↓
acceso directo funciona
    ↓
servidor no exige rol admin
    ↓
vertical privilege escalation
```

---

# 3. Unprotected functionality — unpredictable URL

Una aplicación puede intentar ocultar una función administrativa usando una URL difícil de adivinar:

```text
/administrator-panel-yb556
```

Esto es un ejemplo de **security by obscurity**.

La URL puede seguir siendo enviada al navegador dentro del HTML o JavaScript, aunque la interfaz no muestre el enlace al usuario normal.

Ejemplo conceptual:

```javascript
var isAdmin = false;
if (isAdmin) {
    var adminPanelTag = document.createElement('a');
    adminPanelTag.setAttribute('href', '/administrator-panel-yb556');
}
```

El navegador recibe ese JavaScript, por lo que el usuario puede inspeccionarlo.

## Lab oficial 2 — Unprotected admin functionality with unpredictable URL

### Workflow oficial

1. Revisar el **source** de la página principal usando Burp Suite o las herramientas de desarrollo del navegador.
2. Encontrar JavaScript que revele la URL del panel administrativo.
3. Cargar esa URL.
4. Eliminar a `carlos`.

### Regla para recordar

```text
Difícil de adivinar ≠ protegido
```

La seguridad no debe depender únicamente de que una URL sea secreta.

---

# 4. Parameter-based access control methods

PortSwigger explica que algunas aplicaciones determinan el rol al iniciar sesión y almacenan esa información en una ubicación **controlable por el usuario**.

Puede ser:

- un **hidden field**;
- una **cookie**;
- un **preset query string parameter**.

Ejemplos conceptuales:

```text
?admin=true
?role=1
```

```http
Cookie: Admin=true
```

El problema aparece cuando el servidor confía en ese valor para tomar decisiones de autorización.

## Lab oficial 3 — User role controlled by request parameter

### Datos proporcionados por el lab

```text
wiener:peter
```

Panel administrativo:

```text
/admin
```

Objetivo: acceder al panel y eliminar a `carlos`.

### Workflow oficial de PortSwigger

Este laboratorio es importante porque enseña específicamente **response interception en Burp Proxy**.

1. Navegar a:

```text
/admin
```

2. Comprobar que no tenemos acceso.
3. Abrir la página de login.
4. En **Burp Proxy**, activar la interceptación.
5. Activar también **response interception**.
6. Completar y enviar el login con:

```text
wiener:peter
```

7. Hacer **Forward** de la request de login.
8. Interceptar la **response** del servidor.
9. Observar:

```http
Set-Cookie: Admin=false
```

10. Modificar la response antes de que llegue al navegador:

```http
Set-Cookie: Admin=true
```

11. Hacer **Forward**.
12. El navegador almacena ahora la cookie modificada.
13. Cargar `/admin` normalmente.
14. Eliminar a `carlos`.

### Qué debemos aprender de este lab

No basta con recordar `Admin=false → Admin=true`.

El flujo importante es:

```text
POST /login
     ↓
Response del servidor
     ↓
Set-Cookie: Admin=false
     ↓
Burp Proxy intercepta RESPONSE
     ↓
Admin=true
     ↓
Browser almacena la cookie
     ↓
GET /admin
```

### Request vs Response interception

```text
Request interception
Browser ──[request]──> Burp ──> Server

Response interception
Browser <── Burp <──[response]── Server
```

En este laboratorio, PortSwigger quiere que practiquemos **la segunda**.

> Una alternativa mediante Repeater puede demostrar la misma vulnerabilidad, pero no sustituye el workflow oficial de estudio de este lab.

---

# 5. Horizontal privilege escalation

Ocurre cuando un usuario puede acceder a recursos de **otro usuario que tiene un nivel de privilegios equivalente**.

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

puede permitir acceder a otro recurso si el servidor no comprueba que el objeto pertenece al usuario autenticado.

Los identificadores pueden ser:

```text
1
1234
carlos
usuario@email.com
UUID
```

Un identificador impredecible puede dificultar descubrir otro objeto, pero **no reemplaza el control de acceso**.

## Siguiente lab oficial

**User ID controlled by request parameter, with unpredictable user IDs**

Cuando lleguemos a este lab, documentaremos el procedimiento exacto de PortSwigger después de estudiarlo y resolverlo.

---

# 6. Horizontal to vertical privilege escalation

Una vulnerabilidad horizontal puede terminar produciendo una escalación vertical.

Ejemplo conceptual:

```text
acceso a datos de otro usuario
        ↓
el otro usuario tiene privilegios mayores
        ↓
obtengo información sensible o control de su cuenta
        ↓
escalación vertical
```

## Lab Apprentice posterior

**User ID controlled by request parameter with password disclosure**

Se documentará usando el workflow oficial cuando lleguemos a él.

---

# Temas de Access Control que veremos más adelante

PortSwigger también cubre técnicas adicionales fuera de este primer bloque Apprentice, entre ellas:

- IDOR;
- platform misconfiguration;
- URL-based access controls;
- `X-Original-URL` y routing;
- controles dependientes del método HTTP;
- discrepancias en URL matching;
- procesos de varios pasos;
- controles basados en `Referer`;
- controles basados en ubicación.

No adelantaremos sus workflows como si fueran parte del lab actual. Se añadirán siguiendo el orden del material oficial cuando corresponda.

---

# Workflow general de análisis

Este workflow sirve como orientación, pero **el procedimiento específico de cada lab tiene prioridad**.

```text
1. Entender qué función/recurso debería estar restringido.
2. Navegar normalmente y observar el comportamiento.
3. Revisar requests y responses en Burp.
4. Identificar qué dato utiliza la aplicación para tomar la decisión.
5. Seguir la técnica indicada por el concepto/lab.
6. Comparar el comportamiento autorizado y no autorizado.
7. Entender por qué el servidor permitió la acción.
```

## Qué mirar en una request/response

- URL y endpoint;
- método HTTP;
- parámetros;
- cookies;
- headers;
- body;
- status code;
- redirects;
- contenido del response;
- información enviada por JavaScript.

---

# Método mental

```text
¿Quién soy?
      ↓
¿Qué recurso o acción intento usar?
      ↓
¿Debería tener permiso?
      ↓
¿Qué usa la aplicación para decidirlo?
      ↓
¿Ese dato puede ser manipulado o el control está ausente?
      ↓
¿El servidor vuelve a validar la autorización?
```

---

# Checklist Apprentice

- [x] Entiendo qué es vertical privilege escalation.
- [x] Entiendo por qué ocultar `/admin` no protege el endpoint.
- [x] Entiendo qué revela `robots.txt` y qué NO hace.
- [x] Entiendo Security by Obscurity.
- [x] Sé que una ruta puede filtrarse mediante JavaScript.
- [x] Entiendo los parameter-based access control methods.
- [ ] Sé usar **response interception** en Burp Proxy con soltura.
- [ ] Entiendo horizontal privilege escalation.
- [ ] Sé trabajar con identificadores impredecibles.
- [ ] Entiendo horizontal → vertical privilege escalation.

---

# Registro de labs

## Lab 1 — Unprotected admin functionality

- Estado: ✅ resuelto.
- Concepto clave: ruta administrativa expuesta mediante `robots.txt`.
- Método: solución oficial de PortSwigger.

## Lab 2 — Unprotected admin functionality with unpredictable URL

- Estado: ✅ resuelto.
- Concepto clave: URL administrativa filtrada mediante JavaScript / Security by Obscurity.
- Método: solución oficial de PortSwigger.

## Lab 3 — User role controlled by request parameter

- Concepto clave: cookie de rol manipulable.
- Técnica que debe dominarse: **Burp Proxy response interception**.
- Método de estudio: modificar `Set-Cookie: Admin=false` a `Admin=true` en la response antes de que llegue al navegador.

---

# Regla rápida

**No basta con conseguir `Solved`: tengo que poder reproducir y explicar la técnica que PortSwigger estaba enseñando.**
