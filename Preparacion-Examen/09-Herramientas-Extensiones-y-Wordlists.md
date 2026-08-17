# Herramientas, extensiones y wordlists

> Lista para preparar el entorno **antes** del Practice Exam / examen real. No instalar herramientas por instalar: cada una debe resolver una tarea concreta.

## Burp Suite Professional — esenciales

### Proxy / HTTP history

Base de mapeo y análisis manual.

### Repeater

Validación manual y explotación controlada.

### Intruder

Fuzzing/brute force autorizado de:

- usernames/passwords;
- MFA;
- blind SQLi;
- IDs;
- internal IPs para SSRF confirmado;
- payload encodings.

### Scanner / targeted scan

PortSwigger recomienda scanning dirigido sobre páginas o insertion points prometedores.

### Collaborator

Para OAST:

- blind SSRF;
- blind XXE;
- blind command injection;
- OOB SQLi;
- XSS cross-user;
- Host Header/password reset poisoning.

### DOM Invader

Útil para:

- DOM XSS;
- web messages;
- prototype pollution.

### Decoder

Para URL/Base64/hex/HTML encoding.

### Comparer

Para diferencias sutiles de respuestas.

---

# Extensiones/herramientas destacadas

## HTTP Request Smuggler

PortSwigger la menciona oficialmente como herramienta útil para técnicas Practitioner de request smuggling.

Preparar antes del examen:

- saber instalarla desde BApp Store;
- saber lanzar checks;
- saber validar manualmente un finding;
- no depender de ella sin entender CL/TE/H2 behavior.

## Hackvertor

Muy útil para transformaciones/encoding dentro de Burp.

Casos destacados por la comunidad:

- XML encoding para bypass de filtros SQLi;
- encodings anidados;
- transformación dinámica de payloads.

PortSwigger incluye oficialmente un lab de **SQL injection with filter bypass via XML encoding**, así que entender este tipo de transformación tiene prioridad.

## ysoserial

PortSwigger lo menciona como herramienta útil para determinados labs Practitioner de insecure deserialization.

No usar hasta entender:

- formato serializado;
- lenguaje/runtime;
- gadget chain;
- integrity/signing.

## Turbo Intruder

Puede ser útil para:

- race conditions;
- grandes volúmenes con timing preciso;
- ciertos ataques de concurrencia.

Aprender solo cuando lleguemos oficialmente a Race Conditions.

## sqlmap

Comunidad lo usa como acelerador después de confirmar SQLi.

Uso correcto para estas notas:

```text
manual POC → identificar vector → sqlmap si ahorra tiempo
```

No usar sqlmap como sustituto de saber:

- UNION;
- boolean blind;
- time blind;
- DBMS syntax;
- extracción manual mínima.

Ejemplo desde request guardada:

```bash
sqlmap -r request.txt -p PARAM --level=5 --risk=3 --batch
```

## ffuf / Content Discovery

botesjuan recomienda content discovery y wordlists como parte del mapeo.

Sin embargo, la guía oficial actual del BSCP dice que no hace falta **adivinar** carpetas, filenames o parameter names para localizar las vulnerabilidades del examen.

Uso razonable:

- entrenamiento/labs;
- pistas concretas;
- `robots.txt`, source o JS sugieren rutas;
- no dedicar gran parte del examen a fuzzing ciego.

---

# Wordlists oficiales de PortSwigger

La preparación oficial proporciona/usa listas para:

- usernames de labs de Authentication;
- passwords de labs de Authentication;
- delimiters para Web Cache Deception.

Antes de la fase final, guardar acceso rápido a las versiones oficiales actuales.

## Wordlists comunitarias

botesjuan mantiene una wordlist compilada para labs/content discovery.

Tratarla como recurso secundario, no como requisito del examen.

---

# Preparación del Burp Project

Antes del examen real:

- [ ] Burp Suite Professional actualizado y estable;
- [ ] licencia activa;
- [ ] browser funcionando;
- [ ] Collaborator probado;
- [ ] extensiones necesarias instaladas;
- [ ] saber crear un **project file** persistente;
- [ ] espacio en disco;
- [ ] Java/herramientas externas necesarias probadas;
- [ ] no actualizar todo cinco minutos antes del examen.

PortSwigger exige usar un **Burp project file** durante todo el examen y puede solicitarlo posteriormente.

---

# Tabs / organización sugerida

Repeater:

```text
00-BASELINE
01-AUTH
02-ADMIN
03-SQLI
04-XSS
05-SSRF
06-FILE
07-OAST
```

Notes por aplicación:

```text
APP1
- Stage 1 evidence
- Stage 2 evidence
- Stage 3 evidence

APP2
- Stage 1 evidence
- Stage 2 evidence
- Stage 3 evidence
```

---

# Qué NO preparar como dependencia crítica

Evitar que el plan dependa de:

- una herramienta online que pueda estar caída;
- servidores externos propios para OAST (el examen restringe outbound traffic);
- scripts que nunca hayas probado;
- extensiones de Burp que no entiendas;
- cientos de wordlists irrelevantes.

La herramienta principal sigue siendo **Burp Suite Professional**.
