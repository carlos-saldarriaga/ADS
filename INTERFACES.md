# Descomposición de Sistemas e Interfaces en Diseño de Software

## Contenidos
1. [Introducción](#introducción)
2. [Principios de Diseño de Software](#principios-de-diseño-de-software)
3. [Descomposición de Sistemas](#descomposición-de-sistemas)
4. [Interfaces: Conceptos y Diferencias](#interfaces-conceptos-y-diferencias)
5. [Ejemplo Práctico: Sistema de Hospital](#ejemplo-práctico-sistema-de-hospital)
6. [De la Teoría a la Implementación](#de-la-teoría-a-la-implementación)
7. [Referencias y Recursos Adicionales](#referencias-y-recursos-adicionales)

## Introducción

El diseño de software es una disciplina fundamental en la ingeniería de software que establece la estructura interna y la organización de un sistema, definiendo sus componentes, interfaces y comportamientos antes de la implementación. Este documento sirve como guía para comprender cómo descomponer sistemas complejos en unidades más pequeñas y manejables, y cómo diseñar interfaces efectivas entre estos componentes.

## Principios de Diseño de Software

### Principios SOLID

1. **Principio de Responsabilidad Única (SRP)**: Una clase debe tener una y solo una razón para cambiar.
2. **Principio Abierto/Cerrado (OCP)**: Las entidades de software deben estar abiertas para la extensión pero cerradas para la modificación.
3. **Principio de Sustitución de Liskov (LSP)**: Los objetos de una superclase deben poder ser reemplazados por objetos de sus subclases sin afectar la funcionalidad.
4. **Principio de Segregación de Interfaces (ISP)**: Ningún cliente debe ser forzado a depender de interfaces que no utiliza.
5. **Principio de Inversión de Dependencias (DIP)**: Los módulos de alto nivel no deben depender de módulos de bajo nivel. Ambos deben depender de abstracciones.

### Otros Principios Importantes

- **DRY (Don't Repeat Yourself)**: Evitar la duplicación de código y conocimiento.
- **KISS (Keep It Simple, Stupid)**: Mantener las soluciones simples.
- **Alta cohesión, bajo acoplamiento**: Elementos relacionados juntos (cohesión) y minimizar dependencias entre módulos (acoplamiento).

## Descomposición de Sistemas

La descomposición es el proceso de dividir un sistema complejo en partes más pequeñas y manejables:

### Niveles de Descomposición

1. **Sistema**: La aplicación o solución completa.
2. **Subsistemas**: Grandes bloques funcionales con responsabilidades bien definidas.
3. **Componentes**: Unidades de software independientes y reemplazables.
4. **Clases/Módulos**: Implementaciones específicas de responsabilidades.
5. **Métodos/Funciones**: Operaciones individuales.

### Beneficios de la Descomposición

- **Manejo de complejidad**: Divide problemas grandes en problemas más pequeños y manejables.
- **Desarrollo paralelo**: Diferentes equipos pueden trabajar en diferentes componentes.
- **Reutilización**: Componentes bien diseñados pueden usarse en múltiples contextos.
- **Mantenibilidad**: Es más fácil entender, modificar y probar componentes pequeños.
- **Evolución**: Los componentes pueden evolucionar independientemente.

## Interfaces: Conceptos y Diferencias

### Interfaces en Programación Orientada a Objetos

Una interfaz en POO es un constructo del lenguaje que:
- Define un contrato de métodos que las clases deben implementar
- No contiene estado (variables de instancia)
- Permite polimorfismo sin herencia

```java
// Ejemplo de interfaz en Java
public interface Dibujable {
    void dibujar();
    void escalar(double factor);
}
```

### Interfaces en Arquitectura de Componentes

En diseño de sistemas, una interfaz se refiere a:
- El conjunto de servicios o operaciones que un componente expone
- La "puerta de entrada" o "API" que define cómo interactuar con un componente
- Un contrato funcional entre diferentes partes del sistema

```java
// Interfaz de componente (implementada como interfaz de Java)
public interface GestorTriajeService {
    void registrarSignosVitales(String pacienteId, SignosVitalesDTO datos);
    NivelPrioridadEnum calcularPrioridad(SignosVitalesDTO datos);
    List<PacienteDTO> obtenerPacientesPorPrioridad(NivelPrioridadEnum prioridad);
}
```

### Diferencias Clave

| Aspecto | Interfaces en POO | Interfaces de Componentes |
|---------|-------------------|---------------------------|
| **Propósito** | Definir contratos en código | Definir contratos arquitectónicos |
| **Implementación** | Construcción del lenguaje | Puede implementarse de múltiples formas |
| **Alcance** | Nivel de clase | Nivel de componente/subsistema |
| **Ejemplos** | Interface en Java, C#, TypeScript | APIs de servicios web, bibliotecas, módulos |

### Herencia vs Interfaces en POO

| Característica | Herencia (extends) | Interfaces (implements) |
|----------------|--------------------|-----------------------|
| **Propósito** | Reutilización de código | Definir contratos |
| **Cantidad** | Una sola clase padre | Múltiples interfaces |
| **Elementos heredados** | Atributos y métodos | Solo definiciones de métodos |
| **Estado** | Hereda estado | No hereda estado |
| **Uso ideal** | Relación "es-un" | Definir comportamientos |

## Ejemplo Práctico: Sistema de Hospital

Consideremos un sistema de gestión hospitalaria con los siguientes subsistemas:

### 1. Subsistema de Registro de Pacientes

**Responsabilidad**: Gestionar la información de pacientes y su registro en el sistema.

**Componentes**:
- **Gestor de Formularios de Ingreso**: Maneja la captura de datos de pacientes.
- **Repositorio de Pacientes**: Almacena y recupera información de pacientes.
- **Validador de Datos**: Verifica la integridad y validez de la información.

**Interfaces**:
```java
public interface GestorRegistroPacientesService {
    PacienteDTO registrarNuevoPaciente(DatosPacienteDTO datos);
    PacienteDTO buscarPaciente(String identificacion);
    List<PacienteDTO> buscarPacientesPorCriterio(CriterioBusquedaDTO criterio);
}
```

### 2. Subsistema de Triaje

**Responsabilidad**: Evaluar y clasificar pacientes según gravedad.

**Componentes**:
- **Gestor de Signos Vitales**: Captura y almacena signos vitales.
- **Calculador de Prioridad**: Determina nivel de urgencia según parámetros.
- **Asignador de Códigos**: Asigna códigos de colores/prioridades.

**Interfaces**:
```java
public interface GestorTriajeService {
    TriajeDTO realizarTriaje(String pacienteId, SignosVitalesDTO datos);
    NivelPrioridadEnum calcularPrioridad(SignosVitalesDTO datos);
    List<PacienteDTO> listarPacientesPorPrioridad();
}
```

## De la Teoría a la Implementación

Para implementar un sistema descompuesto con interfaces claras:

1. **Identificar subsistemas** basados en áreas funcionales.
2. **Descomponer en componentes** con responsabilidades únicas.
3. **Definir interfaces** para cada componente.
4. **Diseñar implementaciones** que respeten las interfaces.
5. **Establecer dependencias** entre componentes a través de sus interfaces.

### Ejemplo de Implementación

```java
// 1. Definir la interfaz del componente
public interface CalculadorPrioridadService {
    NivelPrioridadEnum calcularPrioridad(SignosVitalesDTO datos);
}

// 2. Implementar el componente
public class CalculadorPrioridadImpl implements CalculadorPrioridadService {
    @Override
    public NivelPrioridadEnum calcularPrioridad(SignosVitalesDTO datos) {
        if (datos.getTemperatura() > 39.0 || datos.getSaturacionOxigeno() < 92) {
            return NivelPrioridadEnum.ALTA;
        } else if (datos.getPresionSistolica() > 180 || datos.getNivelDolor() >= 8) {
            return NivelPrioridadEnum.MEDIA;
        } else {
            return NivelPrioridadEnum.BAJA;
        }
    }
}

// 3. Inyectar dependencias a través de interfaces
public class GestorTriajeImpl implements GestorTriajeService {
    private final CalculadorPrioridadService calculadorPrioridad;
    private final RepositorioPacientesService repositorioPacientes;
    
    // Inyección de dependencias (Principio de Inversión de Dependencias)
    public GestorTriajeImpl(
            CalculadorPrioridadService calculadorPrioridad,
            RepositorioPacientesService repositorioPacientes) {
        this.calculadorPrioridad = calculadorPrioridad;
        this.repositorioPacientes = repositorioPacientes;
    }
    
    @Override
    public TriajeDTO realizarTriaje(String pacienteId, SignosVitalesDTO datos) {
        // Implementación que usa las dependencias a través de sus interfaces
        NivelPrioridadEnum prioridad = calculadorPrioridad.calcularPrioridad(datos);
        // ...más lógica...
        return new TriajeDTO(/* datos del triaje */);
    }
    
    // Otros métodos...
}
```

## Referencias y Recursos Adicionales

- [Principios SOLID](https://es.wikipedia.org/wiki/SOLID)
- [Patrones de Diseño: Elementos de Software Orientado a Objetos Reutilizable](https://en.wikipedia.org/wiki/Design_Patterns)
- [Clean Architecture de Robert C. Martin](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
- [Arquitectura Hexagonal (Ports and Adapters)](https://alistair.cockburn.us/hexagonal-architecture/)

---

Desarrollado para el curso de Análisis y Diseño de Software, Pontificia Universidad Javeriana.
