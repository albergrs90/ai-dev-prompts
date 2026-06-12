# DRY (Don't Repeat Yourself): Eliminando la Duplicación en el Software Profesional

> Cada pieza de conocimiento debe tener una única representación, inequívoca y autoritativa dentro de un sistema.

---

# Introducción

DRY (Don't Repeat Yourself) es uno de los principios fundamentales del desarrollo de software moderno. Fue introducido por Andy Hunt y Dave Thomas en su libro *The Pragmatic Programmer* y busca reducir la duplicación de lógica, reglas de negocio y conocimiento dentro de una aplicación.

La duplicación es una de las principales causas de:

- Errores recurrentes.
- Costes elevados de mantenimiento.
- Inconsistencias entre módulos.
- Incremento de la deuda técnica.
- Dificultad para evolucionar sistemas.

---

# ¿Qué significa realmente DRY?

Muchas personas interpretan DRY como:

> "No copiar y pegar código."

Sin embargo, el principio es mucho más profundo.

DRY establece que:

> Cada regla de negocio, algoritmo, validación o conocimiento debe existir en un único lugar dentro del sistema.

El problema no es la repetición de texto.

El problema es la repetición de conocimiento.

---

# Por Qué la Duplicación es Peligrosa

Cuando una misma lógica existe en varios lugares:

- Debe mantenerse varias veces.
- Puede evolucionar de forma diferente.
- Incrementa la probabilidad de errores.
- Dificulta el testing.
- Genera inconsistencias funcionales.

---

## Ejemplo Real

Supongamos que el IVA es del 21%.

### Código Duplicado

```csharp
public decimal CalculateInvoice(decimal amount)
{
    return amount * 1.21m;
}
```

```csharp
public decimal CalculateBudget(decimal amount)
{
    return amount * 1.21m;
}
```

```csharp
public decimal CalculateOrder(decimal amount)
{
    return amount * 1.21m;
}
```

Si mañana el IVA cambia al 23%, habrá que modificar múltiples ubicaciones.

---

## Código Correcto

```csharp
public static class TaxConfiguration
{
    public const decimal VatRate = 0.21m;
}
```

```csharp
public decimal CalculateInvoice(decimal amount)
{
    return amount * (1 + TaxConfiguration.VatRate);
}
```

```csharp
public decimal CalculateBudget(decimal amount)
{
    return amount * (1 + TaxConfiguration.VatRate);
}
```

Ahora existe una única fuente de verdad.

---

# Tipos de Duplicación

---

# 1. Duplicación de Código

La más evidente.

## Incorrecto

```csharp
public void CreateUser(User user)
{
    Console.WriteLine("Creating user...");
}

public void CreateCustomer(Customer customer)
{
    Console.WriteLine("Creating customer...");
}
```

---

## Correcto

```csharp
private void LogCreation(string entity)
{
    Console.WriteLine($"Creating {entity}...");
}
```

---

# 2. Duplicación de Reglas de Negocio

Mucho más peligrosa que la duplicación de código.

## Incorrecto

```csharp
if(user.Age >= 18)
```

```csharp
if(customer.Age >= 18)
```

```csharp
if(employee.Age >= 18)
```

---

## Correcto

```csharp
public static class AgeRules
{
    public const int AdultAge = 18;
}
```

```csharp
if(user.Age >= AgeRules.AdultAge)
```

---

# 3. Duplicación de Configuración

Frecuente en aplicaciones empresariales.

## Incorrecto

```json
{
  "timeout": 30
}
```

```json
{
  "timeout": 30
}
```

```json
{
  "timeout": 30
}
```

---

## Correcto

Centralizar la configuración:

```json
{
  "applicationSettings": {
    "timeout": 30
  }
}
```

---

# 4. Duplicación de Consultas

## Incorrecto

```sql
SELECT *
FROM Users
WHERE IsActive = 1
```

Repetida en múltiples repositorios.

---

## Correcto

Crear una vista:

```sql
CREATE VIEW ActiveUsers
AS
SELECT *
FROM Users
WHERE IsActive = 1
```

O encapsular la consulta en un repositorio compartido.

---

# Duplicación Accidental vs Duplicación Intencional

No toda repetición debe eliminarse inmediatamente.

---

## Duplicación Accidental

Ocurre cuando:

- Se copia código rápidamente.
- No existe una abstracción adecuada.
- Se ignora una solución común.

Debe eliminarse.

---

## Duplicación Intencional

Puede ser válida cuando:

- Los contextos son diferentes.
- La abstracción sería más compleja que la repetición.
- El dominio aún no está suficientemente definido.

---

## Regla de las Tres Veces

Una práctica ampliamente aceptada:

### Primera vez

Implementa la solución.

### Segunda vez

Acepta cierta repetición.

### Tercera vez

Extrae una abstracción.

---

# DRY y Clean Code

El código limpio busca:

- Legibilidad.
- Simplicidad.
- Mantenibilidad.

DRY complementa estos objetivos eliminando redundancias innecesarias.

Sin embargo:

> Aplicar DRY de forma extrema puede producir abstracciones complejas y difíciles de comprender.

---

# DRY vs Sobreingeniería

Uno de los errores más comunes es crear abstracciones prematuras.

---

## Incorrecto

Crear:

- Interfaces innecesarias.
- Clases genéricas excesivas.
- Frameworks internos sin necesidad.

Para eliminar una duplicación que sólo aparece una vez.

---

## Correcto

Aplicar DRY únicamente cuando exista una duplicación real y estable.

---

# Señales de que DRY Está Siendo Violado

- Mismo código en múltiples clases.
- Reglas de negocio repetidas.
- Mismos valores mágicos distribuidos.
- Consultas SQL idénticas.
- Configuraciones replicadas.
- Validaciones copiadas y pegadas.

---

# Beneficios de Aplicar DRY

## Mantenimiento Simplificado

Los cambios ocurren en un único lugar.

---

## Menos Errores

Reduce inconsistencias entre módulos.

---

## Mayor Productividad

Menos código para mantener.

---

## Mejor Testabilidad

Una única implementación implica menos escenarios duplicados.

---

## Mayor Escalabilidad

Las modificaciones futuras son más seguras.

---

# DRY en Arquitecturas Modernas

DRY sigue siendo esencial en:

- Clean Architecture
- Domain-Driven Design (DDD)
- Microservicios
- Arquitecturas Hexagonales
- Sistemas Distribuidos

Sin embargo, en arquitecturas distribuidas:

> Es preferible cierta duplicación antes que generar dependencias excesivas entre servicios.

DRY debe aplicarse dentro del contexto adecuado.

---

# Anti-Patrones Relacionados

## Copy-Paste Programming

Duplicar código para avanzar más rápido.

Consecuencia:

- Mayor deuda técnica.
- Bugs repetidos.
- Mantenimiento costoso.

---

## Golden Hammer

Crear una abstracción genérica para absolutamente todo.

Consecuencia:

- Complejidad innecesaria.
- Código difícil de entender.

---

## Premature Abstraction

Abstraer demasiado pronto.

Consecuencia:

- Diseño rígido.
- Mayor dificultad de evolución.

---

# Checklist Profesional

Antes de escribir código, pregúntate:

- ¿Esta lógica ya existe?
- ¿Estoy duplicando una regla de negocio?
- ¿Existe una fuente única de verdad?
- ¿La abstracción simplifica o complica?
- ¿Estoy eliminando conocimiento duplicado o simplemente texto repetido?

---

# Conclusión

DRY no consiste únicamente en evitar copiar y pegar código.

Su verdadero objetivo es garantizar que cada conocimiento importante del sistema tenga una única representación mantenible y confiable.

Aplicado correctamente, DRY permite construir software:

- Más limpio.
- Más consistente.
- Más fácil de mantener.
- Más fácil de evolucionar.
- Menos propenso a errores.

La duplicación genera deuda técnica. La centralización del conocimiento genera software sostenible.

---

## Referencias

- The Pragmatic Programmer — Andy Hunt & Dave Thomas
- Clean Code — Robert C. Martin
- Clean Architecture — Robert C. Martin
- Refactoring — Martin Fowler
- Domain-Driven Design — Eric Evans
