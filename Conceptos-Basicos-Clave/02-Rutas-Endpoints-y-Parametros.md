# 02 — Rutas, endpoints y parámetros

## Ruta vs endpoint

Una **ruta** es una ubicación dentro de la aplicación:

```text
/account
/admin
/product
/image
```

Un **endpoint** es una ruta que recibe una petición concreta y ejecuta alguna función del servidor.

Ejemplo:

```http
GET /accountDetails HTTP/1.1
```

Aquí `/accountDetails` es el endpoint solicitado.

## Parámetros

Los parámetros son valores que la aplicación recibe y utiliza.

### Query string

```text
/product?id=10
/image?filename=23.jpg
```

Parámetros:

```text
id=10
filename=23.jpg
```

### Body

Una petición POST puede enviar:

```text
username=wilson&email=a@a.com
```

### Ruta

También puede haber valores dentro de la propia ruta:

```text
/users/123/profile
```

El `123` puede identificar un objeto o usuario.

## Por qué importa para seguridad

Antes de probar una vulnerabilidad pregunta:

```text
¿Qué dato de esta request puedo controlar?
```

Ejemplos:

- `filename` → interesante para Path Traversal.
- `id` → interesante para Access Control / IDOR.
- `url` o `stockApi` → interesante para SSRF.
- `username` / `password` → Authentication.
- parámetros usados en consultas → potencial SQLi.

## IDOR como ejemplo

Si tu cuenta usa:

```text
/my-account?id=123
```

y al cambiarlo por:

```text
/my-account?id=124
```

puedes ver datos de otra persona sin autorización, existe un problema de control de acceso horizontal.

## Regla mental

```text
Endpoint = qué función llamo
Parámetro = qué valor le entrego
Respuesta = qué hizo el servidor
```

## Checklist

- [ ] Sé identificar la ruta de una request.
- [ ] Sé distinguir query parameters y body parameters.
- [ ] Identifico valores numéricos, filenames, URLs y usernames controlables.
- [ ] Cambio una sola variable por vez en Repeater.
