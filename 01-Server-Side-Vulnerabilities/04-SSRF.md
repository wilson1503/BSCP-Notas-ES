# 04 — Server-side request forgery (SSRF)

> **Método de estudio:** seguir primero el orden, conceptos y workflows oficiales de PortSwigger Web Security Academy. Los bypasses avanzados y atajos de examen quedan separados en `Preparacion-Examen/`.

## Estado del bloque

**✅ SSRF — Apprentice completado.**

Labs realizados:

- ✅ Basic SSRF against the local server.
- ✅ Basic SSRF against another back-end system.

---

## Qué es SSRF

**Server-side request forgery (SSRF)** es una vulnerabilidad que permite hacer que la aplicación del lado servidor envíe una petición a una ubicación que el usuario no debería poder alcanzar directamente.

La idea central es:

```text
Usuario controla una URL/destino
        ↓
Servidor recibe ese valor
        ↓
Servidor hace la petición por nosotros
        ↓
La petición sale desde la red/identidad del servidor
```

Esto puede permitir acceder a:

- servicios solo disponibles localmente;
- paneles administrativos internos;
- otros sistemas de la red privada;
- datos o funciones no expuestos al usuario externo.

---

# Orden oficial en Server-side vulnerabilities — Apprentice

PortSwigger incluye 6 elementos en el bloque SSRF:

1. What is SSRF?
2. SSRF attacks against the server
3. SSRF attacks against the server — Continued
4. **Lab: Basic SSRF against the local server**
5. SSRF attacks against other back-end systems
6. **Lab: Basic SSRF against another back-end system**

---

# 1. SSRF contra el propio servidor

Una aplicación puede aceptar una URL para realizar una operación legítima, por ejemplo comprobar stock:

```text
stockApi=http://stock.example/internal
```

Si el servidor permite sustituir ese destino por:

```text
http://localhost/admin
```

entonces la petición a `/admin` no sale desde nuestro navegador: la realiza el **servidor vulnerable contra sí mismo**.

## Loopback

Dos formas comunes de referirse al propio host son:

```text
localhost
127.0.0.1
```

Esto se denomina **loopback**.

---

# Lab 1 — Basic SSRF against the local server ✅

> [Procedimiento completo paso a paso](../Labs/SSRF/01-Basic-SSRF-against-local-server.md)

## Workflow aprendido

```text
Browser → /admin
        ↓
bloqueado externamente
        ↓
Check stock
        ↓
POST /product/stock
        ↓
stockApi=http://localhost/admin
        ↓
servidor hace GET local
        ↓
HTML admin aparece en Repeater Response
        ↓
stockApi=http://localhost/admin/delete?username=carlos
```

## Lección importante

Cuando PortSwigger indica que el panel se “display”, en este workflow el HTML se observa en:

```text
Burp Repeater → Response
```

No necesariamente como navegación directa en el browser.

---

# 2. SSRF contra otros sistemas back-end

La aplicación vulnerable puede tener conectividad con otros hosts de una red privada que nosotros no podemos alcanzar directamente.

```text
Internet
   X
192.168.0.X:8080/admin
   ↑
servidor vulnerable sí tiene acceso
```

---

# Lab 2 — Basic SSRF against another back-end system ✅

> [Procedimiento completo paso a paso](../Labs/SSRF/02-Basic-SSRF-against-backend-system.md)

## Workflow aprendido

```text
POST /product/stock
        ↓
Send to Intruder
        ↓
stockApi=http://192.168.0.§X§:8080/admin
        ↓
Payload type: Numbers
1 → 255
        ↓
identificar Status 200
        ↓
IP interna válida
        ↓
Send to Repeater
        ↓
/admin/delete?username=carlos
```

Aquí **Intruder** se utilizó para variar sistemáticamente el último octeto de una IP, no para passwords.

---

# Qué buscar como superficie SSRF

Parámetros que representan destinos o URLs pueden ser interesantes, por ejemplo:

```text
url=
uri=
path=
stockApi=
endpoint=
callback=
webhook=
```

La señal importante es que **el servidor parece realizar una petición usando el valor enviado por el cliente**.

---

# Método mental para SSRF

```text
¿La aplicación recibe una URL o destino?
        ↓
¿el servidor hace una petición usando ese valor?
        ↓
¿puedo cambiar el destino?
        ↓
¿puedo apuntar al propio servidor?
        ↓
¿puedo apuntar a sistemas internos?
        ↓
¿la respuesta confirma qué alcanzó el servidor?
```

---

# Qué NO mezclamos todavía

Temas más avanzados del learning path completo:

- blacklist filters;
- whitelist filters;
- open redirects;
- blind SSRF;
- Burp Collaborator/OAST;
- SSRF mediante `Referer`;
- bypasses de validación de URL.

Se estudiarán cuando PortSwigger los introduzca.

---

# Burp aprendido en SSRF

- [x] Interceptar/localizar `Check stock`.
- [x] Identificar `stockApi` en el body.
- [x] `Send to Repeater`.
- [x] Cambiar una URL en Repeater.
- [x] Leer HTML devuelto en la Response.
- [x] `Send to Intruder`.
- [x] Marcar el último octeto con `§`.
- [x] Usar payload type **Numbers**.
- [x] Configurar `1 → 255`.
- [x] Ordenar/buscar por `Status code`.
- [x] Pasar un resultado de Intruder a Repeater.

---

# Checklist Apprentice

- [x] Puedo explicar SSRF con mis palabras.
- [x] Entiendo navegador vs petición server-side.
- [x] Sé qué son `localhost` y loopback.
- [x] Entiendo por qué un servicio interno puede confiar en peticiones locales.
- [x] Sé identificar `stockApi`.
- [x] Sé usar Repeater para cambiar destino.
- [x] Sé usar Intruder Numbers para recorrer una IP interna.
- [x] Lab 1 completado.
- [x] Lab 2 completado.

---

# Regla rápida

**SSRF no significa que mi navegador pueda acceder al destino. Significa que consigo que EL SERVIDOR haga la petición por mí.**
