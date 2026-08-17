# 05 — Authentication vs Authorization

Esta diferencia es fundamental para BSCP.

## Authentication

**Authentication = demostrar quién eres.**

Ejemplos:

```text
username + password
MFA
session cookie después del login
```

Pregunta mental:

```text
¿Quién es este usuario?
```

Problemas típicos:

- brute force;
- credenciales débiles;
- recuperación de contraseña insegura;
- lógica de login defectuosa;
- MFA bypass;
- sesiones inseguras.

## Authorization

**Authorization = decidir qué puede hacer un usuario que ya fue identificado.**

Pregunta mental:

```text
¿Este usuario tiene permiso para hacer esta acción sobre este recurso?
```

Ejemplo:

```text
Wilson inicia sesión correctamente → Authentication OK
Wilson intenta entrar a /administrator-panel → el servidor debe comprobar Authorization
```

Si el servidor deja entrar a un usuario normal al panel administrativo, el fallo no está necesariamente en el login: está en el **control de acceso**.

## Vertical privilege escalation

Un usuario obtiene funciones de un rol superior.

```text
Usuario normal → función de administrador
```

Ejemplo:

```text
/administrator-panel
```

## Horizontal privilege escalation

Un usuario accede a información o funciones pertenecientes a otro usuario del mismo nivel.

```text
/my-account?id=123
```

Cambiar a:

```text
/my-account?id=124
```

y obtener la cuenta de otra persona sería un ejemplo típico.

## Context-dependent access control

Una acción solamente debería permitirse en una etapa o contexto concreto del proceso.

Ejemplo conceptual:

```text
Paso 1 → confirmar producto
Paso 2 → confirmar pago
Paso 3 → completar compra
```

Si puedes llamar directamente al paso 3 saltándote controles anteriores, puede existir un fallo lógico o de control de acceso.

## Error común

```text
El botón Admin no aparece → entonces está protegido
```

Incorrecto.

La interfaz puede ocultar el botón, pero el endpoint debe comprobar permisos en el servidor.

## Regla rápida

```text
Authentication → ¿quién eres?
Authorization  → ¿qué puedes hacer?
```

## Checklist

- [ ] Puedo explicar Authentication sin mencionar permisos.
- [ ] Puedo explicar Authorization sin confundirla con login.
- [ ] Distingo escalación vertical y horizontal.
- [ ] Entiendo que ocultar una función en HTML no la protege.
