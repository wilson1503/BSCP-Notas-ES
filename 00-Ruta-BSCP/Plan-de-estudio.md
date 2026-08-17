# Plan de estudio BSCP

## Principio principal

Durante la etapa de aprendizaje, **PortSwigger es la fuente de verdad**. El objetivo no es únicamente conseguir que un laboratorio marque `Solved`, sino aprender la técnica, el razonamiento y el uso de Burp que el propio laboratorio pretende enseñar.

Si existe una solución oficial, su workflow se considera el método principal de estudio. Las alternativas pueden ser válidas técnicamente, pero se documentarán aparte y no reemplazarán el procedimiento oficial.

## Ritmo objetivo

- Dedicación estimada: **4 horas por día**.
- Prioridad: comprensión práctica, no velocidad.
- Cada tema se estudia con teoría oficial, laboratorio y notas propias en español.

## Método por tema

1. Seguir el **Learning Path oficial de PortSwigger** en su orden.
2. Leer la teoría oficial correspondiente antes del lab.
3. Resumir el concepto en español sin cambiar su significado técnico.
4. Identificar qué comportamiento, request, response o dato controlado por el usuario es relevante.
5. Intentar resolver el lab sin consultar la solución.
6. Si me bloqueo, volver a la teoría y usar pistas de forma gradual.
7. Si finalmente necesito la solución, leer la **solución oficial de PortSwigger**.
8. Reproducir el procedimiento oficial de principio a fin, incluso si anteriormente resolví el lab mediante una técnica alternativa.
9. Documentar:
   - concepto que PortSwigger estaba enseñando;
   - señal inicial;
   - request/response relevante;
   - herramienta de Burp utilizada;
   - workflow oficial;
   - modificación realizada;
   - por qué funcionó;
   - control de seguridad que falló;
   - error personal o lección aprendida.
10. Solo después, si aporta valor, registrar técnicas alternativas en una sección separada de preparación/repaso.

## Regla para Burp Suite

No asumir que **Repeater** es siempre la herramienta correcta. Hay que aprender el flujo que corresponda al ejercicio.

Ejemplos:

```text
Proxy → request interception
Proxy → response interception
HTTP history
Repeater
Intruder
Decoder
Comparer
Collaborator
```

Si PortSwigger usa una función concreta en la solución del lab, se practica esa función.

## Primera fase

### Server-side vulnerabilities — Apprentice

Seguir el learning path oficial:

- [ ] Path Traversal
- [ ] Access Control
- [ ] Authentication
- [ ] SSRF
- [ ] File Upload Vulnerabilities
- [ ] OS Command Injection
- [ ] SQL Injection

## Preparación oficial posterior

Una vez consolidados los fundamentos, seguir la progresión recomendada por PortSwigger:

1. Completar los labs **Apprentice y Practitioner** mientras se estudian los temas.
2. Practicar **Mystery Labs** para aprender a identificar vulnerabilidades sin pistas contextuales.
3. Practicar Mystery Labs de distintos temas para mejorar reconocimiento y enumeración.
4. Realizar los **Practice Exams** oficiales.
5. Estudiar las recomendaciones y hints oficiales del examen.

## Segunda fase futura — Preparación para examen

Esta fase se mantendrá separada del material didáctico oficial.

Ahí construiremos en español:

- cheat sheets;
- payload collections organizadas;
- checklists rápidas;
- snippets y comandos;
- workflows eficientes;
- metodología de enumeración;
- gestión de tiempo;
- aprendizajes de repositorios comunitarios y candidatos BSCP.

Regla: **primero aprender el método oficial; después optimizarlo para velocidad**.
