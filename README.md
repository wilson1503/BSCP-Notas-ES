# BSCP-Notas-ES

Apuntes personales en español para preparar **Burp Suite Certified Practitioner (BSCP)** mediante PortSwigger Web Security Academy.

> Objetivo: entender las vulnerabilidades y poder resolver los laboratorios por cuenta propia. Los nombres técnicos, payloads y elementos de Burp se mantienen en inglés para acostumbrarse al lenguaje del examen.

## Fuente de verdad

1. **PortSwigger Web Security Academy y documentación oficial** — fuente principal.
2. **DCKento** — metodología y puntos de entrada.
3. **DingyShark** — enfoque práctico y organización para BSCP.
4. **ifrane / D4mianWayne / secure-the-code** — referencias adicionales, payloads y contraste.

Los repositorios de terceros se usan como referencia; estas notas se redactan y verifican contra PortSwigger, sin copiar soluciones completas.

## Ruta actual

### 01 — Server-side vulnerabilities (Apprentice)

- [x] [Path Traversal](01-Server-Side-Vulnerabilities/01-Path-Traversal.md) — primer laboratorio completado
- [ ] [Access Control](01-Server-Side-Vulnerabilities/02-Access-Control.md) — siguiente tema
- [ ] Authentication
- [ ] SSRF
- [ ] File Upload Vulnerabilities
- [ ] OS Command Injection
- [ ] SQL Injection

➡️ [Abrir módulo](01-Server-Side-Vulnerabilities/README.md)

## Estructura

- [`00-Ruta-BSCP/`](00-Ruta-BSCP/) — plan, progreso y metodología.
- [`01-Server-Side-Vulnerabilities/`](01-Server-Side-Vulnerabilities/) — primer learning path.
- [`Labs/`](Labs/) — notas de laboratorios realizados.
- [`CheatSheets/`](CheatSheets/) — referencias rápidas para repaso.
- [`Payloads/`](Payloads/) — payloads explicados por contexto.
- [`Templates/`](Templates/) — plantillas de estudio.
- [`Referencias/`](Referencias/) — fuentes oficiales y comunitarias.

## Regla de estudio

**Teoría → intento propio → análisis en Burp → lab resuelto → documentar qué funcionó y por qué.**

No guardar solamente un payload: guardar también **qué señal llevó a probarlo, qué defensa existía y por qué el bypass funcionó**.

## Uso responsable

Todo el material práctico de este repositorio está pensado para **Web Security Academy, laboratorios propios o sistemas con autorización explícita**.
