# Principios SOLID: Guía Práctica con Ejemplos de Código

Este documento proporciona una explicación detallada de los principios SOLID con ejemplos concretos de código. Los principios SOLID son fundamentales para crear sistemas de software mantenibles, extensibles y robustos.

## 1. Principio de Responsabilidad Única (SRP)

> "Una clase debe tener una y solo una razón para cambiar"

### Ejemplo que viola SRP:

```java
public class Carrito {
    private List<Item> items = new ArrayList<>();
    
    // Responsabilidad 1: Gestión del carrito
    public void agregarItem(Item item) { items.add(item); }
    public void eliminarItem(String productoId) { 
        items.removeIf(item -> item.getProductoId().equals(productoId)); 
    }
    
    // Responsabilidad 2: Cálculo de precios
    public double calcularSubtotal() {
        return items.stream()
                .mapToDouble(item -> item.getPrecio() * item.getCantidad())
                .sum();
    }
    public double calcularImpuestos() {
        return calcularSubtotal() * 0.16;
    }
    
    // Responsabilidad 3: Verificación de inventario
    public boolean verificarDisponibilidad() {
        for (Item item : items) {
            // Consulta directa a la base de datos de inventario
            int disponible = BaseDeDatos.consultarInventario(item.getProductoId());
            if (disponible < item.getCantidad()) {
                return false;
            }
        }
        return true;
    }
    
    // Responsabilidad 4: Procesamiento de pagos
    public boolean procesarPago(DatosTarjeta tarjeta) {
        double total = calcularSubtotal() + calcularImpuestos();
        // Lógica para procesar el pago
        return ProcesadorPagos.realizarCargo(tarjeta, total);
    }
}
```

### Aplicando SRP:

```java
// Responsabilidad 1: Gestión del carrito
public class Carrito {
    private List<Item> items = new ArrayList<>();
    
    public void agregarItem(Item item) { 
        items.add(item); 
    }
    
    public void eliminarItem(String productoId) { 
        items.removeIf(item -> item.getProductoId().equals(productoId)); 
    }
    
    public List<Item> getItems() {
        return new ArrayList<>(items); // Copia defensiva
    }
}

// Responsabilidad 2: Cálculo de precios
public class CalculadorPrecios {
    private final TasaImpuestoService impuestoService;
    
    public CalculadorPrecios(TasaImpuestoService impuestoService) {
        this.impuestoService = impuestoService;
    }
    
    public double calcularSubtotal(Carrito carrito) {
        return carrito.getItems().stream()
                .mapToDouble(item -> item.getPrecio() * item.getCantidad())
                .sum();
    }
    
    public double calcularImpuestos(Carrito carrito) {
        return calcularSubtotal(carrito) * impuestoService.obtenerTasaActual();
    }
    
    public double calcularTotal(Carrito carrito) {
        return calcularSubtotal(carrito) + calcularImpuestos(carrito);
    }
}

// Responsabilidad 3: Verificación de inventario
public class VerificadorInventario {
    private final InventarioRepository inventarioRepository;
    
    public VerificadorInventario(InventarioRepository inventarioRepository) {
        this.inventarioRepository = inventarioRepository;
    }
    
    public boolean verificarDisponibilidad(Carrito carrito) {
        for (Item item : carrito.getItems()) {
            int disponible = inventarioRepository.consultarDisponibilidad(item.getProductoId());
            if (disponible < item.getCantidad()) {
                return false;
            }
        }
        return true;
    }
}

// Responsabilidad 4: Procesamiento de pagos
public class ProcesadorPagos {
    private final PasarelaPagoService pasarelaPago;
    
    public ProcesadorPagos(PasarelaPagoService pasarelaPago) {
        this.pasarelaPago = pasarelaPago;
    }
    
    public ResultadoPago procesarPago(Carrito carrito, DatosTarjeta tarjeta, 
                                      CalculadorPrecios calculador) {
        double total = calculador.calcularTotal(carrito);
        return pasarelaPago.realizarCargo(tarjeta, total);
    }
}
```

## 2. Principio Abierto/Cerrado (OCP)

> "Las entidades de software deben estar abiertas para la extensión pero cerradas para la modificación"

### Ejemplo que viola OCP:

```java
public class CalculadorDescuentos {
    
    public double calcularDescuento(Producto producto, String tipoDescuento) {
        if (tipoDescuento.equals("NUEVO_CLIENTE")) {
            return producto.getPrecio() * 0.15; // 15% de descuento
        } 
        else if (tipoDescuento.equals("CLIENTE_FRECUENTE")) {
            return producto.getPrecio() * 0.10; // 10% de descuento
        }
        else if (tipoDescuento.equals("PROMOCION_TEMPORADA")) {
            return producto.getPrecio() * 0.20; // 20% de descuento
        }
        // Si agregamos un nuevo tipo de descuento, debemos modificar esta clase
        return 0;
    }
}
```

### Aplicando OCP:

```java
// Interfaz para estrategias de descuento
public interface EstrategiaDescuento {
    double calcularDescuento(Producto producto);
}

// Implementaciones concretas
public class DescuentoNuevoCliente implements EstrategiaDescuento {
    @Override
    public double calcularDescuento(Producto producto) {
        return producto.getPrecio() * 0.15; // 15% de descuento
    }
}

public class DescuentoClienteFrecuente implements EstrategiaDescuento {
    @Override
    public double calcularDescuento(Producto producto) {
        return producto.getPrecio() * 0.10; // 10% de descuento
    }
}

public class DescuentoPromocionTemporada implements EstrategiaDescuento {
    @Override
    public double calcularDescuento(Producto producto) {
        return producto.getPrecio() * 0.20; // 20% de descuento
    }
}

// Podemos agregar nuevas estrategias sin modificar el calculador
public class DescuentoCumpleaños implements EstrategiaDescuento {
    @Override
    public double calcularDescuento(Producto producto) {
        return producto.getPrecio() * 0.25; // 25% de descuento
    }
}

// Calculador que usa las estrategias
public class CalculadorDescuentos {
    private Map<String, EstrategiaDescuento> estrategias = new HashMap<>();
    
    public CalculadorDescuentos() {
        // Registramos las estrategias disponibles
        estrategias.put("NUEVO_CLIENTE", new DescuentoNuevoCliente());
        estrategias.put("CLIENTE_FRECUENTE", new DescuentoClienteFrecuente());
        estrategias.put("PROMOCION_TEMPORADA", new DescuentoPromocionTemporada());
        estrategias.put("CUMPLEAÑOS", new DescuentoCumpleaños());
    }
    
    public void registrarEstrategia(String tipo, EstrategiaDescuento estrategia) {
        estrategias.put(tipo, estrategia);
    }
    
    public double calcularDescuento(Producto producto, String tipoDescuento) {
        EstrategiaDescuento estrategia = estrategias.get(tipoDescuento);
        if (estrategia != null) {
            return estrategia.calcularDescuento(producto);
        }
        return 0;
    }
}
```

## 3. Principio de Sustitución de Liskov (LSP)

> "Los objetos de una superclase deben poder ser reemplazados por objetos de sus subclases sin afectar la funcionalidad"

### Ejemplo que viola LSP:

```java
public class Rectangulo {
    protected int ancho;
    protected int alto;
    
    public void setAncho(int ancho) {
        this.ancho = ancho;
    }
    
    public void setAlto(int alto) {
        this.alto = alto;
    }
    
    public int getAncho() {
        return ancho;
    }
    
    public int getAlto() {
        return alto;
    }
    
    public int getArea() {
        return ancho * alto;
    }
}

// Esta subclase viola LSP porque modifica el comportamiento esperado
public class Cuadrado extends Rectangulo {
    @Override
    public void setAncho(int ancho) {
        this.ancho = ancho;
        this.alto = ancho; // Un cuadrado debe tener lados iguales
    }
    
    @Override
    public void setAlto(int alto) {
        this.alto = alto;
        this.ancho = alto; // Un cuadrado debe tener lados iguales
    }
}

// Este código fallará para un Cuadrado
public void probarRectangulo(Rectangulo r) {
    r.setAncho(5);
    r.setAlto(4);
    // Esperamos que el área sea 20, pero para un Cuadrado será 16
    assert r.getArea() == 20;
}
```

### Aplicando LSP:

```java
// Interfaz común para formas
public interface Forma {
    int getArea();
}

// Implementaciones específicas
public class Rectangulo implements Forma {
    private int ancho;
    private int alto;
    
    public Rectangulo(int ancho, int alto) {
        this.ancho = ancho;
        this.alto = alto;
    }
    
    public void setAncho(int ancho) {
        this.ancho = ancho;
    }
    
    public void setAlto(int alto) {
        this.alto = alto;
    }
    
    public int getAncho() {
        return ancho;
    }
    
    public int getAlto() {
        return alto;
    }
    
    @Override
    public int getArea() {
        return ancho * alto;
    }
}

public class Cuadrado implements Forma {
    private int lado;
    
    public Cuadrado(int lado) {
        this.lado = lado;
    }
    
    public void setLado(int lado) {
        this.lado = lado;
    }
    
    public int getLado() {
        return lado;
    }
    
    @Override
    public int getArea() {
        return lado * lado;
    }
}

// Ahora podemos usar ambas formas polimórficamente
public void probarFormas(Forma forma) {
    // Solo usamos el método común definido en la interfaz
    int area = forma.getArea();
    // Operamos con el área sin importar qué tipo específico de forma sea
}
```

## 4. Principio de Segregación de Interfaces (ISP)

> "Ningún cliente debe ser forzado a depender de interfaces que no utiliza"

### Ejemplo que viola ISP:

```java
// Interfaz demasiado grande con métodos que no todos los clientes necesitan
public interface GestorProductos {
    void agregarProducto(Producto producto);
    void eliminarProducto(String id);
    Producto buscarProducto(String id);
    List<Producto> listarProductos();
    void actualizarPrecio(String id, double nuevoPrecio);
    void actualizarStock(String id, int nuevoStock);
    byte[] generarReporteInventario();
    void enviarAlertaStockBajo(String id);
    void cargarImagenesProducto(String id, List<byte[]> imagenes);
    List<Producto> buscarPorCategoria(String categoria);
    void aplicarDescuento(String id, double porcentaje);
}

// Una implementación debe implementar todos los métodos, incluso si no los necesita
public class GestorProductosImpl implements GestorProductos {
    // Implementaciones de todos los métodos...
}
```

### Aplicando ISP:

```java
// Interfaces más pequeñas y específicas
public interface GestorCatalogoProductos {
    void agregarProducto(Producto producto);
    void eliminarProducto(String id);
    Producto buscarProducto(String id);
    List<Producto> listarProductos();
    List<Producto> buscarPorCategoria(String categoria);
}

public interface GestorInventario {
    void actualizarStock(String id, int nuevoStock);
    int consultarStock(String id);
    void enviarAlertaStockBajo(String id);
}

public interface GestorPrecios {
    void actualizarPrecio(String id, double nuevoPrecio);
    void aplicarDescuento(String id, double porcentaje);
}

public interface GestorMultimedia {
    void cargarImagenesProducto(String id, List<byte[]> imagenes);
    List<byte[]> obtenerImagenesProducto(String id);
}

public interface GeneradorReportes {
    byte[] generarReporteInventario();
    byte[] generarReporteVentas(Date inicio, Date fin);
}

// Ahora las implementaciones solo implementan lo que necesitan
public class CatalogoProductosBasico implements GestorCatalogoProductos {
    // Solo implementa métodos de catálogo
}

public class InventarioAvanzado implements GestorInventario, GeneradorReportes {
    // Implementa gestión de inventario y generación de reportes
}

public class GestorEcommerce implements GestorCatalogoProductos, GestorPrecios, GestorInventario {
    // Implementa solo lo que necesita la aplicación de e-commerce
}
```

## 5. Principio de Inversión de Dependencias (DIP)

> "Los módulos de alto nivel no deben depender de módulos de bajo nivel. Ambos deben depender de abstracciones."
> "Las abstracciones no deben depender de detalles. Los detalles deben depender de abstracciones."

### Ejemplo que viola DIP:

```java
// Clase de bajo nivel con implementación específica
public class RepositorioProductosMongoDB {
    private MongoClient mongoClient;
    
    public RepositorioProductosMongoDB() {
        this.mongoClient = new MongoClient("localhost", 27017);
    }
    
    public void guardarProducto(Producto producto) {
        // Implementación específica para MongoDB
    }
    
    public Producto buscarProducto(String id) {
        // Implementación específica para MongoDB
        return null;
    }
}

// Clase de alto nivel que depende directamente de la implementación de bajo nivel
public class ServicioProductos {
    private RepositorioProductosMongoDB repositorio;
    
    public ServicioProductos() {
        this.repositorio = new RepositorioProductosMongoDB();
    }
    
    public void crearProducto(Producto producto) {
        // Validaciones de negocio
        repositorio.guardarProducto(producto);
    }
    
    public Producto obtenerProducto(String id) {
        return repositorio.buscarProducto(id);
    }
}
```

### Aplicando DIP:

```java
// Abstracción (interfaz)
public interface RepositorioProductos {
    void guardarProducto(Producto producto);
    Producto buscarProducto(String id);
    List<Producto> listarProductos();
    void eliminarProducto(String id);
}

// Implementación concreta que depende de la abstracción
public class RepositorioProductosMongoDB implements RepositorioProductos {
    private MongoClient mongoClient;
    
    public RepositorioProductosMongoDB(String host, int puerto) {
        this.mongoClient = new MongoClient(host, puerto);
    }
    
    @Override
    public void guardarProducto(Producto producto) {
        // Implementación específica para MongoDB
    }
    
    @Override
    public Producto buscarProducto(String id) {
        // Implementación específica para MongoDB
        return null;
    }
    
    @Override
    public List<Producto> listarProductos() {
        // Implementación específica para MongoDB
        return null;
    }
    
    @Override
    public void eliminarProducto(String id) {
        // Implementación específica para MongoDB
    }
}

// Implementación alternativa
public class RepositorioProductosMySQL implements RepositorioProductos {
    private Connection connection;
    
    public RepositorioProductosMySQL(String connectionString) {
        // Inicializar conexión a MySQL
    }
    
    // Implementaciones para MySQL
    @Override
    public void guardarProducto(Producto producto) {
        // Implementación específica para MySQL
    }
    
    // Otras implementaciones...
}

// Clase de alto nivel que depende de la abstracción
public class ServicioProductos {
    private final RepositorioProductos repositorio;
    
    // Inyección de dependencia mediante constructor
    public ServicioProductos(RepositorioProductos repositorio) {
        this.repositorio = repositorio;
    }
    
    public void crearProducto(Producto producto) {
        // Validaciones de negocio
        repositorio.guardarProducto(producto);
    }
    
    public Producto obtenerProducto(String id) {
        return repositorio.buscarProducto(id);
    }
}

// Configuración de la aplicación
public class ConfiguracionApp {
    public static void main(String[] args) {
        // Podemos cambiar la implementación sin afectar ServicioProductos
        RepositorioProductos repositorio = new RepositorioProductosMongoDB("localhost", 27017);
        // Alternativa: RepositorioProductos repositorio = new RepositorioProductosMySQL("jdbc:mysql://localhost:3306/productos");
        
        ServicioProductos servicio = new ServicioProductos(repositorio);
        
        // Usar el servicio...
    }
}
```

## Conclusión

Los principios SOLID proporcionan una base sólida para el diseño de software orientado a objetos. Aplicarlos correctamente resulta en sistemas más:

- **Mantenibles**: Facilita los cambios y actualizaciones
- **Extensibles**: Permite agregar nuevas funcionalidades sin modificar código existente
- **Robustos**: Minimiza los errores causados por cambios
- **Testables**: Facilita la escritura de pruebas unitarias
- **Reutilizables**: Promueve componentes que pueden usarse en diferentes contextos

Al descomponer sistemas en componentes que siguen estos principios, se logra un diseño más modular y de mayor calidad.
