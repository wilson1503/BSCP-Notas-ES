# Conceptos Básicos Clave

Esta carpeta reúne conceptos que aparecen una y otra vez en Web Security Academy y en el BSCP. La idea es entenderlos antes de memorizar payloads.

> **Fuente principal:** PortSwigger Web Security Academy y documentación oficial de Burp Suite. Estos archivos sirven para explicar los conceptos en español, no para sustituir el learning path ni los workflows oficiales de los labs.

## Regla para esta carpeta

- Si el concepto aparece en PortSwigger, se explica con el mismo significado técnico.
- Los ejemplos deben ayudar a comprender el material oficial.
- Si un lab enseña una función concreta de Burp, el workflow específico del lab tiene prioridad sobre cualquier workflow genérico de esta carpeta.
- Atajos, automatizaciones y técnicas de velocidad se reservan para **Preparación para el examen**.

## Orden recomendado

1. [robots.txt y descubrimiento de rutas](01-Robots-txt-y-Descubrimiento-de-Rutas.md)
2. [Rutas, endpoints y parámetros](02-Rutas-Endpoints-y-Parametros.md)
3. [Métodos HTTP y códigos de estado](03-Metodos-HTTP-y-Codigos-de-Estado.md)
4. [Headers, cookies y sesiones](04-Headers-Cookies-y-Sesiones.md)
5. [Authentication vs Authorization](05-Authentication-vs-Authorization.md)
6. [URL encoding y double encoding](06-URL-Encoding.md)
7. [Workflow básico en Burp Suite](07-Workflow-Burp.md)
8. [Security by Obscurity y rutas expuestas en JavaScript](08-Security-by-Obscurity-y-Rutas-en-JavaScript.md)
9. [Burp Intruder — conceptos básicos](09-Burp-Intruder-Basico.md)
10. [SSRF: loopback, localhost y redes internas](10-SSRF-Loopback-y-Redes-Internas.md)
11. [Multipart/form-data y File Upload](11-Multipart-Form-Data-y-File-Upload.md)
12. [OS Command Injection y separadores del shell](12-OS-Command-Injection-y-Shell-Separators.md)
13. [SQL básico para entender SQL Injection](13-SQL-Basico-para-SQL-Injection.md)

## Qué deberías ser capaz de hacer

Después de leer esta sección deberías poder mirar una request HTTP y responder:

- ¿Qué endpoint se está llamando?
- ¿Qué parte controla el usuario?
- ¿Qué método HTTP se utiliza?
- ¿Qué cookies mantienen la sesión?
- ¿Qué headers pueden afectar el comportamiento?
- ¿Qué significa el status code recibido?
- ¿La aplicación está autenticando al usuario o autorizando una acción?
- ¿Hay rutas sensibles expuestas en `robots.txt`, JavaScript, sitemap o respuestas HTTP?
- ¿Una funcionalidad está realmente protegida o solamente oculta mediante *security by obscurity*?
- ¿El valor está URL encoded una o varias veces?
- ¿Estoy modificando una **request** o una **response**, y por qué?
- ¿Sé cuándo conviene automatizar una prueba con **Intruder** y qué diferencia de respuesta estoy buscando?
- ¿Entiendo la diferencia entre mi `localhost` y el `localhost` del servidor remoto?
- ¿Puedo reconocer una URL que apunta a una red interna y separar protocolo, host, puerto y path?
- ¿Puedo distinguir el `Content-Type` global de una request multipart del `Content-Type` de la parte que contiene el archivo?
- ¿Sé localizar `filename=` y el contenido de un archivo en una request de upload?
- ¿Puedo reconocer cuándo un parámetro HTTP podría terminar siendo usado dentro de un comando del sistema operativo?
- ¿Entiendo por qué caracteres como `|`, `&` o `;` tienen significado especial para un shell?
- ¿Entiendo `SELECT`, `WHERE`, `AND`, `OR`, strings entre comillas y comentarios `--` a nivel suficiente para los primeros labs SQLi?
- ¿Puedo explicar por qué `' OR 1=1--` cambia la lógica de una consulta vulnerable?

## Regla mental

```text
Primero entender qué está enseñando PortSwigger.
Después entender la request/response.
Luego identificar qué controla el usuario.
Aplicar el workflow del lab.
Finalmente explicar por qué funcionó.
```

Estos conceptos se reutilizan en Access Control, Authentication, Path Traversal, SSRF, SQLi, XSS, File Upload, OS Command Injection, Request Smuggling y otros temas.
