# SQL básico para entender SQL Injection

> Esta nota no pretende enseñar SQL completo. Solo reúne la sintaxis mínima necesaria para entender los primeros labs Apprentice de PortSwigger.

## 1. SELECT

Una consulta básica puede ser:

```sql
SELECT * FROM products;
```

Esto significa:

```text
traer todas las columnas de la tabla products
```

---

## 2. WHERE

`WHERE` filtra filas.

```sql
SELECT * FROM products
WHERE category = 'Gifts';
```

Solo devuelve productos cuya categoría sea `Gifts`.

---

## 3. Strings y comillas simples

En estos ejemplos, los textos aparecen entre:

```text
'
```

Ejemplo:

```sql
username = 'administrator'
```

Por eso una comilla controlada por el usuario puede ser relevante: puede cerrar la cadena que la aplicación estaba construyendo.

---

## 4. AND

```sql
A AND B
```

solo es verdadero si ambas condiciones son verdaderas.

Ejemplo:

```sql
category = 'Gifts' AND released = 1
```

El producto debe ser de Gifts **y además** estar publicado.

---

## 5. OR

```sql
A OR B
```

es verdadero si al menos una condición es verdadera.

Ejemplo:

```sql
category = '' OR 1=1
```

Como:

```sql
1=1
```

siempre es verdadero, toda la expresión puede resultar verdadera.

---

## 6. Comentarios con `--`

En los labs iniciales de PortSwigger usamos:

```text
--
```

para comentar el resto de una consulta.

Ejemplo conceptual:

```sql
username = 'administrator'--' AND password = 'test'
```

La parte posterior al comentario deja de afectar a la consulta.

---

## 7. Por qué SQL Injection existe

Código inseguro conceptual:

```text
query = "SELECT ... WHERE username = '" + USER_INPUT + "'"
```

El programa espera que `USER_INPUT` sea solamente un username.

Pero si enviamos sintaxis SQL, esa sintaxis queda mezclada con la consulta.

```text
dato + código SQL
```

Ese es el problema.

---

## 8. Primer patrón: recuperar datos ocultos

Entrada:

```text
' OR 1=1--
```

Partes:

```text
'       → cierra cadena
OR      → añade condición alternativa
1=1     → siempre verdadero
--      → comenta el resto
```

---

## 9. Segundo patrón: bypass de login

Entrada:

```text
administrator'--
```

Partes:

```text
administrator → usuario que queremos seleccionar
'             → cierra la cadena
--            → elimina la comprobación posterior de password
```

---

## 10. URL encoding

En requests HTTP puedes ver espacios representados como:

```text
+
```

Por eso:

```text
' OR 1=1--
```

puede verse como:

```text
'+OR+1=1--
```

También una comilla simple puede aparecer como:

```text
%27
```

El significado lógico no cambia.

---

# Regla mental

```text
HTTP parameter
      ↓
¿se concatena dentro de SQL?
      ↓
¿puedo cerrar la cadena?
      ↓
¿puedo añadir lógica SQL?
      ↓
¿puedo comentar el resto?
```

---

# No confundir con otros temas

```text
OS Command Injection → intérprete final = shell / sistema operativo
SQL Injection        → intérprete final = motor de base de datos
```

En ambos casos el problema general se parece:

```text
un dato controlado por el usuario
termina siendo interpretado como instrucciones
```
