# Fuentes y criterio editorial

Esta sección existe para que el material de examen sea útil sin mezclarlo con la fuente de aprendizaje oficial.

## Prioridad de fuentes

### Nivel 1 — PortSwigger

Fuente autoritativa para:

- formato y reglas actuales del examen;
- objetivos de cada etapa;
- requisitos técnicos;
- learning materials;
- labs y soluciones oficiales;
- Burp Suite workflows;
- preparación oficial, Mystery Labs y Practice Exam;
- XSS/SQLi/SSRF cheat sheets oficiales cuando existan.

Antes del examen real hay que volver a verificar estas páginas porque pueden cambiar.

### Nivel 2 — Guías comunitarias BSCP

Se usan para:

- organización por etapas;
- prioridades;
- payload collections;
- checklists;
- estrategias de tiempo;
- selección de herramientas;
- ideas para reconocer vulnerabilidades más rápido.

No se consideran evidencia de qué aparecerá en un intento concreto.

---

## Repositorios revisados

### DCKento — Burp-Suite-Certified-Practitioner-Notes

Aporta especialmente:

- separación Stage 1 / Stage 2 / Stage 3;
- familias de vulnerabilidades por objetivo;
- bypasses genéricos;
- recordatorios rápidos para XSS, SQLi, CSRF, CORS, SSRF, OS Command Injection, SSTI y otras clases;
- colección compacta de técnicas para examen.

Uso en nuestro repo:

```text
priorización por etapa + ideas de checklist
```

### DingyShark — BurpSuiteCertifiedPractitioner

Aporta:

- estrategia explícita por tres etapas;
- lista de técnicas por objetivo;
- énfasis en targeted scan;
- recomendación de Mystery Labs sin hints;
- enfoque práctico de adaptar payloads de labs al objetivo real de la fase.

Uso:

```text
workflow rápido + prioridades + explotación orientada al objetivo
```

### n3oari — BSCP-EXAM-GUIDE-BY-N3OARI-2026

Aporta una guía reciente y muy organizada con:

- cheatsheets por categoría;
- labs considerados importantes por el autor;
- utilidades;
- matriz verde/amarillo/rojo por fase;
- Authentication, SQLi, SSRF, Path Traversal, File Upload, OS Injection, headers y obfuscation;
- recordatorio de encadenar vulnerabilidades.

Uso:

```text
matriz de prioridad + payloads compactos + organización por temas
```

### botesjuan — Burp-Suite-Certified-Practitioner-Exam-Study

Repositorio de gran tamaño basado en más de 100 labs.

Aporta:

- scanning dirigido;
- content discovery;
- foothold / privilege escalation / data exfiltration;
- payloads separados;
- wordlists;
- enfoque de identificación rápida;
- tips de Burp y Mystery Labs.

Uso:

```text
recon + scanner + señales + payload library
```

### nazori-eu — BSCP-Notes

Organiza notas de estudio/paso del examen en:

- Foothold;
- Privilege Escalation;
- Data Exfiltration;
- appendix de scripts/payloads/wordlists.

Tiene fuerte solapamiento metodológico con botesjuan y sirve como contraste adicional.

### zeeshan811 — BSCP_Notes

Aporta:

- payload collections;
- XSS/cookie-stealing patterns;
- Authentication/2FA;
- SQLi;
- request smuggling;
- CSRF/CORS;
- herramientas y attack patterns.

Uso:

```text
contrastar payloads y no depender de una sola colección
```

---

# Regla para incorporar contenido

Una técnica comunitaria entra al cheat sheet principal si cumple al menos una de estas condiciones:

1. aparece en varias fuentes independientes;
2. corresponde a un lab/técnica oficial de PortSwigger;
3. es una POC pequeña de alta utilidad;
4. resuelve una tarea repetitiva de Burp;
5. ayuda a decidir rápidamente entre hipótesis.

Si es muy específica o dudosa, queda como referencia secundaria y no como recomendación principal.

## Evitar copiar por copiar

No importar:

- dominios OAST reales de otros autores;
- tokens/sesiones;
- URLs de labs expirados;
- payloads duplicados sin explicación;
- herramientas externas que no aporten una ventaja clara;
- afirmaciones sobre contenido real del examen que puedan violar NDA o no sean verificables.

## Formato de payloads de este repo

Preferir:

```text
COLLABORATOR
LAB
TARGET
USERNAME
```

sobre valores reales.

Cada payload idealmente debe acompañarse de:

- contexto;
- señal esperada;
- limitación;
- herramienta recomendada.

## Sobre la matriz de probabilidad/prioridad

No llamarla "probabilidad real del examen".

Nombre correcto en estas notas:

> **prioridad comunitaria por objetivo/fase**

La tabla de n3oari refleja la experiencia/metodología del autor. DCKento, DingyShark y botesjuan ayudan a validar que muchas de las asociaciones por etapa son razonables, pero ninguna fuente comunitaria puede garantizar el contenido de un intento.

## Mantenimiento futuro

Cuando estudiemos oficialmente cada tema:

```text
1. PortSwigger define la técnica.
2. Se completan labs.
3. Se revisa esta carpeta.
4. Se eliminan payloads redundantes o incorrectos.
5. Se añaden únicamente los atajos que entendemos.
6. Se registra qué técnica nos cuesta reconocer.
```

El objetivo final es que la carpeta sea **pequeña, rápida y confiable**, no la colección más grande posible.
