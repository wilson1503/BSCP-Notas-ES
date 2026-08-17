# SSRF: loopback, localhost y redes internas

> Referencia básica para entender los dos labs Apprentice de SSRF. El procedimiento específico de cada lab sigue teniendo prioridad.

## Idea principal

Con SSRF, el atacante no accede directamente al recurso interno. Consigue que **el servidor vulnerable haga la petición**.

```text
Cliente externo
    ↓
Aplicación vulnerable
    ↓
Destino interno
```

Esto importa porque el servidor puede tener acceso de red y relaciones de confianza que el cliente externo no tiene.

---

## `localhost`

`localhost` es un nombre que normalmente apunta al propio equipo.

Ejemplo:

```text
http://localhost/admin
```

Si esa URL es solicitada desde el servidor vulnerable, el destino es el propio servidor.

---

## `127.0.0.1`

`127.0.0.1` es la dirección IPv4 de loopback más conocida.

Conceptualmente:

```text
localhost ≈ 127.0.0.1
```

Ambos permiten referirse al host local en los escenarios básicos de PortSwigger.

---

## Loopback

La interfaz de **loopback** permite que un host se comunique consigo mismo.

```text
Servidor
  ↘
  127.0.0.1
  ↗
Servidor
```

Un panel puede estar configurado para aceptar solo conexiones locales. Desde Internet parece inaccesible, pero una SSRF puede hacer que la petición nazca desde el propio servidor.

---

## Redes privadas internas

Direcciones como:

```text
192.168.x.x
10.x.x.x
172.16.x.x - 172.31.x.x
```

se utilizan comúnmente en redes privadas.

Un navegador externo normalmente no puede alcanzar directamente un host privado que está detrás de la aplicación objetivo.

Pero el servidor vulnerable puede tener conectividad hacia esa red.

Ejemplo de los labs:

```text
192.168.0.X:8080/admin
```

---

## Puerto

Una dirección de red puede incluir un puerto:

```text
http://192.168.0.42:8080/admin
```

Descomposición:

```text
http://           protocolo
192.168.0.42      host/IP
8080              puerto
/admin            path
```

En SSRF debemos prestar atención a las cuatro partes porque modificar cualquiera puede cambiar el destino final de la petición server-side.

---

## Por qué SSRF puede saltar controles

Conceptualmente, una aplicación puede implementar algo parecido a:

```text
si request viene de Internet:
    bloquear /admin

si request viene de localhost/red interna:
    permitir /admin
```

Si conseguimos que la propia aplicación haga la petición:

```text
cliente → SSRF → localhost/admin
```

el recurso puede interpretar la conexión como interna y confiar en ella.

---

## Diferencia importante

### Esto NO es SSRF

Escribir en el navegador:

```text
http://localhost/admin
```

haría referencia a **tu propia máquina**, no al servidor remoto.

### Esto SÍ representa el patrón SSRF del lab

Enviar a la aplicación:

```text
stockApi=http://localhost/admin
```

porque el servidor remoto usa ese valor para hacer la petición desde su propia máquina.

---

## Señal típica en HTTP

Una request vulnerable puede contener:

```http
POST /product/stock HTTP/2
Content-Type: application/x-www-form-urlencoded

stockApi=http://stock.example/product/1
```

La pregunta mental debe ser:

```text
¿quién solicita realmente la URL de stockApi?
```

Si la respuesta es **el servidor**, tenemos una superficie potencial de SSRF.

---

## Qué tengo que poder explicar

- ¿Qué significa que una petición sea server-side?
- ¿Por qué `localhost` desde el servidor no es el mismo `localhost` de mi PC?
- ¿Qué representa `127.0.0.1`?
- ¿Qué es una red privada?
- ¿Qué significa `:8080` en una URL?
- ¿Por qué un servidor puede alcanzar sistemas internos que mi navegador no puede?
- ¿Por qué eso puede permitir acceder a `/admin`?

---

## Regla rápida

```text
SSRF = controlo el destino de una petición que realiza el servidor.
```
