# Lab 5 — User ID controlled by request parameter with password disclosure

> **Fuente principal:** solución oficial de PortSwigger Web Security Academy.
>
> **Concepto:** horizontal privilege escalation que termina en vertical privilege escalation.

## Objetivo del laboratorio

PortSwigger indica que la página de cuenta muestra la contraseña actual del usuario dentro de un campo de contraseña enmascarado.

Para resolver el lab hay que:

```text
obtener la contraseña de administrator
        ↓
iniciar sesión como administrator
        ↓
entrar al panel administrativo
        ↓
eliminar a carlos
```

## Credenciales proporcionadas por PortSwigger

```text
Usuario: wiener
Contraseña: peter
```

---

# Procedimiento OFICIAL de PortSwigger

La solución oficial tiene cuatro pasos principales:

1. Iniciar sesión con `wiener:peter` y abrir la página de cuenta.
2. Cambiar el parámetro `id` de la URL a `administrator`.
3. Revisar la **response en Burp** y obtener la contraseña del administrador.
4. Iniciar sesión como `administrator` y eliminar a `carlos`.

A continuación está el mismo procedimiento explicado para alguien que todavía está aprendiendo Burp Suite.

---

# Paso 1 — Iniciar sesión como `wiener`

En el navegador del lab:

```text
My account
```

Usar:

```text
wiener
peter
```

Después del login deberías llegar a una URL parecida a:

```text
/my-account?id=wiener
```

## Qué debes reconocer

La parte importante es:

```text
id=wiener
```

`id` está indicando **qué usuario desea consultar la página de cuenta**.

---

# Paso 2 — Cambiar `id` a `administrator`

PortSwigger indica cambiar el parámetro directamente en la URL.

Cambiar:

```text
/my-account?id=wiener
```

por:

```text
/my-account?id=administrator
```

Presionar Enter.

No estás cambiando tu sesión: sigues autenticado como `wiener`.

Lo que estás cambiando es el **objeto que solicitas**.

```text
sesión = wiener
id solicitado = administrator
```

Si la aplicación estuviera correctamente protegida debería impedirlo.

---

# Paso 3 — Ver la response en Burp

Este es el paso importante de Burp que PortSwigger quiere que practiquemos.

## 3.1 Abrir HTTP history

En Burp Suite:

```text
Proxy
→ HTTP history
```

No necesitas activar **Intercept** para revisar el historial.

## 3.2 Localizar la request

Busca la petición que acabas de generar desde el navegador:

```http
GET /my-account?id=administrator HTTP/2
```

Ayudas para encontrarla:

- busca el dominio de tu lab;
- mira la columna `URL`;
- busca `/my-account?id=administrator`;
- normalmente tendrá una respuesta `200` si el acceso funcionó.

## 3.3 Seleccionar la request

Haz clic sobre esa fila.

En la parte inferior de Burp normalmente tendrás dos lados/paneles:

```text
Request | Response
```

Selecciona:

```text
Response
```

Luego usa la vista:

```text
Pretty
```

o `Raw` si prefieres ver todo el HTML sin formato.

## 3.4 Buscar `password`

Dentro de la Response usa:

```text
Ctrl + F
```

Busca:

```text
password
```

PortSwigger indica que la contraseña del administrador aparece en la response dentro del HTML de un campo similar a:

```html
<input type="password" name="password" value="CONTRASEÑA_DEL_ADMINISTRADOR">
```

### Importante

El navegador puede mostrar el campo como:

```text
••••••••••••
```

porque HTML usa:

```html
type="password"
```

pero eso **solo oculta visualmente los caracteres**.

Si el servidor envía esto:

```html
value="secreto123"
```

el valor real sigue estando presente en la HTTP response y Burp puede verlo.

Copia solamente lo que esté dentro de:

```text
value="..."
```

Ese es el password de `administrator` para ESA instancia del laboratorio.

---

# Paso 4 — Iniciar sesión como administrator

Ahora vuelve al navegador.

Haz logout de `wiener`:

```text
Log out
```

Entra otra vez a:

```text
My account
```

Usa:

```text
Usuario: administrator
Contraseña: LA_QUE_ACABAS_DE_OBTENER
```

Si funciona, ya completaste la cadena:

```text
horizontal escalation
        ↓
password disclosure
        ↓
account takeover de administrator
        ↓
vertical escalation
```

---

# Paso 5 — Eliminar a `carlos`

Ya autenticado como administrator, abre el panel administrativo.

En este lab normalmente aparecerá el enlace:

```text
Admin panel
```

Si necesitas reconocer el endpoint en Burp, será algo parecido a:

```http
GET /admin HTTP/2
```

Dentro del panel busca:

```text
carlos
```

y usa **Delete**.

Al eliminar a `carlos`, el laboratorio debería pasar a:

```text
Solved
```

---

# Qué request es la importante

La petición clave es:

```http
GET /my-account?id=administrator HTTP/2
```

La cookie de sesión seguirá perteneciendo a `wiener`.

Conceptualmente:

```http
GET /my-account?id=administrator HTTP/2
Cookie: session=SESION_DE_WIENER
```

Esto demuestra el fallo:

```text
usuario autenticado = wiener
recurso solicitado   = administrator
servidor              = devuelve datos del administrador
```

---

# Qué response es la importante

En la response queremos localizar un elemento equivalente a:

```html
<input type="password" name="password" value="...">
```

La vulnerabilidad no consiste solamente en que el password esté en HTML.

La cadena completa es:

```text
id controlado por el usuario
        ↓
servidor no comprueba ownership/autorización
        ↓
wiener puede cargar la cuenta de administrator
        ↓
la página contiene el password existente
        ↓
password disclosure
        ↓
login como administrator
        ↓
vertical privilege escalation
```

---

# Burp Suite — práctica opcional con Repeater

> **Esto es complementario.** La solución oficial de PortSwigger indica cambiar `id` en la URL y revisar la response en Burp. Repeater sirve para practicar Burp sin sustituir ese workflow.

Después de encontrar:

```http
GET /my-account?id=wiener
```

puedes:

1. clic derecho sobre la request;
2. **Send to Repeater**;
3. abrir **Repeater**;
4. cambiar:

```text
id=wiener
```

por:

```text
id=administrator
```

5. pulsar **Send**;
6. buscar `password` en la Response.

Esto permite comparar cómodamente ambas respuestas.

---

# Qué está enseñando PortSwigger

## Horizontal privilege escalation

Primero ocurre:

```text
wiener → datos de administrator
```

Aunque `administrator` tenga más privilegios, el acceso inicial ocurre mediante un identificador manipulable en un recurso de cuenta.

## Horizontal → vertical privilege escalation

El dato recuperado es suficientemente sensible para comprometer la cuenta administrativa.

```text
wiener
  ↓
lee password de administrator
  ↓
login administrator
  ↓
admin panel
```

Este es el punto central de este bloque del learning path.

---

# Errores comunes

- Buscar la contraseña solamente en lo que renderiza visualmente el navegador.
- Pensar que `••••••••` significa que el servidor no envió el password.
- No revisar la **HTTP response** en Burp.
- Cambiar la cookie/session cuando el parámetro vulnerable es `id`.
- Confundir `id=administrator` con iniciar sesión como administrator: todavía sigues autenticado como `wiener` hasta que haces logout y utilizas el password recuperado.
- Intentar adivinar el password: el laboratorio pretende que lo obtengas desde la response.

---

# Checklist de aprendizaje

Antes de considerar dominado el lab debo poder explicar:

- [ ] qué significa `id=wiener`;
- [ ] por qué puedo probar `id=administrator`;
- [ ] dónde está **Proxy → HTTP history**;
- [ ] cómo localizar `GET /my-account?id=administrator`;
- [ ] diferencia entre Request y Response;
- [ ] cómo buscar texto dentro de una Response;
- [ ] por qué un `<input type="password">` no protege el valor enviado en HTML;
- [ ] cómo el fallo horizontal termina convirtiéndose en escalación vertical;
- [ ] cómo reproducir el workflow oficial sin depender de una solución copiada.

---

# Resumen ultrarrápido

```text
Login wiener:peter
        ↓
/my-account?id=wiener
        ↓
cambiar URL → id=administrator
        ↓
Burp → Proxy → HTTP history
        ↓
GET /my-account?id=administrator
        ↓
Response → buscar "password"
        ↓
copiar value="PASSWORD"
        ↓
logout
        ↓
login administrator:PASSWORD
        ↓
Admin panel
        ↓
Delete carlos
        ↓
Solved
```
