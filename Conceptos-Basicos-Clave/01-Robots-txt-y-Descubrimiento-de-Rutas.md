# 01 — robots.txt y descubrimiento de rutas

## Qué es `robots.txt`

`robots.txt` es un archivo de texto que normalmente se publica en la raíz de un sitio:

```text
https://ejemplo.com/robots.txt
```

Su objetivo es indicar a crawlers o motores de búsqueda qué partes del sitio deberían o no recorrer.

Ejemplo:

```text
User-agent: *
Disallow: /administrator-panel
```

- `User-agent: *` → regla para todos los crawlers.
- `Disallow: /administrator-panel` → se pide a los crawlers que no recorran esa ruta.

## Lo más importante

`Disallow` **NO bloquea el acceso**.

No es:

- autenticación;
- autorización;
- firewall;
- contraseña;
- control de acceso.

Solamente revela que el propietario del sitio no quiere que un crawler visite esa ruta.

Por eso puede convertirse en una fuente de información interesante.

## Ejemplo mental del lab de Access Control

Si encontramos:

```text
Disallow: /administrator-panel
```

la pregunta correcta es:

```text
¿Puedo visitar /administrator-panel directamente?
```

Si un usuario no autorizado obtiene:

```text
HTTP/1.1 200 OK
```

y puede usar funciones administrativas, el problema real es **Broken Access Control**.

`robots.txt` solamente nos ayudó a descubrir la ruta.

## Otros lugares donde buscar rutas

Además de `robots.txt`, revisar:

- `sitemap.xml`;
- archivos JavaScript;
- comentarios HTML;
- links ocultos en el frontend;
- Proxy → HTTP history;
- respuestas de la aplicación;
- nombres de endpoints usados por AJAX/fetch;
- redirects;
- documentación o mensajes de error.

## Diferencia clave

```text
Ruta oculta ≠ ruta protegida
```

Ocultar un botón de administración en el frontend no protege el endpoint.

El servidor debe verificar los permisos cada vez que se solicita una operación sensible.

## Regla rápida para recordar

```text
robots.txt → información
NO → seguridad
```

## Checklist

- [ ] Sé dónde buscar `robots.txt`.
- [ ] Entiendo `User-agent` y `Disallow`.
- [ ] Sé que `Disallow` no bloquea una URL.
- [ ] Si encuentro una ruta sensible, pruebo su acceso directo en el laboratorio.
- [ ] Distingo descubrimiento de ruta de vulnerabilidad de autorización.
