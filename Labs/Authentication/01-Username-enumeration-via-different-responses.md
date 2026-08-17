# Authentication Lab 1 — Username enumeration via different responses

> Procedimiento basado en la solución oficial actual de PortSwigger. Este lab está diseñado para practicar **Burp Intruder**.

## Objetivo

```text
Enumerar un username válido
→ brute-forcear su password
→ iniciar sesión
→ abrir My account
```

PortSwigger proporciona dos listas en la descripción del lab:

- **Candidate usernames**
- **Candidate passwords**

No inventar wordlists distintas mientras estudiamos el workflow oficial.

---

# Parte A — Generar una request de login fallida

1. Abrir el lab con Burp ejecutándose.
2. Ir a **My account / Login**.
3. Introducir datos falsos, por ejemplo:

```text
username: prueba
password: prueba
```

4. Enviar el formulario.

Ahora Burp debería tener una request parecida a:

```http
POST /login HTTP/2
Host: TU-LAB.web-security-academy.net
Content-Type: application/x-www-form-urlencoded

username=prueba&password=prueba
```

---

# Parte B — Encontrar `POST /login` en Burp

1. Ir a:

```text
Proxy
→ HTTP history
```

2. Buscar la fila:

```text
POST /login
```

3. Seleccionarla y comprobar que el body contiene:

```text
username=...
password=...
```

---

# Parte C — Enviar el username a Intruder

PortSwigger indica seleccionar **solamente el valor del username**.

Ejemplo:

```text
username=prueba&password=prueba
         ^^^^^^
```

1. Marcar con el mouse el valor `prueba` de `username`.
2. Clic derecho.
3. Seleccionar:

```text
Send to Intruder
```

4. Ir a la pestaña:

```text
Intruder
```

Burp debería haber creado automáticamente una posición:

```text
username=§prueba§&password=prueba
```

Los símbolos `§` indican **dónde Intruder va a sustituir valores**.

---

# Parte D — Configurar enumeración de usernames

## 1. Attack type

Seleccionar:

```text
Sniper
```

Solo tenemos una posición activa, por lo que Sniper probará la lista completa en ese lugar.

## 2. Payloads

En el panel **Payloads**:

```text
Payload type → Simple list
```

En **Payload configuration** pegar la lista **Candidate usernames** proporcionada por PortSwigger.

La contraseña se mantiene fija e incorrecta durante esta primera fase.

Ejemplo conceptual:

```text
alice  + password_falsa
bob    + password_falsa
carlos + password_falsa
...
```

## 3. Ejecutar

Pulsar:

```text
Start attack
```

---

# Parte E — Encontrar el username válido

Cuando termine el ataque, revisar especialmente:

```text
Payload
Status
Length
```

PortSwigger indica ordenar por **Length**.

1. Hacer clic en la columna `Length`.
2. Buscar una respuesta cuya longitud sea diferente a las demás.
3. Seleccionarla.
4. Revisar su **Response**.

La mayoría dirán:

```text
Invalid username
```

El username válido devuelve:

```text
Incorrect password
```

## Por qué esto confirma el username

```text
Invalid username
→ el servidor ni siquiera encontró esa cuenta

Incorrect password
→ encontró la cuenta, pero el password fue incorrecto
```

Guardar el username asociado a esa respuesta.

---

# Parte F — Preparar brute force del password

Cerrar la ventana del ataque y volver a la configuración de Intruder.

1. Pulsar:

```text
Clear §
```

Esto elimina las posiciones anteriores.

2. Sustituir manualmente el username por el válido que acabamos de descubrir.

Ejemplo:

```text
username=USUARIO_VALIDO&password=prueba
```

3. Seleccionar solamente el valor de `password`.
4. Pulsar:

```text
Add §
```

Debe quedar:

```text
username=USUARIO_VALIDO&password=§prueba§
```

---

# Parte G — Cargar Candidate passwords

En **Payloads**:

1. Mantener:

```text
Payload type → Simple list
```

2. Borrar la lista anterior de usernames.
3. Pegar **Candidate passwords** proporcionada por PortSwigger.
4. Pulsar:

```text
Start attack
```

---

# Parte H — Encontrar el password correcto

Esta vez PortSwigger indica mirar principalmente **Status**.

La mayoría de intentos fallidos devolverán:

```text
200
```

Uno debería devolver:

```text
302
```

¿Por qué?

```text
password incorrecto
→ login vuelve a mostrar formulario
→ 200 OK

password correcto
→ servidor redirige a la cuenta
→ 302 Found
```

Seleccionar la fila `302` y guardar el password que aparece en `Payload`.

---

# Parte I — Resolver el lab

Volver al navegador y hacer login con:

```text
username: USUARIO_VALIDO
password: PASSWORD_VALIDO
```

Abrir **My account**.

El lab debe marcar:

```text
Solved
```

---

# Lo que tengo que aprender de Intruder

```text
POST /login
    ↓
Send to Intruder
    ↓
§posición§
    ↓
Sniper
    ↓
Simple list
    ↓
Start attack
    ↓
analizar diferencias
```

## Botones nuevos

### `§...§`
Marca una posición en la request donde Intruder insertará payloads.

### `Add §`
Añade una posición al texto seleccionado.

### `Clear §`
Borra las posiciones configuradas.

### `Sniper`
Prueba una lista de payloads sobre una posición de forma secuencial.

### `Simple list`
Permite pegar una lista de strings que serán usados como payloads.

---

# Error que debemos evitar

No empezar probando todas las combinaciones username × password.

PortSwigger señala que para este lab es más eficiente:

```text
enumerar username
        ↓
fijar username
        ↓
brute-force password
```

Un Cluster bomb podría encontrar las credenciales, pero **no es el workflow principal que queremos aprender en este ejercicio**.

---

# Checklist

- [ ] Encontré `POST /login` en HTTP history.
- [ ] Entiendo el body `username=...&password=...`.
- [ ] Envié una posición a Intruder.
- [ ] Entiendo qué significan los `§`.
- [ ] Usé `Sniper`.
- [ ] Usé `Simple list`.
- [ ] Encontré el username por diferencia de Response/Length.
- [ ] Cambié la posición al password.
- [ ] Encontré el password por el `302`.
- [ ] Entré a la cuenta.
- [ ] Puedo explicar por qué `Invalid username` vs `Incorrect password` es una vulnerabilidad.
