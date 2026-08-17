# Security by Obscurity y rutas expuestas en JavaScript

## Idea principal

**Security by obscurity** significa intentar proteger una función sensible solamente porque su ubicación, nombre o funcionamiento es difícil de adivinar.

Ejemplo:

```text
/administrator-panel-yb556
```

Que una ruta sea impredecible no significa que esté protegida. Si el servidor permite acceder a ella sin comprobar permisos, sigue existiendo una vulnerabilidad de control de acceso.

## Por qué una ruta "secreta" puede filtrarse

Aunque el enlace no aparezca en la interfaz, la aplicación puede enviar información sobre esa ruta al navegador mediante:

- HTML;
- JavaScript;
- respuestas JSON/API;
- comentarios;
- archivos estáticos;
- `robots.txt`;
- `sitemap.xml`;
- redirects;
- código frontend condicionado por roles.

El navegador necesita descargar el JavaScript para ejecutarlo. Por eso, **todo valor incluido en código JavaScript enviado al cliente debe considerarse visible para el usuario**.

## Ejemplo típico

La aplicación puede hacer algo así:

```javascript
var isAdmin = false;

if (isAdmin) {
    var adminPanelTag = document.createElement('a');
    adminPanelTag.setAttribute('href', '/administrator-panel-yb556');
    adminPanelTag.innerText = 'Admin panel';
}
```

La condición evita que un usuario normal vea el botón, pero el navegador ya recibió el código fuente que contiene:

```text
/administrator-panel-yb556
```

Por tanto, un usuario puede inspeccionar el source o los scripts y descubrir la ruta.

## El error real

El fallo **no es solamente que JavaScript revele la URL**.

El problema crítico ocurre si después el servidor acepta:

```http
GET /administrator-panel-yb556
```

sin comprobar que la sesión pertenece a un administrador.

La defensa correcta debe ser algo conceptualmente equivalente a:

```text
Request a /administrator-panel-yb556
        ↓
¿La sesión está autenticada?
        ↓
¿El usuario tiene rol administrador?
        ↓
Sí → permitir
No → 403 / denegar
```

Ocultar el enlace es solamente una decisión de interfaz.

## Cómo buscar estas rutas

### En el navegador

- View Source / Ver código fuente.
- DevTools → Sources.
- DevTools → Network.
- Buscar palabras como:

```text
admin
administrator
panel
manage
role
isAdmin
href
endpoint
api
```

### En Burp Suite

Revisar:

- Proxy → HTTP history;
- respuestas HTML;
- archivos `.js`;
- Target → Site map;
- Search sobre respuestas del sitio.

Si se descubre una ruta sensible, probarla directamente en Repeater o en el navegador del laboratorio.

## Relación con Access Control

Este patrón suele producir **vertical privilege escalation**:

```text
usuario normal
    ↓
descubre endpoint administrativo en JS
    ↓
accede directamente
    ↓
el servidor no valida el rol
    ↓
funcionalidad de administrador
```

## Comparación con robots.txt

Los dos conceptos tienen la misma enseñanza:

```text
robots.txt → puede revelar una ruta
JavaScript → puede revelar una ruta
```

Pero ninguno de ellos debería ser la barrera de seguridad.

La barrera real debe estar en la autorización del servidor.

## Regla para recordar

> **Todo lo que llega al navegador puede ser inspeccionado. Nunca guardar una ruta sensible, secreto o decisión de autorización confiando en que el usuario no mirará el frontend.**

## Checklist rápido

Cuando una función parezca "oculta":

- [ ] Revisar `robots.txt`.
- [ ] Revisar el HTML/source.
- [ ] Revisar archivos JavaScript.
- [ ] Buscar endpoints en respuestas API.
- [ ] Revisar Site map e HTTP history en Burp.
- [ ] Probar acceso directo a la ruta encontrada.
- [ ] Confirmar si el servidor valida realmente el rol.

## Aprendizaje del laboratorio de Access Control

En el laboratorio de **Unprotected admin functionality with unpredictable URL**, la ruta administrativa no era fácil de adivinar, pero estaba embebida en JavaScript enviado al navegador. Al inspeccionar ese código era posible descubrir la URL exacta. El laboratorio demuestra que una URL impredecible no sustituye una comprobación de autorización en el servidor.
