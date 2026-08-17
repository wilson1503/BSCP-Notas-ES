# Matriz comunitaria de prioridad

Esta tabla resume la heurística por fases publicada por **n3oari (2026)** y la contrasta conceptualmente con la organización por etapas de **DCKento**, **DingyShark** y **botesjuan/nazori**.

> **No es información oficial de PortSwigger ni una predicción del examen.** Úsala únicamente como guía para decidir qué hipótesis probar primero cuando el tiempo sea limitado.

## Leyenda

- 🟢 = prioridad alta / encaja naturalmente con el objetivo de la fase.
- 🟡 = posible, secundaria o dependiente del contexto.
- 🔴 = normalmente baja prioridad para ese objetivo concreto.

| Categoría | Fase 1: acceso inicial | Fase 2: admin | Fase 3: secret |
|---|:---:|:---:|:---:|
| XSS | 🟢 | 🟢 | 🟡 |
| DOM vulnerabilities | 🟢 | 🟢 | 🟡 |
| SQL Injection | 🔴 | 🟢 | 🟡 |
| NoSQL Injection | 🔴 | 🟢 | 🟡 |
| CSRF | 🟢 | 🟢 | 🔴 |
| SSRF | 🔴 | 🟡 | 🟢 |
| Authentication | 🟢 | 🟢 | 🔴 |
| OAuth | 🟢 | 🟢 | 🔴 |
| OS Command Injection | 🔴 | 🔴 | 🟢 |
| Web Cache Poisoning | 🟢 | 🟢 | 🔴 |
| Web Cache Deception | 🟢 | 🟢 | 🔴 |
| File Upload | 🔴 | 🔴 | 🟢 |
| Host Header Injection | 🟡 | 🟡 | 🟢 |
| Insecure Deserialization | 🔴 | 🔴 | 🟢 |
| HTTP Request Smuggling | 🟢 | 🟢 | 🔴 |
| API testing | 🟢 | 🟢 | 🔴 |
| CORS | 🟢 | 🟢 | 🔴 |
| Prototype Pollution | 🟢 | 🟢 | 🟢 |
| JWT | 🟢 | 🟢 | 🔴 |
| GraphQL | — | 🟢 | 🔴 |
| XXE | 🔴 | 🟡 | 🟢 |
| SSTI | 🔴 | 🔴 | 🟢 |
| Broken Access Control | 🔴 | 🟢 | 🔴 |
| Path Traversal | 🔴 | 🔴 | 🟢 |
| Race Conditions | 🟢 | — | 🟢 |

## Consenso fuerte entre repositorios

### Fase 1

Las listas de DCKento, DingyShark y botesjuan coinciden especialmente en:

```text
Authentication
XSS / DOM
HTTP Request Smuggling
Web Cache Poisoning
Host Header
Content Discovery
```

### Fase 2

El consenso más claro es:

```text
Access Control
SQL Injection
CSRF
Authentication / password reset
JWT / OAuth
API / GraphQL authorization
```

### Fase 3

El consenso más fuerte es:

```text
Path Traversal
SSRF
XXE
OS Command Injection
SSTI
File Upload
Insecure Deserialization
```

## Cómo usar la matriz correctamente

Ejemplo: si estás en Fase 3 y encuentras un parámetro `filename`, no tendría sentido dedicar primero 20 minutos a probar CSRF. **Path Traversal** encaja mejor con el objetivo y con la superficie observada.

Pero la superficie siempre manda:

```text
fase + funcionalidad + evidencia > tabla
```

La matriz sirve para desempatar prioridades, no para ignorar señales reales.

## Nota de mantenimiento

Cuando terminemos el learning path y practiquemos Mystery Labs, esta tabla deberá ajustarse con:

- qué categorías reconocemos más lento;
- qué payloads generan señales más claras;
- cuáles son nuestros rabbit holes personales;
- cuánto tiempo medio tardamos en identificar cada familia.
