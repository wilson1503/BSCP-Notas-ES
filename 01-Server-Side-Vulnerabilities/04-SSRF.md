# 04 — Server-side request forgery (SSRF)

> **Método de estudio:** seguir primero el orden, conceptos y workflows oficiales de PortSwigger Web Security Academy. Los bypasses avanzados y atajos de examen quedan separados en `Preparacion-Examen/`.

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

PortSwigger incluye actualmente 6 elementos en el bloque SSRF de este learning path:

1. What is SSRF?
2. SSRF attacks against the server
3. SSRF attacks against the server — Continued
4. **Lab: Basic SSRF against the local server**
5. SSRF attacks against other back-end systems
6. **Lab: Basic SSRF against another back-end system**

En este bloque Apprentice haremos **2 labs**.

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

Una función puede estar bloqueada para clientes externos pero permitida cuando la petición procede de la propia máquina. SSRF puede abusar de esa diferencia de confianza.

---

# Lab 1 — Basic SSRF against the local server ⏳

> [Procedimiento completo paso a paso](../Labs/SSRF/01-Basic-SSRF-against-local-server.md)

## Objetivo

Usar la función **Check stock** para hacer que el servidor acceda a:

```text
http://localhost/admin
```

y luego eliminar a `carlos`.

## Flujo que debemos entender

```text
Browser
  ↓
POST /product/stock
stockApi=URL
  ↓
Servidor vulnerable
  ↓
GET URL indicada en stockApi
```

Cambiando la URL:

```text
stockApi legítimo
        ↓
http://localhost/admin
        ↓
servidor consulta su propio panel admin
```

La herramienta principal será **Burp Repeater**.

---

# 2. SSRF contra otros sistemas back-end

La aplicación vulnerable puede tener conectividad con otros hosts de una red privada que nosotros no podemos alcanzar directamente.

Ejemplo conceptual:

```text
Internet
   X
192.168.0.42:8080/admin
   ↑
servidor vulnerable sí tiene acceso
```

Si controlamos el destino de una petición server-side, podemos intentar distintos hosts internos hasta localizar uno que responda de forma diferente.

---

# Lab 2 — Basic SSRF against another back-end system

> [Procedimiento completo paso a paso](../Labs/SSRF/02-Basic-SSRF-against-backend-system.md)

## Objetivo

Escanear mediante `stockApi` el rango:

```text
192.168.0.X:8080
```

para localizar un panel `/admin` y eliminar a `carlos`.

## Técnica Burp que enseña

Este lab combina:

```text
Proxy / HTTP history
        ↓
Send to Intruder
        ↓
Numbers 1 → 255
        ↓
identificar respuesta HTTP 200
        ↓
Send to Repeater
        ↓
eliminar carlos
```

Aquí **Intruder** ya no se usa para usernames/passwords, sino para variar sistemáticamente un número dentro de una IP.

---

# Qué buscar como superficie SSRF

En este bloque debemos acostumbrarnos a detectar parámetros que representen destinos de red o URLs, por ejemplo:

```text
url=
uri=
path=
stockApi=
endpoint=
callback=
webhook=
```

No significa que todos sean vulnerables. La señal importante es que **el servidor parece realizar una petición usando el valor enviado por el cliente**.

---

# Qué mirar en Burp

Cuando usemos una función como **Check stock**:

1. localizar la request en `Proxy → HTTP history` o interceptarla;
2. buscar en el body/query un valor que parezca URL;
3. identificar qué respuesta devuelve la aplicación cuando cambia ese destino;
4. enviar la request a la herramienta indicada por el lab:
   - Repeater para probar un destino concreto;
   - Intruder para recorrer un conjunto de hosts/valores.

Ejemplo conceptual:

```http
POST /product/stock HTTP/2
Content-Type: application/x-www-form-urlencoded

stockApi=http://stock.example/product/1
```

El punto controlable es:

```text
stockApi=
```

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
¿la respuesta me permite confirmar qué alcanzó el servidor?
```

---

# Qué NO mezclaremos todavía

El learning path completo de SSRF contiene temas más avanzados como:

- blacklist filters;
- whitelist filters;
- open redirects;
- blind SSRF;
- Burp Collaborator/OAST;
- SSRF mediante `Referer`;
- bypasses de validación de URL.

Los estudiaremos cuando PortSwigger los introduzca. No los usaremos para sustituir el workflow básico de estos dos labs Apprentice.

---

# Burp que debemos aprender en SSRF

- [ ] Interceptar `Check stock`.
- [ ] Identificar `stockApi` en el body.
- [ ] `Send to Repeater`.
- [ ] Cambiar solo una URL en Repeater.
- [ ] Leer HTML devuelto dentro de la response del stock check.
- [ ] `Send to Intruder`.
- [ ] Marcar solo el último octeto de una IP con `§`.
- [ ] Usar payload type **Numbers**.
- [ ] Configurar `From=1`, `To=255`, `Step=1`.
- [ ] Ordenar resultados por `Status code`.
- [ ] Enviar un resultado concreto de Intruder a Repeater.

---

# Checklist Apprentice

- [ ] Puedo explicar SSRF con mis palabras.
- [ ] Entiendo la diferencia entre una petición del navegador y una petición server-side.
- [ ] Sé qué son `localhost` y `127.0.0.1`.
- [ ] Entiendo por qué un servicio interno puede confiar más en peticiones locales.
- [ ] Sé identificar un parámetro URL como `stockApi`.
- [ ] Sé usar Repeater para cambiar el destino.
- [ ] Sé usar Intruder Numbers para variar una IP interna.
- [ ] Lab 1 completado.
- [ ] Lab 2 completado.

---

# Registro de labs

## Lab 1 — Basic SSRF against the local server

- Estado: ⏳ siguiente lab.
- Herramienta principal: **Repeater**.
- Parámetro: `stockApi`.
- Destino clave: `http://localhost/admin`.

## Lab 2 — Basic SSRF against another back-end system

- Estado: ⬜ pendiente.
- Herramientas: **Intruder + Repeater**.
- Red a recorrer: `192.168.0.X`.
- Puerto: `8080`.

---

# Regla rápida

**SSRF no significa que mi navegador pueda acceder al destino. Significa que consigo que EL SERVIDOR haga la petición por mí.**
