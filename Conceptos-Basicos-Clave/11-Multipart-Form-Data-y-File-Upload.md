# Multipart/form-data y File Upload

## Por qué este concepto importa

Cuando un formulario web envía un archivo, normalmente no puede mandar todo como un simple:

```text
campo=valor&campo2=valor2
```

En su lugar utiliza:

```http
Content-Type: multipart/form-data; boundary=...
```

La request se divide en varias **partes**.

---

# Estructura conceptual

```http
POST /my-account/avatar HTTP/2
Content-Type: multipart/form-data; boundary=----ABC

------ABC
Content-Disposition: form-data; name="avatar"; filename="foto.jpg"
Content-Type: image/jpeg

<bytes del archivo>
------ABC
Content-Disposition: form-data; name="user"

wiener
------ABC--
```

Cada parte puede tener sus propios headers.

---

# Dos Content-Type distintos

Este detalle es especialmente importante en Burp.

## 1. Content-Type de toda la request

```http
Content-Type: multipart/form-data; boundary=----ABC
```

Indica cómo está estructurado el body completo.

## 2. Content-Type del archivo

Dentro de una parte aparece:

```http
Content-Disposition: form-data; name="avatar"; filename="exploit.php"
Content-Type: application/x-php
```

Este segundo header describe **solo esa parte**.

En el lab Apprentice de File Upload, PortSwigger modifica este segundo valor a:

```http
Content-Type: image/jpeg
```

No el Content-Type global.

---

# Content-Disposition

Ejemplo:

```http
Content-Disposition: form-data; name="avatar"; filename="exploit.php"
```

Aquí tenemos:

```text
name="avatar"        → nombre del campo del formulario
filename="exploit.php" → nombre que declara el cliente
```

Ambos datos llegan desde la request del cliente y deben tratarse como no confiables.

---

# Boundary

El `boundary` separa las partes.

```text
------ABC
parte 1
------ABC
parte 2
------ABC--
```

Cuando editamos manualmente un upload en Burp, normalmente **no debemos tocar los boundaries** si no es necesario.

Romperlos puede hacer que el servidor deje de parsear el formulario correctamente.

---

# Qué debemos reconocer en Burp

Al ver un upload, localizar:

```text
POST endpoint
Content-Type global multipart/form-data
boundary
Content-Disposition
name=
filename=
Content-Type de la parte
contenido del archivo
```

---

# Trust boundary

Un error frecuente es pensar:

```text
Content-Type: image/jpeg
→ entonces realmente es JPEG
```

No necesariamente.

El cliente puede enviar:

```text
filename="exploit.php"
Content-Type: image/jpeg
contenido = PHP
```

Por eso una aplicación segura no debería confiar únicamente en el MIME declarado por el cliente.

---

# Regla mental

```text
multipart/form-data
        ↓
varias partes
        ↓
cada parte puede tener headers propios
        ↓
headers enviados por cliente = manipulables
```

---

# Relación con Burp

En Repeater podemos modificar directamente:

```text
filename
Content-Type de la parte
contenido
otros campos del formulario
```

pero durante la fase de aprendizaje solo modificaremos lo que el workflow oficial del lab indique.

---

# Checklist

- [ ] Distingo el Content-Type global del Content-Type del archivo.
- [ ] Sé qué significa `Content-Disposition`.
- [ ] Sé reconocer `filename=`.
- [ ] Entiendo para qué sirve el boundary.
- [ ] Sé que el MIME declarado por el cliente no demuestra el tipo real.
- [ ] Puedo localizar la parte del archivo dentro de una request en Burp.
