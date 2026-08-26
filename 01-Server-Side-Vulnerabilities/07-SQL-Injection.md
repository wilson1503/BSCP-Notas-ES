# 07 — SQL Injection (SQLi) — Apprentice

> **Método de estudio:** seguir primero PortSwigger Web Security Academy y sus workflows oficiales.

## Estado

✅ **SQL Injection — Apprentice completado.**

## Qué es SQL Injection

SQL injection ocurre cuando una aplicación incorpora datos controlados por el usuario dentro de una consulta SQL de forma insegura.

```text
input del usuario
→ concatenado dentro de SQL
→ la base de datos interpreta parte del input como sintaxis
→ cambia la lógica de la consulta
```

---

# Labs completados

## 1. WHERE clause allowing retrieval of hidden data ✅

> [Guía completa](../Labs/SQL-Injection/01-WHERE-clause-retrieving-hidden-data.md)

Payload oficial:

```text
'+OR+1=1--
```

Conceptualmente:

```sql
' OR 1=1--
```

Aprendizaje:

```text
'      → cerrar string
OR 1=1 → condición siempre verdadera
--     → comentar el resto
```

Objetivo conseguido: mostrar productos que debían permanecer ocultos.

## 2. SQL injection allowing login bypass ✅

> [Guía completa](../Labs/SQL-Injection/02-SQLi-login-bypass.md)

Payload oficial:

```text
administrator'--
```

Aprendizaje:

```text
administrator' → cerrar el valor username
--             → comentar la comprobación posterior del password
→ login como administrator
```

---

# Conceptos Apprentice dominados

- `SELECT`;
- `WHERE`;
- `AND` / `OR`;
- strings con `'`;
- condición `1=1`;
- comentarios `--`;
- diferencia entre recuperar datos y subvertir lógica de autenticación;
- observar las consecuencias de SQLi aunque la consulta interna no sea visible.

---

# Burp practicado

```text
Proxy → Intercept
GET /filter?category=...
POST /login
modificar parámetros
Forward
observar la aplicación / Response
```

---

# Checklist

- [x] Puedo explicar SQL Injection con mis palabras.
- [x] Entiendo `SELECT`, `WHERE`, `AND` y `OR`.
- [x] Entiendo por qué `'` puede cerrar un string SQL.
- [x] Entiendo `1=1`.
- [x] Entiendo `--` como comentario.
- [x] Lab hidden data completado.
- [x] Lab login bypass completado.

---

# Siguiente etapa

➡️ [SQL Injection — Practitioner](../02-Practitioner/01-SQL-Injection.md)

Ahora avanzamos a:

```text
UNION SELECT
→ número de columnas
→ tipos de columna
→ extracción de otras tablas
→ enumeración del DBMS
→ blind SQLi
→ errores / time delays
→ OAST
→ XML encoding bypass
```

# Regla rápida

**SQL Injection ocurre cuando algo que debería ser solo DATO termina siendo interpretado como parte de la CONSULTA SQL.**
