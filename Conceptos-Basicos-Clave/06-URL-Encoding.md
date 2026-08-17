# 06 — URL encoding

## Qué es

Las URLs no siempre pueden transportar todos los caracteres literalmente. Algunos caracteres se representan mediante **percent-encoding**.

Ejemplos:

```text
/    → %2F
.    → %2E
space → %20
%    → %25
?    → %3F
#    → %23
```

Por ejemplo:

```text
../
```

puede representarse como:

```text
%2e%2e%2f
```

## Por qué importa en seguridad web

La aplicación puede aplicar filtros antes o después de decodificar el valor.

Ejemplo conceptual:

```text
Entrada recibida
   ↓
Filtro
   ↓
URL decode
   ↓
Uso interno
```

no es equivalente a:

```text
Entrada recibida
   ↓
URL decode
   ↓
Filtro
   ↓
Uso interno
```

El orden puede cambiar completamente el resultado.

## Double encoding

Un carácter `%` también puede codificarse como `%25`.

Por ejemplo:

```text
%2f
```

codificado nuevamente puede convertirse en:

```text
%252f
```

Una primera decodificación produce:

```text
%2f
```

y una segunda:

```text
/
```

Esto explica por qué algunos labs requieren double encoding: existen varias capas de procesamiento.

## Burp Suite

Burp incluye herramientas para codificar y decodificar valores. También puedes seleccionar texto dentro de Repeater y aplicar transformaciones de URL encoding.

La idea no es memorizar cadenas largas, sino saber responder:

```text
¿Qué valor quiero que vea finalmente la aplicación?
¿Cuántas veces parece decodificarse?
```

## Relación con Path Traversal

Una secuencia básica:

```text
../../../etc/passwd
```

puede ser bloqueada literalmente, mientras una representación codificada puede comportarse distinto dependiendo de cómo esté implementado el filtro.

## Regla mental

```text
Lo que envío ≠ necesariamente lo que procesa finalmente el servidor
```

## Checklist

- [ ] Reconozco `%2f`, `%2e` y `%25`.
- [ ] Entiendo qué significa percent-encoding.
- [ ] Entiendo por qué el orden filtro/decodificación importa.
- [ ] Puedo explicar double encoding sin memorizar el payload.
