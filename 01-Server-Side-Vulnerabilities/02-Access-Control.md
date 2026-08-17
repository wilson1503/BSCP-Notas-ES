# 02 — Access Control

## Qué es

**Access Control** (control de acceso) define qué acciones puede realizar un usuario y a qué recursos puede acceder después de que la aplicación ya sabe quién es.

Una vulnerabilidad de control de acceso aparece cuando el servidor confía en datos controlados por el usuario o aplica las reglas de autorización de forma incompleta.

La pregunta principal es:

> **¿El servidor vuelve a comprobar que este usuario tiene permiso para realizar esta acción o acceder a este recurso?**

No hay que confundir:

- **Authentication** → ¿quién eres?
- **Access Control / Authorization** → ¿qué tienes permitido hacer?

---

## Tipos principales

### 1. Vertical privilege escalation

Un usuario con pocos privilegios consigue ejecutar funciones reservadas para un rol superior.

Ejemplo:

```text
Usuario normal → función de administrador
```

Objetivos típicos:

```text
/admin
/admin-panel
/administrator
/manage
```

Si una aplicación solamente oculta el enlace del panel de administración, pero el servidor permite acceder directamente a la URL, existe un problema de autorización.

---

### 2. Horizontal privilege escalation

Un usuario puede acceder a recursos pertenecientes a **otro usuario del mismo nivel**.

Ejemplo:

```text
/my-account?id=123
```

Cambiar:

```text
id=123
```

por:

```text
id=124
```

puede revelar la información de otra cuenta si el servidor no comprueba la propiedad del recurso.

Este patrón suele aparecer como **IDOR** (*Insecure Direct Object Reference*).

---

### 3. Horizontal → vertical

A veces una vulnerabilidad horizontal permite obtener datos de una cuenta privilegiada y después escalar verticalmente.

Ejemplo conceptual:

```text
1. Accedo al perfil de otro usuario.
2. Ese usuario resulta ser administrador.
3. Obtengo un dato sensible o una función que permite controlar su cuenta.
4. Termino obteniendo privilegios administrativos.
```

---

### 4. Context-dependent access control

La autorización depende del estado o del flujo de la aplicación.

Ejemplo:

```text
Paso 1 → elegir acción
Paso 2 → confirmar
Paso 3 → ejecutar
```

Puede ocurrir que el servidor valide los permisos en el paso 1, pero no en el paso 2 o 3.

---

## Superficie de ataque que debo revisar

Buscar especialmente:

- parámetros `id`, `user`, `uid`, `account`, `role`;
- URLs administrativas;
- IDs numéricos o UUID;
- endpoints API;
- cambios de email, contraseña o rol;
- eliminación de usuarios;
- datos de pedidos o facturas;
- descargas y documentos privados;
- funciones visibles solo para ciertos usuarios;
- métodos HTTP distintos (`GET`, `POST`, `PUT`, `DELETE`);
- cookies o parámetros que indiquen roles.

Ejemplos:

```text
/user?id=10
/account?user=carlos
/download?file=5832
/admin/delete?username=carlos
```

---

## 1. Funciones administrativas sin protección

Una aplicación puede esconder el enlace a `/admin`, pero eso no significa que el endpoint esté protegido.

### Qué probar

1. Revisar la navegación normal.
2. Buscar rutas interesantes en Burp.
3. Revisar archivos como:

```text
/robots.txt
```

4. Probar acceso directo a rutas administrativas encontradas.

### Idea clave

**Ocultar una función en la interfaz no es control de acceso.**

La autorización debe realizarse en el servidor para cada petición sensible.

---

## 2. URL administrativa no predecible

Algunas aplicaciones intentan proteger una función usando una ruta difícil de adivinar:

```text
/admin-4f83a2
```

Esto tampoco reemplaza una comprobación de autorización.

### Dónde buscar la ruta

- HTML;
- JavaScript;
- respuestas API;
- comentarios;
- archivos cargados por la página.

Ejemplo conceptual en JavaScript:

```javascript
var isAdmin = false;
if (isAdmin) {
    var adminPanel = "/admin-4f83a2";
}
```

Aunque el botón no aparezca, la ruta puede quedar expuesta en el código enviado al navegador.

---

## 3. Control de acceso basado en parámetros

Una aplicación puede confiar en un parámetro o cookie como:

```text
admin=false
```

```text
role=user
```

```text
isAdmin=0
```

Si cambiarlo a:

```text
admin=true
```

modifica los privilegios sin una validación real en el servidor, existe escalación vertical.

### En Burp

Revisar:

- query string;
- body;
- cookies;
- JSON;
- headers.

---

## 4. IDOR / acceso a objetos de otros usuarios

Uno de los patrones más importantes para BSCP.

Petición legítima:

```http
GET /my-account?id=wiener
```

Prueba:

```http
GET /my-account?id=carlos
```

Si el servidor devuelve el recurso sin comprobar que pertenece al usuario autenticado, existe una vulnerabilidad horizontal.

### Los identificadores no siempre son números

Pueden ser:

```text
1
1004
carlos
user@example.com
550e8400-e29b-41d4-a716-446655440000
```

Un UUID difícil de adivinar puede reducir la facilidad de explotación, pero **no sustituye la autorización**.

---

## 5. IDs encontrados en otras partes de la aplicación

Si el identificador es impredecible, buscar dónde lo expone la aplicación.

Ejemplos:

- comentarios;
- perfiles públicos;
- mensajes;
- API;
- historial de pedidos;
- JavaScript.

Flujo mental:

```text
No puedo adivinar el ID
        ↓
¿La aplicación lo muestra en otra funcionalidad?
        ↓
Obtengo el ID de otro usuario
        ↓
Lo pruebo en el endpoint sensible
```

---

## 6. Redirect no significa protección

Un endpoint puede responder con un redirect hacia `/login` o `/my-account`, pero aun así incluir información sensible en el cuerpo de la respuesta.

Por eso en Burp hay que revisar siempre:

- status code;
- headers;
- body;
- longitud de respuesta.

No asumir que un `302` significa automáticamente que el recurso está protegido.

---

## 7. Controles basados en el método HTTP

Puede ocurrir que la aplicación proteja:

```http
POST /admin/deleteUser
```

pero no aplique la misma autorización al usar otro método.

Ejemplos a comprobar cuando tenga sentido:

```text
GET
POST
PUT
PATCH
DELETE
```

En Burp Repeater se puede cambiar el método y observar si cambia la autorización.

### Regla

La autorización debe ser consistente independientemente del método HTTP que termine ejecutando la misma operación.

---

## 8. Controles basados en headers

Algunas aplicaciones o proxies aplican reglas según headers como:

```text
X-Original-URL
X-Rewrite-URL
Referer
```

Puede haber diferencias entre lo que valida el frontend/proxy y lo que procesa el backend.

Ejemplo conceptual:

```http
GET /
X-Original-URL: /admin
```

Esto solo tiene sentido cuando la arquitectura o las respuestas sugieren que existe un control de acceso basado en routing o headers.

---

## 9. Control basado en Referer

Una aplicación puede asumir que una petición es legítima simplemente porque viene desde una página concreta.

Ejemplo:

```text
Referer: https://target.example/admin
```

El `Referer` es controlado por el cliente y no debe ser la única base de una decisión de autorización.

En Repeater se puede comparar el comportamiento:

```text
con Referer
sin Referer
Referer modificado
```

---

## 10. Procesos de varios pasos

Revisar operaciones como:

```text
1. Seleccionar usuario
2. Elegir nuevo rol
3. Confirmar cambio
```

Probar si es posible enviar directamente el último paso sin pasar por la validación anterior.

La aplicación debe comprobar permisos **en cada request que realiza una acción sensible**.

---

# Workflow en Burp Suite

## Paso 1 — Navegar normalmente

Usar la aplicación como un usuario legítimo y generar tráfico.

## Paso 2 — Proxy → HTTP history

Buscar endpoints asociados a:

```text
account
user
admin
role
id
profile
order
file
delete
update
```

## Paso 3 — Send to Repeater

Crear una baseline con la petición válida.

## Paso 4 — Cambiar una sola cosa

Ejemplos:

```text
id=mi_usuario → id=otro_usuario
role=user → role=admin
/admin oculto → acceso directo
POST → GET
```

## Paso 5 — Comparar respuestas

Mirar:

- código HTTP;
- longitud;
- contenido;
- redirects;
- errores;
- funciones nuevas;
- datos pertenecientes a otro usuario.

---

# Método mental para Access Control

```text
¿Qué recurso/acción estoy usando?
        ↓
¿Quién debería poder acceder?
        ↓
¿Qué dato identifica al usuario, objeto o rol?
        ↓
¿Puedo modificarlo desde el cliente?
        ↓
¿El servidor vuelve a comprobar autorización?
        ↓
NO → posible Access Control vulnerability
```

---

## Pruebas rápidas que quiero recordar

Cuando vea un endpoint interesante:

```text
1. ¿Puedo acceder sin login?
2. ¿Puedo acceder con un usuario de menor privilegio?
3. ¿Puedo cambiar el ID por el de otro usuario?
4. ¿Puedo cambiar un role/admin flag?
5. ¿Puedo cambiar el método HTTP?
6. ¿Hay otro paso del flujo que no valide permisos?
7. ¿Un redirect sigue filtrando información?
8. ¿La URL administrativa aparece en JS/robots.txt?
```

---

## Errores comunes

- Confundir autenticación con autorización.
- Pensar que ocultar un botón protege una función.
- Probar solo URLs y olvidarse de IDs en parámetros/JSON.
- No comparar la misma request entre dos usuarios.
- No revisar cookies y headers.
- Considerar un UUID como prueba de que no existe IDOR.
- Ver un `302` y no revisar el cuerpo de la respuesta.
- Cambiar muchas cosas a la vez en Repeater.
- No probar los distintos pasos de una operación sensible.

---

## Qué debo poder explicar con mis palabras

Antes de considerar dominado este tema:

- ¿Cuál es la diferencia entre Authentication y Authorization?
- ¿Qué diferencia hay entre escalación vertical y horizontal?
- ¿Qué es un IDOR?
- ¿Por qué un ID impredecible no reemplaza el control de acceso?
- ¿Por qué ocultar `/admin` no es una defensa suficiente?
- ¿Por qué cada request sensible debe validar permisos?
- ¿Cómo compararía dos usuarios distintos usando Burp Repeater?

---

## Checklist personal

- [ ] Entiendo vertical privilege escalation.
- [ ] Entiendo horizontal privilege escalation.
- [ ] Entiendo IDOR.
- [ ] Sé buscar funciones administrativas ocultas.
- [ ] Sé revisar parámetros/cookies relacionados con roles.
- [ ] Sé comparar objetos de dos usuarios en Repeater.
- [ ] Sé revisar controles dependientes del método HTTP.
- [ ] Sé revisar flujos de varios pasos.
- [ ] He completado los labs Apprentice correspondientes.
- [ ] He documentado mis errores y aprendizajes.

---

## Mis notas después de los labs

> Completar esta sección después de resolver cada laboratorio.

### Señales que me ayudaron

- Pendiente.

### Errores que cometí

- Pendiente.

### Pruebas que realmente entendí

- Pendiente.

### Regla rápida que quiero recordar

- **No confiar en lo que muestra la interfaz: comprobar siempre qué autoriza realmente el servidor.**
