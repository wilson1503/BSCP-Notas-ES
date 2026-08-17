# Burp Intruder — conceptos básicos

> Referencia rápida para entender los labs de Authentication. El workflow específico de cada lab sigue teniendo prioridad.

## Qué es Intruder

**Burp Intruder** permite repetir una misma request muchas veces sustituyendo uno o más valores por una lista de payloads.

Usos típicos en Web Security Academy:

- brute force;
- username enumeration;
- fuzzing;
- probar identificadores;
- descubrir diferencias entre respuestas.

---

## Flujo básico

```text
Proxy → HTTP history
        ↓
seleccionar request interesante
        ↓
Send to Intruder
        ↓
marcar posición §...§
        ↓
seleccionar attack type
        ↓
cargar payloads
        ↓
Start attack
        ↓
comparar resultados
```

---

# Payload positions

Una posición se representa así:

```text
username=§prueba§&password=test
```

Intruder sustituye solamente lo que está dentro de `§...§`.

## Add §

1. Seleccionar texto.
2. Pulsar **Add §**.

## Clear §

Elimina todas las posiciones activas para configurarlas de nuevo.

---

# Sniper

**Sniper** es apropiado cuando queremos probar una lista sobre una posición concreta.

Ejemplo:

```text
username=§PAYLOAD§&password=test
```

Payload list:

```text
alice
bob
carlos
administrator
```

Intruder enviará una request por cada valor.

---

# Simple list

`Simple list` permite pegar directamente una lista de valores que Intruder usará como payloads.

En los labs de PortSwigger debemos usar las listas proporcionadas por el propio lab cuando la solución oficial lo indica.

---

# Qué columnas mirar

## Status

Puede revelar comportamientos distintos:

```text
200 → respuesta normal/fallo
302 → redirect; en un login puede indicar éxito
```

No asumirlo universalmente: siempre revisar la Response.

## Length

Si casi todas las respuestas tienen el mismo tamaño y una es diferente, puede indicar que el servidor respondió de otra manera.

## Response

Abrir la respuesta real y buscar diferencias como:

```text
Invalid username
Incorrect password
Account locked
Welcome
```

## Time / response timing

Algunos labs posteriores pueden revelar información mediante diferencias de tiempo.

---

# Intruder no decide por nosotros

Intruder automatiza requests. Nosotros debemos interpretar el resultado.

```text
automatización ≠ vulnerabilidad confirmada
```

Siempre preguntar:

```text
¿Qué diferencia veo?
¿Por qué podría existir?
¿Es repetible?
¿Qué hipótesis confirma?
```

---

# Sniper vs Cluster bomb

Durante el primer lab Apprentice de Authentication, PortSwigger utiliza **Sniper** dos veces:

```text
1. username variable + password fijo
2. username fijo + password variable
```

También menciona que un `Cluster bomb` podría probar todas las combinaciones de username y password, pero sería menos eficiente para ese ejercicio.

La lección de estudio es:

```text
enumerar primero
→ reducir espacio de búsqueda
→ brute-forcear después
```

---

# Checklist

- [ ] Sé encontrar una request en HTTP history.
- [ ] Sé usar Send to Intruder.
- [ ] Entiendo `§...§`.
- [ ] Sé usar Add § y Clear §.
- [ ] Sé seleccionar Sniper.
- [ ] Sé cargar una Simple list.
- [ ] Sé iniciar el ataque.
- [ ] Sé ordenar por Length y Status.
- [ ] Sé abrir una Response.
- [ ] Sé explicar por qué una anomalía es importante.
