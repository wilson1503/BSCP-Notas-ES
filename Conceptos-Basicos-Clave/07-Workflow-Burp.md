# 07 — Workflow básico en Burp Suite

## Objetivo

Usar Burp para observar una petición válida, aislarla y modificar una sola variable cada vez.

## Flujo básico

### 1. Navegar normalmente

Usa la aplicación como lo haría un usuario normal.

### 2. Proxy → HTTP history

Busca la request interesante.

Ejemplos:

```text
/image?filename=23.jpg
/my-account?id=123
/administrator-panel
/login
```

### 3. Send to Repeater

Clic derecho sobre la request:

```text
Send to Repeater
```

Repeater permite modificar y reenviar la request todas las veces necesarias.

### 4. Crear una baseline

Antes de cambiar nada, envía la request original y observa:

- status code;
- longitud;
- headers;
- body;
- redirect;
- tiempo de respuesta.

Esta es tu respuesta de comparación.

### 5. Cambiar una sola cosa

Ejemplo Path Traversal:

```text
filename=23.jpg
```

cambiar únicamente a:

```text
filename=../../../etc/passwd
```

Ejemplo Access Control:

```text
id=123
```

a:

```text
id=124
```

Si modificas muchas cosas simultáneamente será más difícil saber qué causó el cambio.

### 6. Comparar respuestas

Preguntas:

```text
¿Cambió el status?
¿Cambió el tamaño?
¿Apareció información nueva?
¿Me redirigió?
¿La acción se ejecutó?
¿El comportamiento cambia sin cookie?
```

## Proxy vs Repeater

```text
Proxy    → observar tráfico real del navegador
Repeater → experimentar manualmente con una request
```

## Intercept

`Intercept` permite detener una request antes de enviarla al servidor.

No necesitas mantenerlo activado permanentemente para estudiar. Muchas veces es más cómodo navegar con Intercept off, encontrar la request en HTTP history y enviarla a Repeater.

## Decoder

Útil para entender/cambiar representaciones como:

- URL encoding;
- Base64;
- hexadecimal.

## Intruder

Útil cuando necesitas repetir una request con muchos valores. Antes de usarlo, entiende manualmente la request en Repeater.

## Regla para los labs

```text
Navegar
   ↓
HTTP history
   ↓
Identificar request
   ↓
Repeater
   ↓
Baseline
   ↓
Una modificación
   ↓
Comparar respuesta
```

## Checklist

- [ ] Sé encontrar una request en HTTP history.
- [ ] Sé enviarla a Repeater.
- [ ] Creo una baseline antes de modificar.
- [ ] Cambio una sola cosa por intento.
- [ ] Comparo status, length, headers y body.
- [ ] Entiendo para qué sirven Proxy, Repeater, Decoder e Intruder.
