# 06 — OS Command Injection

> **Método de estudio:** seguir primero el orden, conceptos y workflow oficial de PortSwigger Web Security Academy. Los payloads avanzados, blind command injection y atajos de examen quedan separados en `Preparacion-Examen/`.

## Estado del bloque

**🟡 OS Command Injection — Apprentice en progreso.**

PortSwigger incluye actualmente **5 elementos** en este bloque y **1 laboratorio Apprentice**.

---

# Qué es OS command injection

**OS command injection** (también llamada **shell injection**) ocurre cuando una aplicación incorpora datos controlados por el usuario dentro de un comando del sistema operativo de forma insegura.

Ejemplo conceptual:

```text
Aplicación recibe:
productId=1
storeId=2

Servidor construye algo parecido a:
stockreport.pl 1 2
```

Si `storeId` se concatena sin validación y podemos introducir un separador de comandos:

```text
storeId=1|whoami
```

el shell puede interpretar:

```text
stockreport.pl 1 1 | whoami
```

y ejecutar nuestro comando adicional.

La idea central es:

```text
entrada controlada por usuario
        ↓
se inserta en un comando del SO
        ↓
shell interpreta metacaracteres
        ↓
ejecuta un comando adicional
```

---

# Orden oficial en Server-side vulnerabilities — Apprentice

1. What is OS command injection?
2. Useful commands
3. Injecting OS commands
4. Injecting OS commands — Continued
5. **Lab: OS command injection, simple case**

---

# Comandos útiles que enseña PortSwigger

| Objetivo | Linux | Windows |
|---|---|---|
| Usuario actual | `whoami` | `whoami` |
| Sistema operativo | `uname -a` | `ver` |
| Configuración de red | `ifconfig` | `ipconfig /all` |
| Conexiones de red | `netstat -an` | `netstat -an` |
| Procesos | `ps -ef` | `tasklist` |

Para el laboratorio Apprentice solo necesitamos:

```text
whoami
```

---

# Separadores de comandos

PortSwigger explica que diferentes shells reconocen metacaracteres que permiten encadenar comandos.

Funcionan comúnmente en Windows y Unix:

```text
&
&&
|
||
```

En Unix también aparecen:

```text
;
newline
`comando`
$(comando)
```

No necesitamos memorizar todos para este lab. El workflow oficial usa:

```text
|
```

---

# Lab — OS command injection, simple case ⏳

> [Procedimiento completo paso a paso](../Labs/OS-Command-Injection/01-OS-command-injection-simple-case.md)

## Objetivo

Ejecutar:

```text
whoami
```

mediante la función **Check stock** y observar el nombre del usuario del sistema en la respuesta.

## Request relevante

La función de stock genera una request parecida a:

```http
POST /product/stock HTTP/2
Content-Type: application/x-www-form-urlencoded

productId=1&storeId=1
```

La entrada vulnerable es:

```text
storeId
```

## Modificación oficial

Cambiar:

```text
storeId=1
```

por:

```text
storeId=1|whoami
```

La respuesta contendrá el nombre del usuario que ejecuta el proceso del servidor.

---

# Qué debemos entender

El servidor no está ejecutando `whoami` porque exista un endpoint llamado `whoami`.

Está ocurriendo esto:

```text
POST /product/stock
        ↓
storeId=1|whoami
        ↓
la aplicación construye un comando del SO
        ↓
el shell interpreta |
        ↓
ejecuta whoami
        ↓
la salida aparece en la Response
```

Esto es **in-band command injection** porque la salida del comando se devuelve directamente en la respuesta HTTP.

---

# Burp que debemos practicar

Para seguir el workflow oficial:

```text
Browser → producto → Check stock
        ↓
Proxy → Intercept ON
        ↓
interceptar POST /product/stock
        ↓
modificar storeId
        ↓
Forward
        ↓
observar la Response
```

**Repeater no es obligatorio para resolver este laboratorio.** Si se usa después para repetir pruebas, se considera práctica complementaria y no sustituye el workflow oficial.

---

# Qué NO mezclaremos todavía

PortSwigger también cubre posteriormente:

- blind OS command injection;
- time delays;
- output redirection;
- Burp Collaborator / OAST;
- exfiltración mediante DNS.

Esos temas no forman parte de este primer lab Apprentice y se estudiarán cuando corresponda.

---

# Checklist Apprentice

- [ ] Puedo explicar qué es OS command injection.
- [ ] Entiendo que la aplicación ejecuta un comando del sistema operativo.
- [ ] Sé qué función tiene un separador como `|`.
- [ ] Sé localizar `POST /product/stock`.
- [ ] Sé identificar `productId` y `storeId`.
- [ ] Sé modificar una request interceptada en Proxy.
- [ ] Puedo reconocer la salida de `whoami` en la Response.
- [ ] Lab completado.

---

# Registro de labs

## Lab 1 — OS command injection, simple case

- Estado: ⏳ siguiente lab.
- Función vulnerable: **Check stock**.
- Parámetro: `storeId`.
- Valor oficial: `1|whoami`.
- Objetivo: obtener el usuario actual del sistema.

---

# Regla rápida

**No estamos modificando una consulta SQL ni una URL interna: estamos consiguiendo que datos HTTP terminen siendo interpretados por el shell del sistema operativo.**
