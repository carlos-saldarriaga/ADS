# Taller: Diseño y Arquitectura de Software

## Introducción

Este taller integra los conocimientos adquiridos sobre patrones de diseño y arquitecturas de software en un proyecto práctico. Aplicarás principios SOLID, patrones de diseño y una arquitectura por capas para resolver un problema del mundo real, demostrando tu comprensión de los conceptos teóricos mediante su implementación práctica.

## Objetivos de Aprendizaje

- Aplicar una arquitectura por capas clara y bien definida
- Implementar correctamente al menos tres patrones de diseño diferentes
- Usar principios SOLID en el desarrollo de la solución
- Crear un sistema modular con bajo acoplamiento y alta cohesión
- Documentar decisiones de diseño y arquitectura

## Proyecto: Sistema de Gestión de Pedidos de Restaurante

Desarrollarás un sistema para gestionar pedidos de un restaurante, con las siguientes características:

### Requisitos Funcionales

1. Gestionar el menú del restaurante (crear/modificar/eliminar platos)
2. Crear y procesar pedidos de clientes
3. Seguir el estado de los pedidos (Recibido → En Preparación → Listo → Entregado)
4. Calcular el total de un pedido (incluyendo impuestos y descuentos)
5. Notificar cuando un pedido cambia de estado
6. Generar reportes simples (ej. ventas diarias, platos más populares)

### Requisitos Técnicos

1. Utilizar Java (sin frameworks complejos)
2. Seguir una arquitectura por capas definida (presentación, lógica de negocio, persistencia)
3. Implementar al menos tres patrones de diseño diferentes
4. Almacenar datos en memoria (opcional: persistencia en archivos)
5. Incluir un conjunto básico de pruebas unitarias

## Arquitectura Propuesta

El sistema debe implementar una arquitectura por capas:

### 1. Capa de Presentación
- Interfaz de consola para interacción con usuarios
- Formateo de datos para presentación
- Sin lógica de negocio

### 2. Capa de Aplicación/Servicios
- Orquestación de operaciones del sistema
- Implementación de casos de uso
- Coordinación entre componentes

### 3. Capa de Dominio
- Entidades y lógica de negocio central
- Reglas y validaciones del negocio
- Independiente de infraestructura

### 4. Capa de Infraestructura
- Implementación de persistencia
- Adaptadores para servicios externos
- Gestión de recursos del sistema

## Patrones de Diseño a Implementar

Debes implementar al menos tres de los siguientes patrones (uno de cada categoría):

### Patrones Creacionales
- Factory Method: Para crear diferentes tipos de platos
- Builder: Para construcción de pedidos complejos
- Singleton: Para gestión de recursos compartidos

### Patrones Estructurales
- Decorator: Para añadir extras o modificaciones a los platos
- Adapter: Para integrar diferentes sistemas de pago
- Composite: Para crear menús y combos

### Patrones de Comportamiento
- Observer: Para notificar cambios de estado en pedidos
- Strategy: Para diferentes políticas de descuento o cálculo de precios
- State: Para gestionar las transiciones de estado de un pedido
- Command: Para encapsular operaciones como comandos ejecutables

## Estructura de Paquetes Recomendada

```
com.restaurant/
├── ui/                      # Capa de presentación
│   └── RestaurantConsoleApp
├── application/             # Capa de aplicación/servicios
│   ├── OrderService
│   ├── MenuService
│   └── ReportService
├── domain/                  # Capa de dominio
│   ├── model/               # Entidades de dominio
│   │   ├── MenuItem
│   │   ├── Order
│   │   └── Customer
│   ├── service/             # Servicios de dominio
│   │   ├── PricingService
│   │   └── OrderProcessingService
│   └── repository/          # Interfaces de repositorio
│       ├── MenuRepository
│       └── OrderRepository
└── infrastructure/          # Capa de infraestructura
    ├── persistence/         # Implementaciones de repositorio
    │   ├── InMemoryMenuRepository
    │   └── InMemoryOrderRepository
    └── notification/        # Sistemas de notificación
        └── OrderNotifier
```

## Diagrama UML Básico

```
+------------------+       +------------------+       +------------------+
|     MenuItem     |       |      Order       |       |     Customer     |
+------------------+       +------------------+       +------------------+
| -id: String      |       | -id: String      |       | -id: String      |
| -name: String    |<----->| -items: List     |<----->| -name: String    |
| -price: double   |       | -customer: Cust. |       | -address: String |
| -category: String|       | -status: Status  |       | -phone: String   |
| -description: Str|       | -date: Date      |       +------------------+
+------------------+       +------------------+
         ^                          |
         |                          |
+------------------+                |
|     Discount     |<---------------+
+------------------+
| -calculate()     |
+------------------+
         ^
         |
+------------------+------------------+
|                  |                  |
| FixedDiscount    | PercentDiscount  |
+------------------+------------------+
```

## Entregables

1. Código fuente completo y organizado según la estructura propuesta
2. Documentación de la arquitectura implementada (en forma de README)
3. Diagrama UML de las clases principales
4. Descripción de los patrones de diseño utilizados y su implementación
5. Conjunto de pruebas unitarias básicas

## Criterios de Evaluación

- **Arquitectura (30%)**: Correcta separación de capas y responsabilidades
- **Patrones de Diseño (30%)**: Implementación adecuada de al menos tres patrones
- **Calidad del Código (20%)**: Principios SOLID, bajo acoplamiento, alta cohesión
- **Funcionalidad (10%)**: El sistema cumple con los requisitos funcionales
- **Documentación (10%)**: Claridad en la documentación de diseño y arquitectura

## Consejos para la Implementación

1. Comienza diseñando las entidades principales y sus relaciones
2. Identifica dónde aplicar los patrones de diseño antes de comenzar a codificar
3. Implementa primero la capa de dominio, seguida de la infraestructura
4. Deja la interfaz de usuario para el final
5. Mantén una documentación continua de tus decisiones de diseño

## Ejemplo de Implementación de Patrones

### Patrón State para Estado de Pedidos

```java
// Interfaz Estado
public interface OrderState {
    void processOrder(Order order);
    String getStateName();
}

// Estados concretos
public class ReceivedState implements OrderState {
    @Override
    public void processOrder(Order order) {
        System.out.println("Pedido recibido, enviando a cocina...");
        order.setState(new PreparingState());
    }
    
    @Override
    public String getStateName() {
        return "Recibido";
    }
}

// Clase Order que usa el patrón State
public class Order {
    private OrderState state;
    
    public Order() {
        this.state = new ReceivedState();
    }
    
    public void setState(OrderState state) {
        this.state = state;
    }
    
    public void processOrder() {
        state.processOrder(this);
    }
    
    public String getStatus() {
        return state.getStateName();
    }
}
```

### Patrón Strategy para Descuentos

```java
// Interfaz Estrategia
public interface DiscountStrategy {
    double applyDiscount(double amount);
}

// Estrategias concretas
public class PercentageDiscount implements DiscountStrategy {
    private double percentage;
    
    public PercentageDiscount(double percentage) {
        this.percentage = percentage;
    }
    
    @Override
    public double applyDiscount(double amount) {
        return amount * (1 - percentage/100);
    }
}

// Uso del patrón
public class Order {
    // Otros atributos
    private DiscountStrategy discountStrategy;
    
    public void setDiscountStrategy(DiscountStrategy discountStrategy) {
        this.discountStrategy = discountStrategy;
    }
    
    public double calculateTotal() {
        double subtotal = calculateSubtotal();
        return discountStrategy != null ? 
               discountStrategy.applyDiscount(subtotal) : subtotal;
    }
}
```

## Recursos Adicionales

- [Refactoring Guru - Patrones de Diseño](https://refactoring.guru/es/design-patterns)
- [Clean Architecture - Robert C. Martin](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
- [Domain-Driven Design - Eric Evans](https://domainlanguage.com/ddd/)
- [SOLID Principles Explained](https://www.digitalocean.com/community/conceptual-articles/s-o-l-i-d-the-first-five-principles-of-object-oriented-design)


¡Buena suerte con tu implementación! Recuerda que la clave está en aplicar de manera práctica los conceptos teóricos de arquitectura y diseño que has aprendido.
