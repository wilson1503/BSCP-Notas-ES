# SQL Injection — Practitioner

> **Fuente de verdad:** PortSwigger Web Security Academy — learning path oficial de SQL Injection y soluciones oficiales de los labs.

## Punto de partida

Ya dominamos los dos labs Apprentice:

- recuperar productos ocultos con una condición booleana;
- saltar el login usando `administrator'--`.

Practitioner amplía SQLi desde cambiar una condición hasta **extraer información de otras consultas, reconocer el motor de base de datos y explotar SQLi blind**.

La progresión mental será:

```text
SQLi visible básica
→ UNION SELECT
→ número de columnas
→ columnas compatibles con texto
→ extraer datos
→ enumerar esquema
→ blind SQLi
→ respuesta condicional
→ errores condicionales
→ time delays
→ OAST / Collaborator
→ bypass mediante XML encoding
```

---

# Labs Practitioner del learning path oficial

Actualmente el learning path de SQL Injection contiene estos **13 labs Practitioner**, en este orden:

## A. UNION attacks

1. [Determining the number of columns returned by the query](../Labs/SQL-Injection-Practitioner/01-UNION-determinar-numero-columnas.md) — ⬜
2. [Finding a column containing text](../Labs/SQL-Injection-Practitioner/02-UNION-columna-texto.md) — ⬜
3. [Retrieving data from other tables](../Labs/SQL-Injection-Practitioner/03-UNION-datos-otras-tablas.md) — ⬜
4. [Retrieving multiple values in a single column](../Labs/SQL-Injection-Practitioner/04-UNION-multiples-valores-una-columna.md) — ⬜

## B. Examining the database

5. SQL injection attack, querying the database type and version on MySQL and Microsoft — ⬜
6. SQL injection attack, listing the database contents on non-Oracle databases — ⬜

## C. Blind SQL injection

7. Blind SQL injection with conditional responses — ⬜
8. Blind SQL injection with conditional errors — ⬜
9. Visible error-based SQL injection — ⬜
10. Blind SQL injection with time delays and information retrieval — ⬜
11. Blind SQL injection with out-of-band interaction — ⬜
12. Blind SQL injection with out-of-band data exfiltration — ⬜

## D. Different contexts / encoding

13. SQL injection with filter bypass via XML encoding — ⬜

---

# Primer bloque: UNION SELECT

`UNION` permite combinar los resultados de dos consultas `SELECT`.

Conceptualmente:

```sql
SELECT name, price FROM products
UNION
SELECT username, password FROM users;
```

Para que funcione necesitamos cumplir dos condiciones importantes:

1. ambas consultas deben devolver el **mismo número de columnas**;
2. los tipos de datos de las columnas correspondientes deben ser compatibles.

Por eso PortSwigger no empieza directamente extrayendo passwords. Primero nos hace descubrir la forma de la consulta original.

---

# Por qué usamos NULL

`NULL` suele ser compatible con muchos tipos de columna, por eso es útil para descubrir cuántas columnas devuelve una consulta.

Probamos:

```text
' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
' UNION SELECT NULL,NULL,NULL--
```

Mientras el número de columnas no coincida, normalmente obtenemos un error.

Cuando coincide:

```text
error desaparece
→ sabemos el número de columnas
```

---

# Después: encontrar una columna de texto

Saber que existen 3 columnas no significa que podamos colocar texto en cualquiera.

Ejemplo:

```text
' UNION SELECT 'abc',NULL,NULL--
' UNION SELECT NULL,'abc',NULL--
' UNION SELECT NULL,NULL,'abc'--
```

La posición que acepta el string nos indica qué columna puede mostrar información textual.

---

# Extraer información real

Cuando conocemos número y tipos, podemos pasar de pruebas a impacto:

```sql
' UNION SELECT username,password FROM users--
```

Si solo existe una columna textual visible, podemos concatenar valores:

```sql
username || '~' || password
```

Así una sola celda puede devolver algo como:

```text
administrator~secretPassword
```

---

# Examinar la base de datos

En Practitioner aprenderemos que la sintaxis cambia según el motor.

Ejemplos que PortSwigger introduce:

```text
MySQL / Microsoft → @@version
PostgreSQL / MySQL / Microsoft → information_schema
Oracle → dual, v$version, all_tables, all_tab_columns
```

No memorizar todavía todas las variantes. La idea es saber **identificar el DBMS y consultar el catálogo correcto**.

---

# Blind SQL Injection

Blind SQLi significa que la aplicación ejecuta nuestra SQL, pero **no devuelve directamente el resultado de la consulta**.

Entonces convertimos los datos en señales observables:

```text
¿condición verdadera?
→ aparece texto / cambia respuesta

¿condición falsa?
→ respuesta distinta
```

O:

```text
verdadero → provocar error
falso     → no provocar error
```

O:

```text
verdadero → dormir 10 s
falso     → responder inmediatamente
```

O finalmente:

```text
SQL ejecutada
→ servidor realiza DNS/HTTP a Burp Collaborator
```

---

# Burp Suite que tendremos que dominar

En este módulo vamos a reforzar y añadir:

- Proxy → Intercept;
- Proxy → HTTP history;
- Repeater para iterar payloads;
- Intruder para extracción carácter por carácter;
- posiciones `§...§`;
- payload sets alfanuméricos;
- comparar Status, Length y contenido;
- observar tiempos de respuesta;
- Resource pools con concurrencia 1 cuando el tiempo importa;
- Burp Collaborator para OAST;
- Decoder / URL encoding cuando sea necesario;
- extensiones oficiales cuando el lab las requiera, por ejemplo Hackvertor en el lab XML.

---

# Método mental Practitioner

```text
1. ¿Dónde está el input?
2. ¿Parece SQLi?
3. ¿Tengo respuesta visible o es blind?
4. Si UNION: ¿cuántas columnas?
5. ¿qué columnas aceptan texto?
6. ¿qué DBMS parece ser?
7. ¿qué información necesito realmente?
8. ¿qué señal puedo observar?
9. ¿manual con Repeater o repetitivo con Intruder?
10. ¿necesito OAST?
```

---

# Orden de estudio inmediato

Vamos a empezar por los cuatro labs UNION, porque se construyen uno encima del otro:

```text
Lab 1: número de columnas
        ↓
Lab 2: cuál acepta texto
        ↓
Lab 3: extraer username/password
        ↓
Lab 4: concatenarlos si solo una columna sirve
```

No avanzar a blind SQLi hasta entender esta cadena.

---

# Referencias oficiales

- Learning path: https://portswigger.net/web-security/learning-paths/sql-injection
- Tema SQL Injection: https://portswigger.net/web-security/sql-injection
- Cheat sheet: https://portswigger.net/web-security/sql-injection/cheat-sheet

## Regla rápida

**Practitioner SQLi no es aprender payloads más largos; es aprender a deducir la estructura y extraer información aunque la aplicación no te la entregue directamente.**
