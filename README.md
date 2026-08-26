# BSCP-Notas-ES

Apuntes personales en español para preparar **Burp Suite Certified Practitioner (BSCP)** mediante PortSwigger Web Security Academy.

> Objetivo: aprender los conceptos, técnicas y flujos de Burp de la forma en que los enseña PortSwigger, y llegar a resolver los laboratorios sin consultar soluciones.

## Fuente de verdad

La **fuente principal y autoritativa es PortSwigger**:

1. Web Security Academy.
2. Learning paths oficiales.
3. Soluciones oficiales de los labs.
4. Documentación oficial de Burp Suite.
5. Guías oficiales de preparación, Mystery Labs y Practice Exam del BSCP.

### Regla editorial

Durante la fase de aprendizaje:

- se mantiene el orden y terminología de PortSwigger siempre que sea posible;
- el workflow de la solución oficial se documenta primero;
- técnicas alternativas no sustituyen el método que el lab intenta enseñar;
- automatizaciones, payload collections y atajos se mantienen en `Preparacion-Examen/`;
- si existe una discrepancia entre estas notas y PortSwigger, **PortSwigger gana**.

### Regla obligatoria para cada laboratorio

Cada lab debe tener el procedimiento completo en GitHub antes o mientras se realiza:

```text
1. Objetivo exacto
2. Credenciales/datos dados
3. Procedimiento oficial paso a paso
4. Pestañas/botones de Burp
5. Request/response relevante
6. Parámetro/cookie/header/body modificado
7. Resultado esperado
8. Por qué funciona
9. Habilidad de Burp aprendida
```

---

# Progreso

## ✅ Server-side vulnerabilities — Apprentice completado

- [x] [Path Traversal](01-Server-Side-Vulnerabilities/01-Path-Traversal.md)
- [x] [Access Control](01-Server-Side-Vulnerabilities/02-Access-Control.md)
- [x] [Authentication](01-Server-Side-Vulnerabilities/03-Authentication.md)
- [x] [Server-side request forgery (SSRF)](01-Server-Side-Vulnerabilities/04-SSRF.md)
- [x] [File Upload Vulnerabilities](01-Server-Side-Vulnerabilities/05-File-Upload-Vulnerabilities.md)
- [x] [OS Command Injection](01-Server-Side-Vulnerabilities/06-OS-Command-Injection.md)
- [x] [SQL Injection](01-Server-Side-Vulnerabilities/07-SQL-Injection.md)

➡️ [Abrir módulo Apprentice](01-Server-Side-Vulnerabilities/README.md)

## 🟡 Etapa actual — Practitioner

Estamos comenzando por **SQL Injection — Practitioner** para aprovechar los fundamentos de SQLi recién aprendidos.

➡️ [SQL Injection — Practitioner](02-Practitioner/01-SQL-Injection.md)

➡️ [Labs Practitioner](Labs/SQL-Injection-Practitioner/README.md)

Los primeros cuatro labs preparados son el bloque `UNION`:

```text
1. determinar número de columnas
2. encontrar columna compatible con texto
3. recuperar datos de otra tabla
4. recuperar múltiples valores usando una sola columna
```

Referencia conceptual:

➡️ [SQL UNION, NULL y tipos de datos](Conceptos-Basicos-Clave/14-SQL-UNION-NULL-y-Tipos-de-Datos.md)

---

# Dos fases distintas

## Fase 1 — Aprender como enseña PortSwigger

```text
Teoría oficial
      ↓
Procedimiento del lab documentado
      ↓
Intento propio
      ↓
Lab
      ↓
Revisar solución oficial si hace falta
      ↓
Entender y documentar por qué funcionó
```

Aquí importa aprender también qué herramienta usa el lab: Proxy, Repeater, Intruder, Decoder, Collaborator, etc.

## Fase 2 — Preparación específica para el examen

La sección `Preparacion-Examen/` recopila reglas oficiales, cheat sheets, prioridades comunitarias, payloads, checklists y workflows rápidos sin sustituir el aprendizaje de Academy.

➡️ [Abrir Preparación para el examen](Preparacion-Examen/README.md)

---

# Estructura

- [`00-Ruta-BSCP/`](00-Ruta-BSCP/) — plan y progreso.
- [`Conceptos-Basicos-Clave/`](Conceptos-Basicos-Clave/) — fundamentos reutilizables.
- [`01-Server-Side-Vulnerabilities/`](01-Server-Side-Vulnerabilities/) — learning path Apprentice completado.
- [`02-Practitioner/`](02-Practitioner/) — etapa Practitioner actual.
- [`Labs/`](Labs/) — procedimientos oficiales paso a paso.
- [`Preparacion-Examen/`](Preparacion-Examen/) — material para velocidad, priorización y repaso.
- [`Templates/`](Templates/) — plantillas de estudio.
- [`Referencias/`](Referencias/) — fuentes oficiales y comunitarias.

## Regla de estudio

**Entender primero la técnica que PortSwigger está enseñando. Optimizar después.**

## Uso responsable

Todo el material práctico de este repositorio está pensado para **Web Security Academy, laboratorios propios o sistemas con autorización explícita**.
