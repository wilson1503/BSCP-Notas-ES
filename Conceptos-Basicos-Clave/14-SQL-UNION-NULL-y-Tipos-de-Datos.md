# SQL UNION, NULL y tipos de datos

> Conceptos mínimos para entender los primeros labs Practitioner de SQL Injection de PortSwigger.

## UNION

`UNION` combina filas devueltas por dos consultas `SELECT`.

Ejemplo conceptual:

```sql
SELECT name, price FROM products
UNION
SELECT username, password FROM users;
```

Para que sea válido normalmente deben cumplirse dos condiciones:

1. ambas consultas devuelven el mismo número de columnas;
2. los tipos de datos en posiciones equivalentes son compatibles.

---

## Por qué PortSwigger usa NULL

`NULL` representa ausencia de valor y suele ser convertible/compatible con muchos tipos de datos.

Por eso podemos probar:

```sql
UNION SELECT NULL
UNION SELECT NULL,NULL
UNION SELECT NULL,NULL,NULL
```

La cantidad que deja de producir error revela cuántas columnas debemos devolver.

---

## Posiciones de columna

Si sabemos que hay 3 columnas:

```sql
UNION SELECT NULL,NULL,NULL
```

podemos comprobar cuál acepta texto:

```sql
UNION SELECT 'abc',NULL,NULL
UNION SELECT NULL,'abc',NULL
UNION SELECT NULL,NULL,'abc'
```

Mover el string una posición cada vez nos permite inferir la compatibilidad de tipos.

---

## Concatenación

Si solamente una columna puede mostrar texto, podemos combinar varios valores en ella.

Ejemplo usado por PortSwigger:

```sql
username || '~' || password
```

Puede producir:

```text
administrator~secret123
```

El `~` no tiene magia: simplemente es un separador visual elegido para distinguir username y password.

---

## La idea importante

No memorizar:

```text
NULL,NULL,NULL
```

como si fuera un payload universal.

Hay que pensar:

```text
¿cuántas columnas tiene la consulta original?
        ↓
¿qué tipos acepta cada posición?
        ↓
¿en qué columna puedo colocar los datos que quiero recuperar?
```

## Relación con Burp

Estos labs son especialmente buenos para **Repeater** porque hacemos variaciones pequeñas de una misma request:

```text
1 columna
→ Send
2 columnas
→ Send
3 columnas
→ Send
```

Después podemos comparar status, contenido y errores sin repetir toda la navegación.
