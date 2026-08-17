# 03 — Métodos HTTP y códigos de estado

## Métodos HTTP comunes

### GET

Normalmente solicita información.

```http
GET /product?id=10 HTTP/1.1
```

### POST

Normalmente envía datos o ejecuta una acción.

```http
POST /login HTTP/1.1

username=test&password=test
```

### PUT / PATCH

Suelen utilizarse para modificar recursos.

### DELETE

Suele utilizarse para eliminar recursos.

### HEAD

Pide las cabeceras de una respuesta sin solicitar normalmente el body completo.

### OPTIONS

Puede informar qué métodos o capacidades admite un recurso.

## Importante para Access Control

No asumas que porque una acción está protegida mediante POST también lo estará si el servidor acepta otro método.

En los labs conviene observar:

```text
GET /admin/delete?username=carlos
POST /admin/delete
```

y comprobar cómo aplica el servidor la autorización.

## Status codes que debes reconocer

### 200 OK

La petición fue procesada correctamente.

En pruebas de Access Control, un `200` sobre una página administrativa siendo usuario normal puede ser una señal importante.

### 201 Created

Se creó un recurso.

### 301 / 302 Redirect

El servidor te redirige a otra ubicación.

Importante: **no mires solamente el status code**. Examina también `Location` y el body porque algunas aplicaciones devuelven información sensible antes o durante un redirect.

### 400 Bad Request

La request no tiene el formato que el servidor espera.

### 401 Unauthorized

A pesar del nombre, normalmente significa que falta autenticación válida.

### 403 Forbidden

El servidor entendió la petición pero rechaza el acceso.

### 404 Not Found

El recurso no fue encontrado, aunque algunas aplicaciones usan 404 para ocultar recursos existentes.

### 500 Internal Server Error

El servidor encontró un error interno. Durante pruebas puede ser una señal de que nuestra entrada alcanzó una operación sensible, pero por sí sola no confirma una vulnerabilidad.

## Regla mental

```text
Status code + headers + body + longitud + comportamiento
```

Nunca juzgar una prueba solamente por `200`, `403` o `500`.

## Checklist

- [ ] Reconozco GET, POST, PUT/PATCH y DELETE.
- [ ] Entiendo 200, 302, 400, 401, 403, 404 y 500.
- [ ] Reviso `Location` cuando existe un redirect.
- [ ] Comparo body y longitud además del status code.
