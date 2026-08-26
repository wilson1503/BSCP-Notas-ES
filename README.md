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
- los workflows alternativos, automatizaciones, payload collections, scripts y atajos se mantienen separados en **Preparación para el examen**;
- si existe una discrepancia entre estas notas y PortSwigger, **PortSwigger gana**.

### Regla obligatoria para cada laboratorio

Como parte del aprendizaje de Burp Suite, **cada lab debe tener el procedimiento completo en GitHub antes o mientras se realiza**.

Cada guía de lab debe incluir:

```text
1. Objetivo exacto del lab
2. Credenciales/datos proporcionados por PortSwigger
3. Procedimiento oficial paso a paso
4. Pestañas y botones concretos de Burp que se utilizan
5. Request/response que debemos localizar
6. Parámetro/cookie/header/body que se modifica
7. Resultado esperado
8. Por qué funciona
9. Qué habilidad de Burp debemos aprender
```

Si la solución oficial no necesita Burp, eso se indicará claramente. Se puede añadir una práctica complementaria en Burp para aprender la herramienta, pero **nunca sustituyendo el workflow oficial**.

Los repositorios comunitarios se usan como referencias secundarias para crear material de repaso y agilidad, nunca para reemplazar el aprendizaje oficial.

## Dos fases distintas

### Fase 1 — Aprender como enseña PortSwigger

```text
Teoría oficial
      ↓
Procedimiento del lab documentado en GitHub
      ↓
Intento propio siguiendo el workflow
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

Ya existe una sección separada que se irá refinando mientras avanzamos. Recopila reglas oficiales, cheat sheets, prioridades comunitarias, payloads, checklists y workflows rápidos.

➡️ [Abrir Preparación para el examen](Preparacion-Examen/README.md)

## Ruta actual

### 01 — Server-side vulnerabilities (Apprentice)

- [x] [Path Traversal](01-Server-Side-Vulnerabilities/01-Path-Traversal.md) — completado
- [x] [Access Control](01-Server-Side-Vulnerabilities/02-Access-Control.md) — completado
- [x] [Authentication](01-Server-Side-Vulnerabilities/03-Authentication.md) — completado
- [x] [Server-side request forgery (SSRF)](01-Server-Side-Vulnerabilities/04-SSRF.md) — completado
- [x] [File Upload Vulnerabilities](01-Server-Side-Vulnerabilities/05-File-Upload-Vulnerabilities.md) — completado
- [ ] [OS Command Injection](01-Server-Side-Vulnerabilities/06-OS-Command-Injection.md) — **en progreso**
- [ ] SQL Injection

➡️ [Abrir módulo](01-Server-Side-Vulnerabilities/README.md)

## Lab actual

OS Command Injection — único laboratorio Apprentice del bloque:

➡️ [OS command injection, simple case](Labs/OS-Command-Injection/01-OS-command-injection-simple-case.md)

## Conceptos básicos clave

➡️ [Abrir Conceptos Básicos Clave](Conceptos-Basicos-Clave/README.md)

Para OS Command Injection se añadió específicamente:

➡️ [OS Command Injection y separadores del shell](Conceptos-Basicos-Clave/12-OS-Command-Injection-y-Shell-Separators.md)

## Estructura

- [`00-Ruta-BSCP/`](00-Ruta-BSCP/) — plan, progreso y metodología.
- [`Conceptos-Basicos-Clave/`](Conceptos-Basicos-Clave/) — fundamentos web reutilizables.
- [`01-Server-Side-Vulnerabilities/`](01-Server-Side-Vulnerabilities/) — primer learning path.
- [`Labs/`](Labs/) — procedimientos oficiales paso a paso.
- [`Preparacion-Examen/`](Preparacion-Examen/) — material para velocidad, priorización y repaso.
- [`Templates/`](Templates/) — plantillas de estudio.
- [`Referencias/`](Referencias/) — fuentes oficiales y comunitarias.

## Regla de estudio

**Entender primero la técnica que PortSwigger está enseñando. Optimizar después.**

No guardar solamente un payload. Para cada lab debemos saber qué concepto enseña, qué request/response importa, qué herramienta de Burp se utiliza, qué cambia y por qué funciona.

## Uso responsable

Todo el material práctico de este repositorio está pensado para **Web Security Academy, laboratorios propios o sistemas con autorización explícita**.
