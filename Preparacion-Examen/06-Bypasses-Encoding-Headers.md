# Bypasses, encoding y headers

Colección de recordatorios transversales tomada de patrones repetidos en las guías comunitarias revisadas.

> Un bypass solo tiene sentido después de identificar qué control está interfiriendo.

## URL encoding

Caracteres frecuentes:

```text
/  → %2f
.  → %2e
'  → %27
"  → %22
<  → %3c
>  → %3e
\  → %5c
%  → %25
```

## Double URL encoding

Ejemplo clásico:

```text
/    → %2f    → %252f
../  → ..%2f  → ..%252f
```

Pensar en doble encoding si existen varias capas de decoding (proxy/backend/framework).

## Representaciones útiles de caracteres

### `'`

```text
%27
&#39;
&#x27;
&apos;
\x27
\u0027
```

### `"`

```text
%22
&#34;
&#x22;
&quot;
\x22
\u0022
```

### `<`

```text
%3C
&#60;
&#x3C;
&lt;
\x3C
\u003C
```

### `>`

```text
%3E
&#62;
&#x3E;
&gt;
\x3E
\u003E
```

### `\`

```text
%5C
&#92;
&#x5C;
\\
\x5C
\u005C
```

## Sanitización incompleta

Patrón de pensamiento:

```text
input
 ↓
filtro elimina/reemplaza una coincidencia
 ↓
resultado vuelve a formar un payload válido
```

Ejemplo de traversal:

```text
....//
```

puede transformarse en:

```text
../
```

si el filtro elimina una secuencia de forma incompleta.

## Case variation

Cuando el parser es case-insensitive pero el filtro no:

```text
script
ScRiPt
SCRIPT
```

No asumir que funcionará: depende del lenguaje/contexto.

## Null byte

Representación:

```text
%00
```

Históricamente útil cuando una capa valida una extensión pero una API nativa interpreta el null como terminador. Es dependiente de tecnología y debe tratarse como técnica contextual, no universal.

## Métodos HTTP

Si la autorización/validación parece ligada a un método:

```text
GET
POST
PUT
PATCH
DELETE
```

Probar alternativas únicamente si la aplicación soporta semánticamente esa operación.

También observar parámetros tipo:

```text
_method=POST
_method=DELETE
```

## Headers importantes

### Routing / proxies

```http
Host: target
X-Forwarded-Host: target
X-Original-URL: /admin
X-Rewrite-URL: /admin
```

### IP del cliente

```http
X-Forwarded-For: 127.0.0.1
X-Real-IP: 127.0.0.1
X-Client-IP: 127.0.0.1
True-Client-IP: 127.0.0.1
```

### CORS

```http
Origin: https://example.com
Access-Control-Allow-Origin: https://example.com
Access-Control-Allow-Credentials: true
```

### Cache

```http
Cache-Control:
Vary:
Age:
X-Cache:
```

### Redirects

```http
Location: /somewhere
```

Siempre revisar también el **body** de un 30x.

## Referer

Pruebas contextuales:

```text
con Referer
sin Referer
Referer del dominio esperado
Referer que solo contiene el dominio esperado como substring
```

No confundir una validación de Referer con autorización robusta.

## Host header

Buscar especialmente en:

- password reset;
- URLs absolutas generadas por backend;
- caches;
- routing;
- links enviados por email.

Headers relacionados:

```http
Host:
X-Forwarded-Host:
Forwarded:
```

## SSRF: representaciones de loopback

```text
127.0.0.1
127.1
localhost
```

También existen formatos decimal/hex/IPv6 y normalizaciones de URL. Para preparación avanzada, usar primero la cheat sheet oficial de PortSwigger de URL validation bypass antes de colecciones aleatorias.

## SQL syntax bypass

Alternativas conceptuales cuando un operador/palabra está filtrado:

```text
=       → LIKE / IN / BETWEEN según contexto
AND     → variaciones de case u operadores equivalentes según DB
OR      → operador equivalente según DB
```

Siempre identificar DBMS antes de depender de sintaxis específica.

## Duplicación de headers

Algunos frontends/backends resuelven headers duplicados de forma distinta.

```http
Header: value1
Header: value2
```

Es relevante para familias como request smuggling, host header o inconsistencias proxy/backend, no como prueba aleatoria universal.

## Checklist antes de intentar un bypass

```text
1. ¿Qué input fue rechazado?
2. ¿El rechazo ocurre antes o después de decoding?
3. ¿Qué componente parece rechazarlo?
4. ¿La respuesta cambia si codifico solo un carácter?
5. ¿Hay más de una capa de parsing?
6. ¿Puedo mantener la semántica con otra representación?
```
