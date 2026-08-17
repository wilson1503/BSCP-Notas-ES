# 01 — Path Traversal

> **Método de estudio:** primero seguimos la explicación y los workflows oficiales de PortSwigger. Las variantes, automatizaciones o atajos se reservan para la futura sección de preparación para examen.

## Qué es

**Path Traversal** (también llamado **Directory Traversal**) permite manipular una ruta de archivo controlada por el usuario para acceder a archivos fuera del directorio previsto por la aplicación.

PortSwigger usa como ejemplo una aplicación que carga imágenes mediante algo parecido a:

```text
/loadImage?filename=218.png
```

Si las imágenes están almacenadas en:

```text
/var/www/images/
```

el servidor puede terminar leyendo:

```text
/var/www/images/218.png
```

Si no existen defensas adecuadas, un valor como:

```text
../../../etc/passwd
```

puede hacer que la ruta resuelta termine siendo:

```text
/etc/passwd
```

## Por qué `../` funciona

`..` representa el **directorio padre**.

Desde:

```text
/var/www/images/
```

cada `../` retrocede un nivel:

```text
/var/www/images/../     → /var/www/
/var/www/../            → /var/
/var/../                → /
```

Por eso:

```text
/var/www/images/../../../etc/passwd
```

termina apuntando a:

```text
/etc/passwd
```

En Windows también pueden aparecer secuencias como:

```text
..\..\..\windows\win.ini
```

---

# Orden oficial de estudio

En el learning path de PortSwigger:

1. What is path traversal?
2. Reading arbitrary files via path traversal
3. **Lab: File path traversal, simple case**
4. Common obstacles to exploiting path traversal vulnerabilities
5. Labs Practitioner de bypasses
6. How to prevent a path traversal attack

---

# Lab Apprentice — File path traversal, simple case

## Objetivo

Recuperar el contenido de:

```text
/etc/passwd
```

## Workflow que debemos aprender

El patrón oficial de PortSwigger para estos labs es trabajar sobre la request que carga una imagen:

1. Usar Burp Suite para **interceptar una request que obtiene una imagen de producto**.
2. Localizar el parámetro:

```text
filename=
```

3. Sustituir el nombre de imagen por:

```text
../../../etc/passwd
```

4. Enviar/forwardear la request modificada.
5. Observar que la response contiene `/etc/passwd`.

Ejemplo conceptual:

```http
GET /image?filename=../../../etc/passwd HTTP/2
```

Respuesta esperada:

```text
root:x:0:0:root:/root:/bin/bash
...
```

## Qué aprendimos del lab

```text
request de una imagen
       ↓
filename controlado por el usuario
       ↓
../../../
       ↓
escapamos del directorio de imágenes
       ↓
/etc/passwd
       ↓
el servidor devuelve un archivo no autorizado
```

> Haber reproducido la misma prueba desde HTTP history + Repeater también confirma la vulnerabilidad, pero para el material de estudio priorizamos el workflow que enseña PortSwigger.

---

# Common obstacles to exploiting path traversal vulnerabilities

PortSwigger presenta varias defensas comunes y bypasses correspondientes. Estos pertenecen principalmente al learning path completo / Practitioner y deben estudiarse en su orden.

## 1. Absolute path bypass

Si la aplicación bloquea secuencias traversal pero trata el valor como una ruta relativa al working directory, puede funcionar:

```text
/etc/passwd
```

## 2. Nested traversal sequences

Si una aplicación elimina `../` de forma no recursiva, pueden existir secuencias como:

```text
....//
```

que, tras una sanitización incompleta, vuelvan a producir una secuencia traversal válida.

## 3. URL encoding y double URL encoding

Ejemplos que PortSwigger estudia en este contexto:

```text
%2e%2e%2f
%252e%252e%252f
```

La idea importante es entender **cuándo ocurre el URL decoding** y cuántas veces se decodifica el valor.

## 4. Validación del inicio de la ruta

Si la aplicación exige que el valor empiece por una carpeta concreta, puede ser necesario conservarla:

```text
/var/www/images/../../../etc/passwd
```

## 5. Extensión obligatoria y null byte

Si la aplicación exige una extensión como `.png`, PortSwigger incluye un lab donde se usa:

```text
../../../etc/passwd%00.png
```

No asumir que este bypass funciona universalmente: se estudia dentro del contexto concreto del lab y de la tecnología vulnerable.

---

# Qué observar en Burp

Según el ejercicio, debemos aprender a identificar:

- la request que obtiene el recurso;
- el parámetro de archivo;
- cómo cambia la response al modificarlo;
- status code;
- longitud;
- contenido devuelto;
- errores de validación.

El uso de **Proxy, Intercept, HTTP history o Repeater** depende del workflow que PortSwigger enseñe en cada ejercicio. No debemos convertir Repeater en una respuesta automática para todos los labs.

---

# Cómo prevenir Path Traversal

PortSwigger recomienda, como primera opción, evitar pasar datos controlados por el usuario a APIs del filesystem.

Cuando no sea posible, la defensa debe incluir validación de entrada y canonicalización segura de la ruta, verificando después que la ruta canonicalizada permanece dentro del directorio esperado.

---

# Método mental

```text
¿La aplicación recibe un filename/path?
        ↓
¿Ese valor termina en una operación del filesystem?
        ↓
¿Puedo influir en la ruta?
        ↓
¿Existe alguna defensa?
        ↓
Entender qué defensa aplica
        ↓
Usar la técnica correspondiente que enseña PortSwigger
```

---

# Errores a evitar

- Memorizar solo `../../../etc/passwd` sin entender la ruta.
- Memorizar un número fijo de `../`.
- Probar bypasses Practitioner antes de entender el caso simple.
- Confundir URL encoding con traversal en sí mismo.
- Cambiar muchas cosas simultáneamente.
- Resolver el lab de una manera distinta y no practicar después el workflow oficial.

---

# Checklist

- [x] Entiendo qué significa `../`.
- [x] Entiendo cómo se sale de `/var/www/images/`.
- [x] Sé identificar un parámetro `filename`.
- [x] He recuperado `/etc/passwd` en el lab simple.
- [ ] Puedo repetir el lab usando el workflow oficial de interceptación sin ayuda.
- [ ] Entiendo absolute path bypass.
- [ ] Entiendo nested traversal.
- [ ] Entiendo URL encoding y double encoding en este contexto.
- [ ] Entiendo validación de prefijo.
- [ ] Entiendo el null-byte bypass del lab correspondiente.

---

# Registro

## File path traversal, simple case

- Estado: ✅ resuelto.
- Objetivo: leer `/etc/passwd`.
- Parámetro relevante: `filename`.
- Payload: `../../../etc/passwd`.
- Concepto aprendido: lectura arbitraria de archivos mediante traversal.
- Pendiente de dominio: poder reproducir con soltura el workflow oficial de Burp sin instrucciones.

---

# Regla rápida

**Primero entender cómo la aplicación construye la ruta; después estudiar los bypasses en el orden de PortSwigger.**
