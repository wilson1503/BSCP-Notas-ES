# 07 — SQL Injection (SQLi)

> **Método de estudio:** seguir primero el orden, conceptos y workflows oficiales de PortSwigger Web Security Academy. Las técnicas UNION, blind SQLi, error-based, time-based, OAST y payload collections avanzadas se estudiarán cuando PortSwigger las introduzca o en `Preparacion-Examen/`.

## Qué es SQL Injection

**SQL injection (SQLi)** ocurre cuando una aplicación incorpora datos controlados por el usuario dentro de una consulta SQL de forma insegura.

La idea central es:

```text
usuario controla un parámetro
        ↓
aplicación lo concatena dentro de SQL
        ↓
la base de datos interpreta parte del input como sintaxis SQL
        ↓
la lógica de la consulta cambia
```

Esto puede permitir, según el contexto:

- recuperar datos que deberían estar ocultos;
- saltarse un login;
- leer información de otras tablas;
- modificar datos;
- en casos más avanzados, comprometer otros componentes.

---

# Orden oficial en Server-side vulnerabilities — Apprentice

PortSwigger incluye actualmente **7 elementos** en el bloque SQL Injection de este learning path:

1. What is SQL injection (SQLi)?
2. How to detect SQL injection vulnerabilities
3. Retrieving hidden data
4. Retrieving hidden data — Continued
5. **Lab: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data**
6. Subverting application logic
7. **Lab: SQL injection vulnerability allowing login bypass**

En este bloque Apprentice haremos **2 labs**.

---

# 1. Consulta SQL básica que debemos entender

Una aplicación puede construir una consulta parecida a:

```sql
SELECT * FROM products
WHERE category = 'Gifts'
AND released = 1;
```

Esto significa:

```text
traer productos
QUE pertenezcan a Gifts
Y que released sea 1
```

La aplicación espera que `category` sea solamente un dato, por ejemplo:

```text
Gifts
Pets
Accessories
```

El problema aparece si concatena directamente ese valor dentro de la consulta.

---

# 2. Comillas y cambio de sintaxis

En SQL, las cadenas suelen encerrarse entre comillas simples:

```sql
'Gifts'
```

Si un parámetro controlado por el usuario termina dentro de esas comillas, introducir:

```text
'
```

puede cerrar la cadena original y permitir añadir sintaxis SQL.

Regla mental:

```text
entrada esperada = dato
entrada vulnerable = puede convertirse en parte de la consulta
```

---

# 3. Condiciones booleanas

SQL usa operadores lógicos como:

```text
AND
OR
```

Una condición como:

```sql
1=1
```

es siempre verdadera.

Por eso una inyección como:

```text
' OR 1=1--
```

puede cambiar completamente el significado de un `WHERE` vulnerable.

---

# 4. Comentarios SQL

`--` se utiliza en muchos motores SQL para iniciar un comentario.

En estos labs se usa para hacer que el resto de la consulta original deje de afectar al resultado.

Ejemplo conceptual:

```sql
SELECT * FROM products
WHERE category = '' OR 1=1--'
AND released = 1;
```

La parte posterior a `--` queda anulada por el comentario.

La condición relevante pasa a ser:

```sql
category = '' OR 1=1
```

Como `1=1` siempre es verdadero, la consulta puede devolver todos los productos, incluidos los no publicados.

---

# Lab 1 — SQL injection vulnerability in WHERE clause allowing retrieval of hidden data

> [Procedimiento completo paso a paso](../Labs/SQL-Injection/01-WHERE-clause-retrieving-hidden-data.md)

## Objetivo

Conseguir que la aplicación muestre **uno o más productos no publicados**.

## Workflow oficial de PortSwigger

```text
seleccionar categoría
        ↓
Proxy → Intercept
        ↓
interceptar request del filtro
        ↓
modificar category
        ↓
'+OR+1=1--
        ↓
Forward
        ↓
la Response muestra productos ocultos
```

Payload oficial en el parámetro `category`:

```text
'+OR+1=1--
```

Semánticamente representa:

```text
' OR 1=1--
```

Los `+` aparecen porque la petición representa espacios de forma URL-encoded/form-encoded.

---

# 5. Subverting application logic

SQLi no sirve únicamente para recuperar más filas. También puede alterar la lógica de autenticación.

Un login vulnerable podría construir algo parecido a:

```sql
SELECT * FROM users
WHERE username = 'wiener'
AND password = 'peter';
```

La aplicación interpreta que existe una cuenta válida si la consulta devuelve una fila.

Si controlamos `username`, podemos intentar hacer que la comprobación de contraseña deje de formar parte efectiva de la consulta.

---

# Lab 2 — SQL injection vulnerability allowing login bypass

> [Procedimiento completo paso a paso](../Labs/SQL-Injection/02-SQLi-login-bypass.md)

## Objetivo

Iniciar sesión como:

```text
administrator
```

sin conocer su contraseña.

## Workflow oficial de PortSwigger

```text
abrir login
        ↓
Proxy → Intercept
        ↓
interceptar POST /login
        ↓
username=administrator'--
        ↓
Forward
        ↓
la parte de password queda comentada
        ↓
login como administrator
```

Payload oficial:

```text
administrator'--
```

Conceptualmente la consulta queda parecida a:

```sql
SELECT * FROM users
WHERE username = 'administrator'--'
AND password = 'cualquier-cosa';
```

La comprobación del password queda después del comentario.

---

# Cómo detectar SQLi — mentalidad inicial

PortSwigger enseña que una forma de detectar SQLi es modificar sistemáticamente entradas y observar cambios.

En este nivel Apprentice debemos acostumbrarnos a probar y razonar sobre:

```text
'
condiciones booleanas
comentarios
cambios visibles en resultados
cambios en la lógica de autenticación
```

No asumir que un error visible es obligatorio. Una aplicación vulnerable puede responder normalmente pero con **datos diferentes**.

---

# Qué mirar en Burp

En estos dos labs será importante reconocer:

```text
GET /filter?category=...
POST /login
```

Y distinguir:

```text
parámetro category
parámetro username
parámetro password
```

El procedimiento oficial de ambos labs utiliza principalmente:

```text
Proxy → Intercept
modificar request
Forward
observar resultado
```

Podemos practicar también con Repeater después, pero no sustituye al workflow oficial de estudio.

---

# URL encoding que veremos

En una URL/request puedes ver:

```text
' OR 1=1--
```

representado como:

```text
'+OR+1=1--
```

O algunos caracteres podrían aparecer percent-encoded, por ejemplo:

```text
%27
```

para una comilla simple `'`.

No son ataques diferentes: son representaciones HTTP del mismo contenido lógico.

---

# Método mental para SQL Injection

```text
¿Qué parámetro controla el usuario?
        ↓
¿podría terminar dentro de una consulta SQL?
        ↓
¿una comilla altera el comportamiento?
        ↓
¿puedo introducir una condición SQL?
        ↓
¿puedo comentar el resto de la consulta?
        ↓
¿cambian los datos o la lógica de la aplicación?
```

---

# Qué NO mezclaremos todavía

El learning path completo de SQL Injection introduce después técnicas más avanzadas, entre ellas:

- `UNION SELECT`;
- determinar número de columnas;
- encontrar columnas de tipo texto;
- recuperar datos de otras tablas;
- identificar motor y versión de base de datos;
- blind SQL injection;
- conditional responses;
- conditional errors;
- time delays;
- out-of-band/OAST;
- XML encoding bypass;
- second-order SQL injection.

Las estudiaremos cuando corresponda. No las usaremos para sustituir estos dos primeros labs Apprentice.

---

# Burp que debemos dominar en este bloque

- [ ] Interceptar una request de filtro de categoría.
- [ ] Localizar y modificar `category`.
- [ ] Reconocer URL/form encoding básico.
- [ ] Interpretar cómo cambia la Response cuando la condición SQL cambia.
- [ ] Interceptar `POST /login`.
- [ ] Localizar `username` y `password`.
- [ ] Modificar solamente `username` con el payload oficial.
- [ ] Entender por qué `--` elimina la comprobación posterior.

---

# Checklist Apprentice

- [ ] Puedo explicar SQL Injection con mis palabras.
- [ ] Entiendo `SELECT`, `WHERE`, `AND` y `OR` a nivel básico.
- [ ] Entiendo por qué una comilla simple puede ser relevante.
- [ ] Entiendo por qué `1=1` es una condición siempre verdadera.
- [ ] Entiendo qué hace `--` en los labs.
- [ ] Entiendo la diferencia entre recuperar datos ocultos y alterar lógica de login.
- [ ] Lab 1 completado.
- [ ] Lab 2 completado.

---

# Registro de labs

## Lab 1 — SQL injection vulnerability in WHERE clause allowing retrieval of hidden data

- Estado: ⬜ preparado.
- Parámetro: `category`.
- Payload oficial: `'+OR+1=1--`.
- Objetivo: mostrar productos no publicados.

## Lab 2 — SQL injection vulnerability allowing login bypass

- Estado: ⬜ preparado.
- Parámetro: `username`.
- Payload oficial: `administrator'--`.
- Objetivo: iniciar sesión como `administrator`.

---

# Regla rápida

**SQL Injection ocurre cuando algo que debería ser solo DATO termina siendo interpretado como parte de la CONSULTA SQL.**
