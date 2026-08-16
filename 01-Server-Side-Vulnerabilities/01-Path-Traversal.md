# 01 — Path Traversal

## Qué es

**Path Traversal** (también llamado **Directory Traversal**) es una vulnerabilidad que permite manipular una ruta de archivo controlada por el usuario para acceder a archivos fuera del directorio que la aplicación debería permitir.

Ejemplo conceptual:

```text
/image?filename=producto.jpg
```

Si el servidor construye una ruta como:

```text
/var/www/images/ + filename
```

y no valida correctamente el valor, un atacante puede intentar salir del directorio `images` mediante secuencias como `../`.

## Qué debes entender antes de memorizar payloads

La pregunta importante no es "¿qué payload uso?", sino:

1. ¿Hay un parámetro que parezca representar un archivo o ruta?
2. ¿El servidor usa ese valor para leer un archivo?
3. ¿Puedo hacer que la ruta retroceda directorios?
4. ¿Existe algún filtro o normalización?
5. ¿En qué momento ocurre el URL decoding?

## Señales típicas

Buscar parámetros o funcionalidades relacionadas con:

- imágenes;
- documentos;
- descargas;
- plantillas;
- archivos adjuntos;
- nombres de fichero;
- rutas de recursos.

Ejemplos de parámetros sospechosos:

```text
filename=
file=
path=
image=
document=
template=
```

## Prueba inicial

En un entorno autorizado o laboratorio, una prueba clásica en Linux es intentar acceder a:

```text
../../../etc/passwd
```

La cantidad exacta de `../` depende de la ruta donde se encuentre la aplicación.

En Windows, un objetivo equivalente de prueba puede ser un archivo conocido del sistema, por ejemplo:

```text
..\..\..\Windows\win.ini
```

## Por qué `../` funciona

`..` significa **directorio padre**.

Si la aplicación espera:

```text
/var/www/images/producto.jpg
```

pero recibe:

```text
../../../etc/passwd
```

la resolución de la ruta puede terminar fuera de `/var/www/images/` y apuntar a `/etc/passwd`.

## Bypasses que debes comprender

### 1. Secuencias anidadas

Si la aplicación elimina `../` una sola vez, pueden existir variantes como:

```text
....//....//....//etc/passwd
```

La idea es que, después de una sanitización incompleta, vuelva a aparecer una secuencia válida de traversal.

### 2. URL encoding

Algunos filtros comparan el valor antes o después de decodificarlo.

Ejemplos conceptuales:

```text
../
..%2f
%2e%2e%2f
```

### 3. Double URL encoding

Si hay más de una etapa de decodificación, una secuencia puede llegar codificada dos veces.

Ejemplo:

```text
..%252f
```

`%25` representa el carácter `%`, por lo que una primera decodificación puede producir `%2f`, y una segunda `/`.

### 4. Ruta absoluta

A veces no hace falta usar `../` si la aplicación acepta rutas absolutas:

```text
/etc/passwd
```

### 5. Prefijo obligatorio

Si la aplicación exige que la ruta comience con un directorio concreto, puede ser necesario conservar ese prefijo y después escapar de él.

### 6. Extensión obligatoria y null byte

En aplicaciones o runtimes antiguos, una comprobación de extensión podía intentar forzar algo como `.jpg`. Históricamente, un null byte codificado (`%00`) podía truncar la cadena a nivel nativo. Esto depende mucho de la tecnología y no debe asumirse como universal.

## Qué mirar en Burp Suite

1. Navegar normalmente por la aplicación.
2. Revisar **Proxy → HTTP history**.
3. Buscar requests con parámetros de archivo/ruta.
4. Enviar la request a **Repeater**.
5. Cambiar un solo elemento a la vez.
6. Comparar:
   - status code;
   - longitud de respuesta;
   - contenido;
   - errores;
   - diferencias entre una ruta válida e inválida.

## Método mental para el examen/labs

```text
¿Veo un nombre de archivo o ruta?
        ↓
¿El servidor devuelve el contenido de ese recurso?
        ↓
Probar traversal básico
        ↓
¿Está bloqueado?
        ↓
Identificar el tipo de filtro
        ↓
Probar bypass acorde al filtro
```

## Errores comunes

- Probar 20 payloads sin entender qué filtro existe.
- No comprobar primero cómo se comporta una ruta válida.
- Cambiar varias cosas de la request a la vez.
- Confundir un `404` de la aplicación con una prueba definitiva de que no existe traversal.
- No considerar encoding/decoding.
- Memorizar un número fijo de `../`.

## Lo que debes poder explicar con tus palabras

Antes de marcar este tema como dominado, deberías poder responder:

- ¿Qué significa `../`?
- ¿Por qué puede escapar del directorio previsto?
- ¿Qué diferencia hay entre sanitización y canonicalización de rutas?
- ¿Por qué el orden del URL decoding importa?
- ¿Por qué `....//` puede superar una sanitización incompleta?

## Checklist personal

- [ ] Entiendo qué problema resuelve `../`.
- [ ] Sé localizar un parámetro que representa un archivo.
- [ ] Sé interceptarlo y modificarlo con Repeater.
- [ ] Entiendo URL encoding y double encoding en este contexto.
- [ ] Puedo explicar un bypass en vez de solo copiarlo.
- [ ] He completado los labs Apprentice correspondientes.
- [ ] He documentado mis errores y aprendizajes.

## Mis notas después de los labs

> Esta sección se completa después de resolver cada laboratorio.

### Señales que me ayudaron

- Pendiente.

### Errores que cometí

- Pendiente.

### Payloads que realmente entendí

- Pendiente.

### Regla rápida que quiero recordar

- Pendiente.
