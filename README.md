# BSCP-Notas-ES

Apuntes personales en español para preparar **Burp Suite Certified Practitioner (BSCP)** mediante PortSwigger Web Security Academy.

> Objetivo: aprender los conceptos, técnicas y flujos de Burp de la forma en que los enseña PortSwigger, y llegar a resolver los laboratorios sin consultar soluciones.

## Fuente de verdad

### Material de estudio

La **fuente principal y autoritativa es PortSwigger**:

1. Web Security Academy.
2. Learning paths oficiales.
3. Soluciones oficiales de los labs.
4. Documentación oficial de Burp Suite.
5. Guías oficiales de preparación, Mystery Labs y Practice Exam del BSCP.

### Regla editorial del repositorio

Durante la fase de aprendizaje:

- se mantiene el **orden conceptual de PortSwigger** siempre que sea posible;
- se conserva la terminología técnica que usa PortSwigger;
- si un laboratorio tiene una técnica o workflow concreto en su solución oficial, esa técnica se documenta **primero y como método de estudio principal**;
- una técnica alternativa que también resuelva el lab no sustituye al procedimiento que PortSwigger intenta enseñar;
- los workflows alternativos, automatizaciones, payload collections, scripts y atajos se separarán posteriormente en una sección de **Preparación para el examen**;
- si existe una discrepancia entre estas notas y PortSwigger, **PortSwigger gana**.

Los repositorios comunitarios como DCKento, DingyShark, ifrane, D4mianWayne y secure-the-code quedan como referencias secundarias. Se usarán principalmente más adelante para crear material de repaso y agilidad para el examen, nunca para reemplazar el aprendizaje oficial.

## Dos fases distintas

### Fase 1 — Aprender como enseña PortSwigger

```text
Teoría oficial
      ↓
Intento propio
      ↓
Lab
      ↓
Si es necesario, revisar solución oficial
      ↓
Reproducir y entender el workflow oficial
      ↓
Documentar en español qué ocurrió y por qué
```

Aquí importa aprender también **qué herramienta de Burp usa el lab**: Proxy, request interception, response interception, Repeater, Intruder, Decoder, Collaborator, etc.

### Fase 2 — Preparación específica para el examen

Se construirá más adelante y de forma separada. Incluirá, entre otras cosas:

- cheat sheets en español;
- payloads organizados por contexto;
- checklists de reconocimiento;
- comandos y snippets útiles;
- workflows rápidos en Burp;
- consejos de otros candidatos y repositorios comunitarios, contrastados con PortSwigger;
- Mystery Labs;
- Practice Exam;
- gestión del tiempo y metodología de enumeración.

La finalidad será **ganar velocidad sin confundir los atajos del examen con el material usado para aprender cada vulnerabilidad**.

## Ruta actual

### 01 — Server-side vulnerabilities (Apprentice)

- [x] [Path Traversal](01-Server-Side-Vulnerabilities/01-Path-Traversal.md) — primer laboratorio completado
- [x] [Access Control](01-Server-Side-Vulnerabilities/02-Access-Control.md) — primeros laboratorios en progreso
- [ ] Authentication
- [ ] SSRF
- [ ] File Upload Vulnerabilities
- [ ] OS Command Injection
- [ ] SQL Injection

➡️ [Abrir módulo](01-Server-Side-Vulnerabilities/README.md)

## Conceptos básicos clave

Antes y durante los labs, usar esta sección como referencia rápida:

➡️ [Abrir Conceptos Básicos Clave](Conceptos-Basicos-Clave/README.md)

Incluye `robots.txt`, descubrimiento de rutas, endpoints, parámetros, métodos HTTP, status codes, headers, cookies, sesiones, Authentication vs Authorization, URL encoding, workflow básico de Burp y Security by Obscurity / rutas expuestas en JavaScript.

## Estructura

- [`00-Ruta-BSCP/`](00-Ruta-BSCP/) — plan, progreso y metodología.
- [`Conceptos-Basicos-Clave/`](Conceptos-Basicos-Clave/) — fundamentos web reutilizables.
- [`01-Server-Side-Vulnerabilities/`](01-Server-Side-Vulnerabilities/) — primer learning path.
- [`Labs/`](Labs/) — notas de laboratorios realizados.
- [`CheatSheets/`](CheatSheets/) — reservado para la fase de repaso/examen.
- [`Payloads/`](Payloads/) — reservado para payloads explicados y posteriormente material de agilidad.
- [`Templates/`](Templates/) — plantillas de estudio.
- [`Referencias/`](Referencias/) — fuentes oficiales y comunitarias.

## Regla de estudio

**Entender primero la técnica que PortSwigger está enseñando. Optimizar después.**

No guardar solamente un payload. Para cada lab debemos saber:

- qué concepto estaba enseñando;
- qué señal llevó a identificarlo;
- qué request/response era relevante;
- qué herramienta y workflow de Burp utilizó PortSwigger;
- qué modificación se hizo;
- por qué funcionó;
- qué control de seguridad estaba fallando.

## Uso responsable

Todo el material práctico de este repositorio está pensado para **Web Security Academy, laboratorios propios o sistemas con autorización explícita**.
