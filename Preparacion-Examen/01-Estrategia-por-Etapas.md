# Estrategia por etapas

Las guías comunitarias revisadas convergen en organizar la resolución por **objetivo**, no por una lista aleatoria de vulnerabilidades.

> Esto es una heurística de preparación. No es una lista oficial ni garantiza qué aparecerá en un examen concreto.

## Fase 1 — Conseguir acceso a cualquier usuario

Objetivo mental:

```text
Anónimo
  ↓
obtener sesión / credenciales / acceso válido
```

Prioridades recurrentes en DCKento, DingyShark, botesjuan/nazori y n3oari:

- Authentication y lógica de login/password reset;
- XSS y DOM-based vulnerabilities;
- HTTP Request Smuggling;
- Web Cache Poisoning / Web Cache Deception;
- Host Header attacks;
- OAuth;
- APIs y endpoints no documentados;
- CSRF en escenarios concretos;
- descubrimiento de contenido e information disclosure.

### Pregunta rápida

> ¿Qué funcionalidad podría permitirme obtener una sesión válida o hacer que otro usuario ejecute/entregue algo útil?

---

## Fase 2 — Escalar a administrador o comprometer datos del admin

Objetivo mental:

```text
Usuario normal
  ↓
admin / account takeover / acceso a datos privilegiados
```

Prioridades recurrentes:

- Broken Access Control / IDOR / roles manipulables;
- SQL Injection;
- Authentication y password reset;
- CSRF;
- JWT;
- OAuth;
- CORS;
- Prototype Pollution;
- GraphQL / API authorization;
- NoSQL Injection;
- Insecure Deserialization en ciertos flujos.

### Pregunta rápida

> Ya tengo una identidad. ¿Dónde confía la aplicación en un rol, ID, token o flujo que yo pueda modificar?

---

## Fase 3 — Leer `/home/carlos/secret`

Objetivo mental:

```text
Acceso privilegiado
  ↓
filesystem / ejecución / request interna
  ↓
/home/carlos/secret
```

Prioridades recurrentes:

- Path Traversal / LFI;
- OS Command Injection;
- File Upload;
- SSRF;
- XXE / XInclude;
- SSTI;
- Insecure Deserialization;
- Server-side Prototype Pollution;
- SQLi cuando permite lectura de archivo / RCE / OOB;
- funciones administrativas que generan PDFs, procesan imágenes, importan XML, plantillas o URLs.

### Pregunta rápida

> ¿Qué funcionalidad del servidor toca archivos, ejecuta comandos, procesa plantillas/XML, hace requests o transforma contenido?

---

# Orden de pensamiento

No probar todo contra todo.

```text
1. Definir el objetivo de la fase.
2. Enumerar la funcionalidad disponible.
3. Asociar cada funcionalidad a familias de vulnerabilidades probables.
4. Hacer pruebas baratas primero.
5. Confirmar con una POC mínima.
6. Explotar solo lo necesario para pasar a la siguiente fase.
7. Repetir.
```

## Ejemplos de mapeo funcionalidad → hipótesis

| Funcionalidad observada | Hipótesis prioritarias |
|---|---|
| Login / remember-me / 2FA | Authentication, brute force logic, cookies, MFA bypass |
| Forgot password | Host header, token leakage, logic flaws |
| Search / comentarios | XSS, SQLi, cache poisoning |
| Perfil con `role` / `id` | Access Control, mass assignment, IDOR |
| Stock check / URL input | SSRF, XXE según formato |
| Upload avatar | File Upload, path traversal, content-type validation |
| Import XML | XXE / XInclude |
| Template / email preview | SSTI |
| PDF report / image fetch | SSRF, command injection según backend |
| Parámetro filename/path | Path Traversal |
| Feedback con shell-like processing | OS Command Injection |
| JWT | JWT attacks / authorization |
| API / GraphQL | hidden fields, authorization, IDOR, introspection |

## Regla para evitar rabbit holes

Si una hipótesis:

- no tiene una señal clara;
- no cambia la respuesta;
- requiere demasiadas suposiciones;
- o no ayuda al objetivo de la fase;

baja su prioridad y vuelve a enumerar.
