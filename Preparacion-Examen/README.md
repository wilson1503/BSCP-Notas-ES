# Preparación para el examen BSCP

> **No es material de aprendizaje primario.** Esta carpeta está pensada para usarse cuando ya se hayan estudiado los temas con PortSwigger Web Security Academy y se busque ganar velocidad, priorizar hipótesis y tener referencias rápidas durante la preparación final.

## Objetivo

Concentrar en español lo más útil de varias guías comunitarias de preparación para BSCP, manteniendo separado este material de las notas oficiales de estudio.

La estructura se basa en una idea que se repite en varias guías comunitarias: pensar cada aplicación del examen como una cadena de objetivos y priorizar las vulnerabilidades que razonablemente pueden llevar al objetivo de cada fase.

## Regla principal

```text
APRENDER
PortSwigger → teoría → labs → workflow oficial

OPTIMIZAR
Preparación-Examen → reconocimiento rápido → priorización → payloads → checklists
```

Si algo de esta carpeta contradice a PortSwigger, **PortSwigger gana**.

## Índice

1. [Estrategia por etapas](01-Estrategia-por-Etapas.md)
2. [Matriz comunitaria de prioridad](02-Matriz-Prioridad-Comunitaria.md)
3. [Reconocimiento y enumeración](03-Reconocimiento-y-Enumeracion.md)
4. [Workflow rápido de Burp](04-Workflow-Burp-Examen.md)
5. [Payloads de alto valor](05-Payloads-Alto-Valor.md)
6. [Bypasses, encoding y headers](06-Bypasses-Encoding-Headers.md)
7. [Checklist rápido por vulnerabilidad](07-Checklist-Rapido-por-Vulnerabilidad.md)
8. [Fuentes y criterio editorial](08-Fuentes-y-Criterio.md)

### Cheat sheets por fase

- [Fase 1 — Foothold / acceso inicial](CheatSheets/01-Fase-1-Foothold.md)
- [Fase 2 — Escalación de privilegios](CheatSheets/02-Fase-2-Privilege-Escalation.md)
- [Fase 3 — Acceso al secreto / filesystem](CheatSheets/03-Fase-3-Data-Exfiltration.md)

## Qué se recopila aquí

- prioridades por etapa;
- señales que permiten reconocer una vulnerabilidad rápidamente;
- endpoints, parámetros y estructuras que conviene revisar;
- payloads pequeños y reutilizables;
- pruebas mínimas antes de descartar una hipótesis;
- uso dirigido de Scanner, Repeater, Intruder, Collaborator y DOM Invader;
- técnicas de encoding y bypass que se repiten entre categorías;
- recordatorios de cadenas de vulnerabilidades;
- errores de tiempo frecuentes y rabbit holes.

## Qué NO se pretende hacer

- copiar soluciones completas de todos los labs;
- sustituir la Web Security Academy;
- tratar una matriz comunitaria como si fuera una garantía sobre el examen;
- guardar cientos de payloads sin contexto;
- memorizar atajos antes de entender la vulnerabilidad.

## Estado

Esta sección está **preparada para uso futuro**. Se seguirá enriqueciendo a medida que se estudien los temas, pero no hace falta leerla durante la fase inicial de aprendizaje.
