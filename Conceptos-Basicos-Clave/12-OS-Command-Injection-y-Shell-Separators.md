# OS Command Injection y separadores del shell

> Fuente principal: PortSwigger Web Security Academy.

## Idea básica

Una aplicación web puede llamar programas o comandos del sistema operativo para realizar tareas internas.

Ejemplo conceptual:

```text
stockreport.pl 1 2
```

Si valores como `1` o `2` vienen directamente de una request HTTP y se concatenan sin una validación segura, ciertos caracteres pueden cambiar la forma en que el shell interpreta el comando.

---

# Qué es un shell

El **shell** es un intérprete de comandos.

En lugar de recibir únicamente texto, reconoce sintaxis especial como separadores, redirecciones y operadores.

Por eso esto:

```text
1|whoami
```

no necesariamente significa literalmente "el texto 1, una barra y whoami".

El carácter `|` puede tener significado para el shell.

---

# Separadores que enseña PortSwigger

Comunes en Windows y Unix:

```text
&
&&
|
||
```

Adicionales habituales en Unix:

```text
;
newline
`comando`
$(comando)
```

No todos se comportan igual y no todos funcionan en todos los contextos.

La lección no es memorizar uno universal, sino reconocer que **metacaracteres del shell pueden modificar el comando original**.

---

# `|` en el lab Apprentice

PortSwigger utiliza:

```text
storeId=1|whoami
```

El operador `|` es un pipe. En un shell normalmente conecta la salida de un comando con la entrada de otro.

Para este laboratorio lo importante es que permite introducir y ejecutar `whoami` dentro del comando generado por la aplicación.

---

# `whoami`

`whoami` devuelve el usuario actual del sistema.

Funciona tanto en Linux como en Windows y es útil como prueba porque genera una salida corta y fácil de reconocer.

Ejemplo conceptual:

```text
www-data
```

No significa que ese nombre tenga que aparecer siempre; depende del entorno.

---

# In-band vs blind

## In-band

La aplicación devuelve directamente la salida del comando en la respuesta HTTP.

```text
whoami
   ↓
www-data aparece en Response
```

El lab Apprentice **OS command injection, simple case** es de este tipo.

## Blind

El comando puede ejecutarse pero su salida no aparece directamente en la response.

PortSwigger cubre esto más adelante con técnicas como delays, redirección u OAST. No forma parte del lab básico actual.

---

# URL encoding

Los parámetros HTTP pueden mostrar caracteres especiales codificados.

Por ejemplo:

```text
|
```

puede aparecer representado como:

```text
%7C
```

Esto no cambia el concepto lógico del payload. Burp o el navegador pueden aplicar encoding durante el transporte.

---

# Diferencia con otras vulnerabilidades

## SQL injection

```text
entrada controlada
→ termina dentro de una consulta SQL
→ el motor SQL interpreta sintaxis
```

## OS command injection

```text
entrada controlada
→ termina dentro de un comando del SO
→ el shell interpreta sintaxis
```

La pregunta clave es siempre:

> **¿Qué intérprete va a procesar finalmente mi entrada?**

---

# Regla mental

```text
¿El servidor llama a un comando externo?
        ↓
¿usa valores de la request como argumentos?
        ↓
¿esos valores llegan al shell?
        ↓
¿un metacarácter cambia la interpretación?
        ↓
¿aparece salida de un comando adicional?
```

---

# Qué recordar para el bloque Apprentice

```text
Función: Check stock
Request: POST /product/stock
Parámetro: storeId
Payload oficial: 1|whoami
Objetivo: ver el usuario actual en la Response
```
