# SOLID: Principios Fundamentales para un Diseño de Software Escalable y Mantenible

> Los principios SOLID constituyen uno de los pilares más importantes de la ingeniería de software moderna. Su correcta aplicación permite construir sistemas más flexibles, extensibles, testeables y fáciles de mantener a largo plazo.

---

# ¿Qué es SOLID?

SOLID es un acrónimo que representa cinco principios de diseño orientado a objetos definidos y promovidos por Robert C. Martin ("Uncle Bob"). Estos principios ayudan a reducir el acoplamiento entre componentes, mejorar la cohesión y facilitar la evolución del software.

| Letra | Principio |
|---------|------------|
| S | Single Responsibility Principle |
| O | Open/Closed Principle |
| L | Liskov Substitution Principle |
| I | Interface Segregation Principle |
| D | Dependency Inversion Principle |

---

# 1. Single Responsibility Principle (SRP)

## Definición

> Una clase debe tener una única razón para cambiar.

Cada módulo, clase o componente debe encargarse de una única responsabilidad dentro del sistema.

---

## Problema

Una clase que realiza múltiples tareas termina acumulando responsabilidades:

- Acceso a datos
- Reglas de negocio
- Validaciones
- Generación de reportes
- Logging

Cuando una responsabilidad cambia, aumenta el riesgo de romper otras funcionalidades.

---

## Ejemplo Incorrecto

```csharp
public class UserService
{
    public void CreateUser(User user)
    {
        Validate(user);
        SaveToDatabase(user);
        SendWelcomeEmail(user);
    }
}
```

La clase tiene múltiples responsabilidades:

- Validar usuarios
- Persistir usuarios
- Enviar correos

---

## Ejemplo Correcto

```csharp
public class UserValidator
{
    public void Validate(User user)
    {
        // Validación
    }
}

public class UserRepository
{
    public void Save(User user)
    {
        // Persistencia
    }
}

public class EmailService
{
    public void SendWelcomeEmail(User user)
    {
        // Envío de correo
    }
}
```

Cada componente tiene una única responsabilidad.

---

## Beneficios

- Mayor mantenibilidad
- Menor complejidad
- Mejor testabilidad
- Cambios más seguros

---

# 2. Open/Closed Principle (OCP)

## Definición

> Las entidades de software deben estar abiertas para extensión pero cerradas para modificación.

Debemos poder agregar nuevas funcionalidades sin modificar código ya probado.

---

## Problema

Modificar continuamente clases existentes incrementa el riesgo de introducir errores.

---

## Ejemplo Incorrecto

```csharp
public class DiscountCalculator
{
    public decimal Calculate(Customer customer)
    {
        if(customer.Type == "Regular")
            return 0.05m;

        if(customer.Type == "Premium")
            return 0.10m;

        if(customer.Type == "VIP")
            return 0.20m;

        return 0;
    }
}
```

Cada nuevo tipo de cliente obliga a modificar la clase.

---

## Ejemplo Correcto

```csharp
public interface IDiscountStrategy
{
    decimal Calculate();
}

public class RegularDiscount : IDiscountStrategy
{
    public decimal Calculate() => 0.05m;
}

public class PremiumDiscount : IDiscountStrategy
{
    public decimal Calculate() => 0.10m;
}

public class VipDiscount : IDiscountStrategy
{
    public decimal Calculate() => 0.20m;
}
```

Ahora pueden añadirse nuevos descuentos sin modificar código existente.

---

## Beneficios

- Menor riesgo de regresiones
- Mayor escalabilidad
- Código más flexible

---

# 3. Liskov Substitution Principle (LSP)

## Definición

> Los objetos de una clase derivada deben poder reemplazar a los objetos de su clase base sin alterar el comportamiento esperado del sistema.

Una subclase debe respetar el contrato de su clase padre.

---

## Problema

Una herencia mal diseñada rompe expectativas y genera errores difíciles de detectar.

---

## Ejemplo Incorrecto

```csharp
public class Bird
{
    public virtual void Fly()
    {
    }
}

public class Penguin : Bird
{
    public override void Fly()
    {
        throw new NotImplementedException();
    }
}
```

Un pingüino no puede volar, por lo que viola el contrato de la clase base.

---

## Ejemplo Correcto

```csharp
public abstract class Bird
{
}

public interface IFlyingBird
{
    void Fly();
}

public class Eagle : Bird, IFlyingBird
{
    public void Fly()
    {
    }
}

public class Penguin : Bird
{
}
```

Ahora cada tipo implementa únicamente los comportamientos que realmente posee.

---

## Beneficios

- Jerarquías más coherentes
- Menos errores de sustitución
- Diseño más robusto

---

# 4. Interface Segregation Principle (ISP)

## Definición

> Ningún cliente debe verse obligado a depender de métodos que no utiliza.

Es mejor tener interfaces pequeñas y específicas que interfaces grandes y genéricas.

---

## Problema

Las interfaces gigantes obligan a implementar funcionalidades innecesarias.

---

## Ejemplo Incorrecto

```csharp
public interface IWorker
{
    void Work();
    void Eat();
}

public class Robot : IWorker
{
    public void Work()
    {
    }

    public void Eat()
    {
        throw new NotImplementedException();
    }
}
```

Un robot no necesita comer.

---

## Ejemplo Correcto

```csharp
public interface IWorkable
{
    void Work();
}

public interface IEatable
{
    void Eat();
}

public class Human : IWorkable, IEatable
{
    public void Work()
    {
    }

    public void Eat()
    {
    }
}

public class Robot : IWorkable
{
    public void Work()
    {
    }
}
```

Cada implementación depende únicamente de lo que necesita.

---

## Beneficios

- Menor acoplamiento
- Interfaces más claras
- Mayor reutilización

---

# 5. Dependency Inversion Principle (DIP)

## Definición

> Los módulos de alto nivel no deben depender de módulos de bajo nivel. Ambos deben depender de abstracciones.

Las dependencias deben apuntar hacia interfaces o contratos, no hacia implementaciones concretas.

---

## Problema

Las dependencias directas dificultan cambios, pruebas y mantenimiento.

---

## Ejemplo Incorrecto

```csharp
public class OrderService
{
    private readonly SqlServerRepository _repository;

    public OrderService()
    {
        _repository = new SqlServerRepository();
    }
}
```

La clase está acoplada a una implementación específica.

---

## Ejemplo Correcto

```csharp
public interface IOrderRepository
{
    void Save(Order order);
}

public class SqlServerRepository : IOrderRepository
{
    public void Save(Order order)
    {
    }
}

public class OrderService
{
    private readonly IOrderRepository _repository;

    public OrderService(IOrderRepository repository)
    {
        _repository = repository;
    }
}
```

Ahora la implementación puede cambiar sin afectar la lógica de negocio.

---

## Beneficios

- Facilita pruebas unitarias
- Reduce el acoplamiento
- Permite intercambiar implementaciones fácilmente

---

# Cómo Aplicar SOLID en Proyectos Reales

## Antes de Crear una Clase

Pregúntate:

- ¿Tiene una única responsabilidad?
- ¿Puede crecer sin modificar código existente?
- ¿Respeta completamente el contrato de sus abstracciones?
- ¿Depende únicamente de lo que necesita?
- ¿Está desacoplada de implementaciones concretas?

---

## Señales de Alerta

### Violaciones comunes de SRP

- Clases con cientos o miles de líneas.
- Métodos excesivamente largos.
- Mezcla de lógica de negocio e infraestructura.

### Violaciones comunes de OCP

- Grandes bloques `if`, `else if` o `switch`.
- Cambios frecuentes en clases centrales.

### Violaciones comunes de LSP

- Métodos que lanzan excepciones porque "no aplican".
- Subclases que deshabilitan comportamientos heredados.

### Violaciones comunes de ISP

- Interfaces con demasiados métodos.
- Implementaciones vacías o excepciones constantes.

### Violaciones comunes de DIP

- Uso excesivo de `new`.
- Dependencias directas a bases de datos, APIs o frameworks.

---

# Beneficios de Aplicar SOLID

- Código más limpio y comprensible.
- Menor deuda técnica.
- Mejor cobertura de pruebas.
- Mayor velocidad de desarrollo.
- Menor coste de mantenimiento.
- Arquitecturas más escalables.
- Facilita el trabajo en equipos grandes.

---

# Conclusión

SOLID no es un conjunto de reglas rígidas, sino una guía para diseñar software profesional que pueda evolucionar de forma segura con el tiempo.

Aplicar estos principios de manera consistente permite construir sistemas:

- Más mantenibles.
- Más extensibles.
- Más testeables.
- Más robustos.
- Más preparados para el crecimiento del negocio.

La diferencia entre un proyecto que envejece bien y uno que se convierte en deuda técnica suele comenzar con decisiones de diseño tomadas desde el primer día.
