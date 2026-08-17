# Lab 4 — User ID controlled by request parameter, with unpredictable user IDs

> **Nivel:** Apprentice  
> **Tema:** Access Control → Horizontal privilege escalation  
> **Fuente principal:** solución oficial de PortSwigger Web Security Academy.  
> **Estado:** ⏳ en progreso

## Objetivo del lab

La página de cuenta tiene una vulnerabilidad de **horizontal privilege escalation**, pero los usuarios se identifican mediante un **GUID** impredecible.

Objetivo:

```text
1. Encontrar el GUID de carlos.
2. Acceder a la cuenta de carlos usando ese GUID.
3. Obtener su API key.
4. Enviar la API key con Submit solution.
```

Credenciales proporcionadas por el lab:

```text
wiener:peter
```

---

# Procedimiento oficial de PortSwigger — paso a paso

## Paso 1 — NO intentar adivinar el GUID

Un GUID tiene una forma similar a:

```text
8f14e45f-ea6b-4f5a-9b7f-123456789abc
```

La idea del lab es entender que, aunque el identificador sea impredecible, **puede filtrarse en otra funcionalidad de la aplicación**.

No hace falta probar miles de valores.

---

## Paso 2 — Encontrar una publicación de `carlos`

1. Navegar por los artículos/blog posts de la aplicación.
2. Buscar una publicación cuyo autor sea:

```text
carlos
```

3. Hacer clic directamente sobre el nombre **carlos**.

---

## Paso 3 — Obtener el GUID de `carlos`

Después de hacer clic en `carlos`, mirar la **barra de direcciones del navegador**.

PortSwigger indica que la URL contiene el identificador del usuario.

Ejemplo conceptual:

```text
...?userId=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

Copiar únicamente el GUID de `carlos` y guardarlo temporalmente.

```text
GUID_CARLOS = xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### Qué acabamos de descubrir

```text
GUID difícil de adivinar
        ↓
pero la aplicación lo publica en el perfil/autor del blog
        ↓
el identificador deja de ser secreto
```

Un identificador impredecible **no reemplaza una comprobación de autorización**.

---

## Paso 4 — Iniciar sesión como nuestro usuario

Entrar a **My account** e iniciar sesión con:

```text
Usuario: wiener
Contraseña: peter
```

Después del login, abrir **My account**.

---

## Paso 5 — Identificar el parámetro vulnerable

Mirar la URL de nuestra cuenta.

Debe contener un parámetro parecido a:

```text
/my-account?id=GUID_DE_WIENER
```

La señal importante es:

```text
id=
```

La aplicación está usando un identificador enviado por el cliente para decidir **qué cuenta cargar**.

---

## Paso 6 — Sustituir nuestro GUID por el de `carlos`

En la barra de direcciones, reemplazar solamente el valor de `id`.

Antes:

```text
/my-account?id=GUID_DE_WIENER
```

Después:

```text
/my-account?id=GUID_DE_CARLOS
```

Presionar **Enter**.

### Resultado esperado

La página debería mostrar información perteneciente a:

```text
carlos
```

Si esto ocurre, acabamos de confirmar **horizontal privilege escalation**.

```text
wiener
  ↓ modifica id
carlos
  ↓
servidor entrega recurso de otro usuario
```

---

## Paso 7 — Copiar la API key de `carlos`

En la página de cuenta de `carlos`, localizar:

```text
API key
```

Copiar su valor completo.

---

## Paso 8 — Submit solution

Volver al banner/página del laboratorio y utilizar:

```text
Submit solution
```

Pegar la API key de `carlos`.

El lab debería cambiar a:

```text
Solved
```

---

# Qué mirar en Burp Suite mientras hago el procedimiento

> Esta parte sirve para aprender Burp. **No reemplaza el workflow oficial del lab**, que puede resolverse modificando el parámetro en el navegador.

## A. Abrir HTTP history

En Burp:

```text
Proxy
→ HTTP history
```

Mantener esta pestaña abierta mientras navegamos.

Si hay mucho tráfico, filtrar por el dominio del lab.

---

## B. Ver la petición del perfil/autor de `carlos`

Cuando hagamos clic en `carlos`, Burp registrará la request correspondiente.

Seleccionarla y revisar abajo:

```text
Request → Pretty / Raw
```

Buscar el GUID que también vimos en la URL.

Objetivo de este paso:

```text
aprender que HTTP history permite ver exactamente
qué valores está enviando el navegador al servidor
```

---

## C. Ver nuestra petición `/my-account`

Después de iniciar sesión como `wiener` y abrir **My account**, buscar en HTTP history una request similar a:

```http
GET /my-account?id=GUID_DE_WIENER HTTP/2
```

Aquí podemos identificar claramente:

```text
Endpoint: /my-account
Parámetro: id
Valor: GUID_DE_WIENER
```

---

# Ejercicio complementario en Repeater

> **Opcional para aprender Burp.** La solución oficial de PortSwigger indica cambiar el parámetro `id` por el GUID guardado; hacerlo en la barra del navegador es suficiente para resolver el lab.

Si queremos practicar Repeater:

1. En **Proxy → HTTP history**, localizar:

```http
GET /my-account?id=GUID_DE_WIENER
```

2. Clic derecho:

```text
Send to Repeater
```

3. Abrir:

```text
Repeater
```

4. Cambiar únicamente:

```text
id=GUID_DE_WIENER
```

por:

```text
id=GUID_DE_CARLOS
```

5. Pulsar:

```text
Send
```

6. En **Response**, buscar:

```text
carlos
API key
```

Esto permite ver de forma muy clara que **la misma sesión de wiener puede solicitar directamente el objeto de carlos**.

---

# Qué vulnerabilidad estamos explotando

## Horizontal privilege escalation

Tenemos dos usuarios del mismo nivel:

```text
wiener → usuario normal
carlos → usuario normal
```

`wiener` consigue acceder al recurso de `carlos`.

No estamos convirtiendo a `wiener` en administrador.

Por eso es:

```text
horizontal
```

y no:

```text
vertical
```

---

# Relación con IDOR

Este patrón está relacionado con **Insecure Direct Object Reference (IDOR)**:

```text
/my-account?id=<objeto controlado por el usuario>
```

El problema real NO es que el GUID sea visible.

El problema es que el servidor hace algo equivalente a:

```text
"me pidieron este ID → devuelvo esta cuenta"
```

sin comprobar correctamente:

```text
"¿el usuario autenticado tiene permiso para ver ESTA cuenta?"
```

---

# Error que NO debemos cometer

```text
"El GUID es aleatorio, entonces no se puede explotar"
```

Incorrecto.

La pregunta correcta es:

```text
¿Dónde más expone la aplicación ese GUID?
```

En este lab:

```text
blog post
→ autor carlos
→ URL
→ GUID de carlos
→ /my-account?id=GUID_CARLOS
```

---

# Burp que debo aprender en este lab

Al terminar debería saber hacer sin ayuda:

- [ ] abrir **Proxy → HTTP history**;
- [ ] localizar una request concreta;
- [ ] leer endpoint y query parameters;
- [ ] reconocer `id=<valor>`;
- [ ] entender qué parte de la request controla el navegador;
- [ ] enviar una request a **Repeater**;
- [ ] modificar un parámetro en Repeater;
- [ ] pulsar **Send** y leer la Response;
- [ ] diferenciar el workflow oficial del lab de una prueba complementaria en Repeater.

---

# Regla rápida

```text
ID impredecible
      ↓
buscar dónde la propia aplicación lo revela
      ↓
copiar ID de otro usuario
      ↓
probarlo en el recurso sensible
      ↓
si funciona sin autorización
      ↓
Horizontal Privilege Escalation / posible IDOR
```
