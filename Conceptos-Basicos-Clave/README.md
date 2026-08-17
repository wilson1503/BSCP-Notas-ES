# Conceptos Básicos Clave

Esta carpeta reúne conceptos que aparecen una y otra vez en Web Security Academy y en el BSCP. La idea es entenderlos antes de memorizar payloads.

## Orden recomendado

1. [robots.txt y descubrimiento de rutas](01-Robots-txt-y-Descubrimiento-de-Rutas.md)
2. [Rutas, endpoints y parámetros](02-Rutas-Endpoints-y-Parametros.md)
3. [Métodos HTTP y códigos de estado](03-Metodos-HTTP-y-Codigos-de-Estado.md)
4. [Headers, cookies y sesiones](04-Headers-Cookies-y-Sesiones.md)
5. [Authentication vs Authorization](05-Authentication-vs-Authorization.md)
6. [URL encoding y double encoding](06-URL-Encoding.md)
7. [Workflow básico en Burp Suite](07-Workflow-Burp.md)

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
- ¿El valor está URL encoded una o varias veces?

## Regla mental

```text
Primero entender la request.
Después identificar qué controla el usuario.
Luego cambiar una sola cosa.
Finalmente comparar la respuesta.
```

Estos conceptos se usan en casi todos los temas: Access Control, Authentication, Path Traversal, SSRF, SQLi, XSS, File Upload, Request Smuggling y otros.
