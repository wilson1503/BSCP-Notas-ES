# Guía oficial de examen — PortSwigger

> Esta es la referencia principal de esta carpeta. Las heurísticas comunitarias deben interpretarse a la luz de estas reglas oficiales.

## Formato actual del BSCP

Según la guía oficial de PortSwigger consultada en agosto de 2026:

- duración: **4 horas**;
- **2 aplicaciones vulnerables**;
- cada aplicación se completa en **3 etapas**.

### Etapa 1

```text
Acceder a cualquier cuenta de usuario.
```

### Etapa 2

```text
Usar esa cuenta para acceder a /admin,
por escalación de privilegios o comprometiendo al administrador.
```

### Etapa 3

```text
Usar la interfaz admin para leer:
/home/carlos/secret

y enviar el valor con Submit solution.
```

## Las etapas se hacen en orden

PortSwigger indica explícitamente que intentar saltarse el objetivo actual suele ser perder tiempo.

```text
sin usuario → trabajar Etapa 1
con usuario → trabajar Etapa 2
con admin   → trabajar Etapa 3
```

Esta regla justifica la organización por fases de nuestras cheat sheets.

## No hace falta adivinar rutas aleatorias

PortSwigger aclara que **no oculta intencionalmente los archivos o páginas que contienen las vulnerabilidades** y que no es necesario adivinar carpetas, filenames o parameter names.

Consecuencia práctica:

```text
enumerar bien la funcionalidad real > brute-force ciego de miles de rutas
```

Content Discovery sigue siendo útil cuando existen pistas concretas, pero no debemos convertir el examen en directory brute forcing sin evidencia.

## Hay que explotar, no solo detectar

Una POC de `alert(1)` o detectar SQLi no completa una etapa por sí sola.

Ejemplos oficiales:

- XSS → debe convertirse en impacto contra el usuario simulado, por ejemplo obtener su sesión si eso es lo que permite avanzar;
- SQLi → extraer el dato/credencial necesario y usarlo;
- la explotación debe llevar al objetivo de la etapa.

Regla mental:

```text
finding → exploit → objetivo de fase
```

## Tráfico saliente restringido

Los servidores vulnerables no pueden conectarse libremente a Internet.

PortSwigger permite específicamente el uso de:

- **public Burp Collaborator server**;
- **integrated exploit server**.

Por eso nuestras POCs OAST usan placeholders de Collaborator y no infraestructura externa arbitraria.

## Scanner dirigido

PortSwigger recomienda usar **Burp Scanner sobre páginas e insertion points seleccionados**.

No es viable depender de un full application scan durante el tiempo del examen.

Workflow recomendado:

```text
mapear app
→ detectar request/insertion point prometedor
→ targeted scan
→ revisar finding
→ validar/explotar manualmente
```

## Encoding y bypasses

La guía oficial señala específicamente que el examen exige capacidad para **adaptar ataques y superar defensas rotas mediante encoding/obfuscation**.

Por eso este repo mantiene una cheat sheet separada de:

- URL encoding;
- double encoding;
- representaciones alternativas;
- diferencias de parsing.

## Navegador de la víctima

La víctima simulada usa **Chromium**.

Para XSS, priorizar vectores compatibles con Chrome/Chromium y probar primero en Burp Browser o Chrome.

## Software/herramientas

PortSwigger señala a **Burp Suite Professional** como herramienta esencial.

También menciona que determinadas vulnerabilidades pueden ser más fáciles con herramientas usadas en labs Practitioner, como:

- `ysoserial`;
- `HTTP Request Smuggler`.

No asumir que cualquier herramienta externa es apropiada para el objetivo.

## Preparación oficial

PortSwigger recomienda para quien todavía está aprendiendo:

1. completar temas y labs **Apprentice + Practitioner**;
2. hacer Mystery Labs al terminar cada tema;
3. practicar Mystery Labs aleatorios sin contexto;
4. completar Practice Exam;
5. leer hints/guidance antes del examen.

## Labs oficiales especialmente seleccionados para preparación

En la preparación oficial actual, PortSwigger destaca específicamente ejercicios que refuerzan habilidades importantes para el examen. Conviene poder resolverlos sin solución antes de considerar la preparación final completa:

1. **Exploiting cross-site scripting to steal cookies**.
2. **Blind SQL injection with out-of-band data exfiltration**.
3. **Forced OAuth profile linking**.
4. **Brute-forcing a stay-logged-in cookie**.
5. **Exploiting HTTP request smuggling to capture other users' requests**.
6. **SSRF with blacklist-based input filter**.
7. **SQL injection with filter bypass via XML encoding**.
8. **Discovering vulnerabilities quickly with targeted scanning**.

Este listado **no es una lista de vulnerabilidades garantizadas del examen**. PortSwigger aclara que se selecciona para desarrollar habilidades como:

- cross-user exploitation;
- OAST / exfiltración fuera de banda;
- bypass de filtros y encoding;
- identificación rápida;
- scanning dirigido.

## Mystery Labs

La preparación oficial incluye Mystery Labs **Practitioner** sin contexto.

Objetivo real:

```text
no saber qué vulnerabilidad buscas
→ mapear superficie
→ reconocer comportamiento vulnerable
→ seleccionar técnica correcta
→ explotar
```

Cuando ya resolvamos Mystery Labs de forma consistente, empezar a cronometrarlos.

## Practice Exam

Actualmente el Practice Exam tiene:

- **1 aplicación**;
- **2 horas**;
- formato similar al examen real;
- posibilidad de repetirlo.

El examen real tiene 2 aplicaciones y 4 horas.

No ir al examen real hasta poder aprobar el Practice Exam de forma reproducible.

## Consejos oficiales al atascarse

PortSwigger recomienda:

- probar ataques contra tu propio browser antes de enviarlos a la víctima;
- comparar el escenario actual con el lab del que adaptaste la técnica;
- identificar las suposiciones que estás haciendo y comprobarlas;
- evitar tunnel vision;
- volver a mapear la superficie si no encuentras la vulnerabilidad;
- usar targeted scanning si estás gastando demasiado tiempo en probing manual.

## Regla de esta carpeta

```text
PortSwigger oficial = reglas del juego
Comunidad = optimización, organización y memoria rápida
```

Antes del examen real, revisar nuevamente las páginas oficiales porque formato, requisitos o recomendaciones pueden cambiar.
