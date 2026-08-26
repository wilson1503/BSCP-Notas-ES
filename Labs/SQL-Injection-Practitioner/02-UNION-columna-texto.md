# Lab 02 — SQL injection UNION attack, finding a column containing text

> Fuente principal: solución oficial de PortSwigger Web Security Academy.

## Objetivo exacto

Encontrar cuál de las columnas devueltas por la consulta acepta datos de tipo texto y hacer que aparezca en la respuesta el valor aleatorio proporcionado por el lab.

URL oficial:

https://portswigger.net/web-security/sql-injection/union-attacks/lab-find-column-containing-text

---

# Qué enseña este lab

Del lab anterior sabemos cómo descubrir el número de columnas. Ahora necesitamos saber **qué posición puede contener strings**.

Aunque una consulta devuelva 3 columnas, no todas tienen por qué aceptar texto.

---

# Procedimiento oficial paso a paso

## Paso 1 — Capturar el filtro

Abre una categoría de productos e intercepta la request con:

```text
Proxy → Intercept
```

Localiza:

```text
category=...
```

## Paso 2 — Confirmar 3 columnas

Usa:

```text
'+UNION+SELECT+NULL,NULL,NULL--
```

En este lab la consulta devuelve **3 columnas**.

## Paso 3 — Copiar el valor aleatorio

La página del lab muestra una cadena aleatoria que debes conseguir que aparezca en los resultados, por ejemplo:

```text
abcdef
```

Tu valor será distinto en cada instancia.

## Paso 4 — Probar la primera posición

Sustituye el primer `NULL` por la cadena del lab:

```text
'+UNION+SELECT+'abcdef',NULL,NULL--
```

Si la aplicación produce un error, esa posición no es compatible con texto.

## Paso 5 — Mover la cadena

Prueba:

```text
'+UNION+SELECT+NULL,'abcdef',NULL--
```

Si sigue fallando, prueba:

```text
'+UNION+SELECT+NULL,NULL,'abcdef'--
```

Cuando la posición sea compatible, el valor aparecerá en la respuesta y el lab se resolverá.

---

# Qué debes observar

```text
string en columna incompatible
→ error

string en columna compatible
→ respuesta válida
→ el texto aparece renderizado
→ Solved
```

---

# Por qué funciona

`UNION` no solo exige el mismo número de columnas. Los tipos de datos correspondientes también deben ser compatibles.

Por eso hacemos:

```text
NULL, NULL, NULL
```

para descubrir estructura, y después sustituimos **un solo NULL cada vez** por texto.

---

# Burp que tengo que aprender

- reutilizar la técnica del lab anterior;
- mover un valor entre posiciones de un `UNION SELECT`;
- distinguir error de tipo frente a respuesta válida;
- comprobar visualmente dónde aparece el texto en la Response.

## Repeater recomendado

Aunque la solución oficial parte de interceptar el filtro, este tipo de prueba incremental es ideal para:

```text
Send to Repeater
```

Así puedes cambiar una posición, pulsar **Send** y comparar respuestas sin volver al navegador cada vez.

---

# Checklist

- [ ] Confirmé que existen 3 columnas.
- [ ] Copié el valor aleatorio del lab.
- [ ] Probé el string en cada posición.
- [ ] Encontré la columna compatible con texto.
- [ ] Vi el valor en la respuesta.
- [ ] Lab Solved.

# Regla para recordar

```text
Número de columnas correcto
≠
todas las columnas sirven para texto
```
