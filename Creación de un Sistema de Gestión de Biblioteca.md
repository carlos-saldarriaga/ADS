# Guía Paso a Paso: Creación de un Sistema de Gestión de Biblioteca en Java

Esta guía te llevará a través del proceso de crear un pequeño sistema de gestión de biblioteca en Java que implementa tres tipos de patrones de diseño: creacional, estructural y de comportamiento. Al finalizar, tendrás un proyecto funcional que demuestra la aplicación práctica de estos patrones.

## Introducción

Nuestro proyecto será un sistema de gestión de biblioteca simple donde implementaremos:
- **Patrón Creacional**: Factory Method (para crear diferentes tipos de elementos de biblioteca)
- **Patrón Estructural**: Decorator (para añadir funcionalidades a los elementos de biblioteca)
- **Patrón de Comportamiento**: Observer (para notificar cambios en el sistema)

## Paso 1: Configuración del Proyecto

1. Crea una estructura de carpetas para tu proyecto:
   ```
   BibliotecaApp/
   └── src/
       ├── main/
       │   └── java/
       │       └── biblioteca/
       │           ├── main/
       │           ├── modelo/
       │           ├── creacional/
       │           ├── estructural/
       │           └── comportamiento/
       └── test/ (opcional)
   ```

2. Abre tu IDE favorito (Eclipse, IntelliJ, NetBeans o VS Code) y crea un nuevo proyecto Java.

## Paso 2: Implementación del Modelo Base

Primero, crearemos las clases base que representarán nuestro modelo de dominio.

1. Crea una interfaz `Item` en el paquete `biblioteca.modelo`:

```java
// Item.java
package biblioteca.modelo;

public interface Item {
    String getTitulo();
    String getAutor();
    int getAnoPublicacion();
    String getInformacion();
    boolean isDisponible();
    void prestar();
    void devolver();
}
```

2. Crea una clase abstracta `ElementoBiblioteca` que implemente la interfaz `Item`:

```java
// ElementoBiblioteca.java
package biblioteca.modelo;

public abstract class ElementoBiblioteca implements Item {
    private String titulo;
    private String autor;
    private int anoPublicacion;
    private boolean disponible;

    public ElementoBiblioteca(String titulo, String autor, int anoPublicacion) {
        this.titulo = titulo;
        this.autor = autor;
        this.anoPublicacion = anoPublicacion;
        this.disponible = true;
    }

    @Override
    public String getTitulo() {
        return titulo;
    }

    @Override
    public String getAutor() {
        return autor;
    }

    @Override
    public int getAnoPublicacion() {
        return anoPublicacion;
    }

    @Override
    public boolean isDisponible() {
        return disponible;
    }

    @Override
    public void prestar() {
        if (disponible) {
            disponible = false;
            System.out.println(getTitulo() + " ha sido prestado.");
        } else {
            System.out.println(getTitulo() + " no está disponible para préstamo.");
        }
    }

    @Override
    public void devolver() {
        if (!disponible) {
            disponible = true;
            System.out.println(getTitulo() + " ha sido devuelto.");
        } else {
            System.out.println(getTitulo() + " ya estaba en la biblioteca.");
        }
    }

    @Override
    public abstract String getInformacion();
}
```

3. Crea dos clases concretas que extiendan `ElementoBiblioteca`:

```java
// Libro.java
package biblioteca.modelo;

public class Libro extends ElementoBiblioteca {
    private String isbn;
    private int numeroPaginas;

    public Libro(String titulo, String autor, int anoPublicacion, String isbn, int numeroPaginas) {
        super(titulo, autor, anoPublicacion);
        this.isbn = isbn;
        this.numeroPaginas = numeroPaginas;
    }

    public String getIsbn() {
        return isbn;
    }

    public int getNumeroPaginas() {
        return numeroPaginas;
    }

    @Override
    public String getInformacion() {
        return "Libro: " + getTitulo() + 
               " | Autor: " + getAutor() + 
               " | Año: " + getAnoPublicacion() + 
               " | ISBN: " + isbn + 
               " | Páginas: " + numeroPaginas;
    }
}
```

```java
// Revista.java
package biblioteca.modelo;

public class Revista extends ElementoBiblioteca {
    private String issn;
    private int numero;

    public Revista(String titulo, String autor, int anoPublicacion, String issn, int numero) {
        super(titulo, autor, anoPublicacion);
        this.issn = issn;
        this.numero = numero;
    }

    public String getIssn() {
        return issn;
    }

    public int getNumero() {
        return numero;
    }

    @Override
    public String getInformacion() {
        return "Revista: " + getTitulo() + 
               " | Editor: " + getAutor() + 
               " | Año: " + getAnoPublicacion() + 
               " | ISSN: " + issn + 
               " | Número: " + numero;
    }
}
```

## Paso 3: Implementación del Patrón Creacional (Factory Method)

Ahora implementaremos el patrón Factory Method para crear diferentes tipos de elementos de biblioteca.

1. Crea una interfaz `ItemFactory` en el paquete `biblioteca.creacional`:

```java
// ItemFactory.java
package biblioteca.creacional;

import biblioteca.modelo.Item;

public interface ItemFactory {
    Item crearItem();
}
```

2. Crea implementaciones concretas de la fábrica:

```java
// LibroFactory.java
package biblioteca.creacional;

import biblioteca.modelo.Item;
import biblioteca.modelo.Libro;

public class LibroFactory implements ItemFactory {
    private String titulo;
    private String autor;
    private int anoPublicacion;
    private String isbn;
    private int numeroPaginas;

    public LibroFactory(String titulo, String autor, int anoPublicacion, String isbn, int numeroPaginas) {
        this.titulo = titulo;
        this.autor = autor;
        this.anoPublicacion = anoPublicacion;
        this.isbn = isbn;
        this.numeroPaginas = numeroPaginas;
    }

    @Override
    public Item crearItem() {
        return new Libro(titulo, autor, anoPublicacion, isbn, numeroPaginas);
    }
}
```

```java
// RevistaFactory.java
package biblioteca.creacional;

import biblioteca.modelo.Item;
import biblioteca.modelo.Revista;

public class RevistaFactory implements ItemFactory {
    private String titulo;
    private String editor;
    private int anoPublicacion;
    private String issn;
    private int numero;

    public RevistaFactory(String titulo, String editor, int anoPublicacion, String issn, int numero) {
        this.titulo = titulo;
        this.editor = editor;
        this.anoPublicacion = anoPublicacion;
        this.issn = issn;
        this.numero = numero;
    }

    @Override
    public Item crearItem() {
        return new Revista(titulo, editor, anoPublicacion, issn, numero);
    }
}
```

## Paso 4: Implementación del Patrón Estructural (Decorator)

Implementaremos el patrón Decorator para añadir funcionalidades adicionales a nuestros ítems de biblioteca.

1. Crea una clase abstracta `ItemDecorator` en el paquete `biblioteca.estructural`:

```java
// ItemDecorator.java
package biblioteca.estructural;

import biblioteca.modelo.Item;

public abstract class ItemDecorator implements Item {
    protected Item itemDecorado;

    public ItemDecorator(Item itemDecorado) {
        this.itemDecorado = itemDecorado;
    }

    @Override
    public String getTitulo() {
        return itemDecorado.getTitulo();
    }

    @Override
    public String getAutor() {
        return itemDecorado.getAutor();
    }

    @Override
    public int getAnoPublicacion() {
        return itemDecorado.getAnoPublicacion();
    }

    @Override
    public boolean isDisponible() {
        return itemDecorado.isDisponible();
    }

    @Override
    public void prestar() {
        itemDecorado.prestar();
    }

    @Override
    public void devolver() {
        itemDecorado.devolver();
    }

    @Override
    public String getInformacion() {
        return itemDecorado.getInformacion();
    }
}
```

2. Crea dos implementaciones concretas del decorador:

```java
// ItemDigital.java
package biblioteca.estructural;

import biblioteca.modelo.Item;

public class ItemDigital extends ItemDecorator {
    private String formatoDigital;
    private double tamanoMb;

    public ItemDigital(Item itemDecorado, String formatoDigital, double tamanoMb) {
        super(itemDecorado);
        this.formatoDigital = formatoDigital;
        this.tamanoMb = tamanoMb;
    }

    public String getFormatoDigital() {
        return formatoDigital;
    }

    public double getTamanoMb() {
        return tamanoMb;
    }

    @Override
    public String getInformacion() {
        return super.getInformacion() + " | Formato Digital: " + formatoDigital + " | Tamaño: " + tamanoMb + " MB";
    }

    @Override
    public void prestar() {
        System.out.println("Generando enlace de descarga para " + getTitulo());
        super.prestar();
    }
}
```

```java
// ItemConResena.java
package biblioteca.estructural;

import biblioteca.modelo.Item;
import java.util.ArrayList;
import java.util.List;

public class ItemConResena extends ItemDecorator {
    private List<String> resenas;

    public ItemConResena(Item itemDecorado) {
        super(itemDecorado);
        this.resenas = new ArrayList<>();
    }

    public void agregarResena(String resena) {
        resenas.add(resena);
        System.out.println("Reseña agregada a " + getTitulo());
    }

    public List<String> getResenas() {
        return new ArrayList<>(resenas);
    }

    @Override
    public String getInformacion() {
        StringBuilder info = new StringBuilder(super.getInformacion());
        info.append(" | Reseñas: ").append(resenas.size());
        return info.toString();
    }

    public void mostrarResenas() {
        System.out.println("Reseñas para " + getTitulo() + ":");
        if (resenas.isEmpty()) {
            System.out.println("  No hay reseñas disponibles.");
        } else {
            for (int i = 0; i < resenas.size(); i++) {
                System.out.println("  " + (i + 1) + ". " + resenas.get(i));
            }
        }
    }
}
```

## Paso 5: Implementación del Patrón de Comportamiento (Observer)

Implementaremos el patrón Observer para notificar a los usuarios interesados sobre cambios en los ítems de la biblioteca.

1. Crea las interfaces `Observador` y `Sujeto` en el paquete `biblioteca.comportamiento`:

```java
// Observador.java
package biblioteca.comportamiento;

import biblioteca.modelo.Item;

public interface Observador {
    void actualizar(Item item, String evento);
}
```

```java
// Sujeto.java
package biblioteca.comportamiento;

public interface Sujeto {
    void agregarObservador(Observador observador);
    void eliminarObservador(Observador observador);
    void notificarObservadores(String evento);
}
```

2. Crea una clase `Biblioteca` que implemente `Sujeto`:

```java
// Biblioteca.java
package biblioteca.comportamiento;

import biblioteca.modelo.Item;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class Biblioteca implements Sujeto {
    private List<Observador> observadores;
    private Map<String, Item> inventario;

    public Biblioteca() {
        this.observadores = new ArrayList<>();
        this.inventario = new HashMap<>();
    }

    @Override
    public void agregarObservador(Observador observador) {
        if (!observadores.contains(observador)) {
            observadores.add(observador);
        }
    }

    @Override
    public void eliminarObservador(Observador observador) {
        observadores.remove(observador);
    }

    @Override
    public void notificarObservadores(String evento) {
        for (Observador observador : observadores) {
            observador.actualizar(null, evento);
        }
    }

    public void notificarCambioItem(Item item, String evento) {
        for (Observador observador : observadores) {
            observador.actualizar(item, evento);
        }
    }

    public void agregarItem(Item item) {
        inventario.put(item.getTitulo(), item);
        notificarCambioItem(item, "Nuevo ítem agregado: " + item.getTitulo());
    }

    public Item buscarItem(String titulo) {
        return inventario.get(titulo);
    }

    public boolean prestarItem(String titulo) {
        Item item = buscarItem(titulo);
        if (item != null && item.isDisponible()) {
            item.prestar();
            notificarCambioItem(item, "Ítem prestado: " + titulo);
            return true;
        }
        return false;
    }

    public boolean devolverItem(String titulo) {
        Item item = buscarItem(titulo);
        if (item != null && !item.isDisponible()) {
            item.devolver();
            notificarCambioItem(item, "Ítem devuelto: " + titulo);
            return true;
        }
        return false;
    }

    public List<Item> listarInventario() {
        return new ArrayList<>(inventario.values());
    }
}
```

3. Crea una implementación concreta de `Observador`:

```java
// UsuarioBiblioteca.java
package biblioteca.comportamiento;

import biblioteca.modelo.Item;

public class UsuarioBiblioteca implements Observador {
    private String nombre;

    public UsuarioBiblioteca(String nombre) {
        this.nombre = nombre;
    }

    public String getNombre() {
        return nombre;
    }

    @Override
    public void actualizar(Item item, String evento) {
        if (item != null) {
            System.out.println("[Usuario: " + nombre + "] Notificación: " + evento);
        } else {
            System.out.println("[Usuario: " + nombre + "] Notificación general: " + evento);
        }
    }
}
```

## Paso 6: Creación de la Clase Principal (Main)

Finalmente, crearemos la clase principal para ejecutar y probar nuestro sistema:

```java
// BibliotecaMain.java
package biblioteca.main;

import biblioteca.comportamiento.Biblioteca;
import biblioteca.comportamiento.UsuarioBiblioteca;
import biblioteca.creacional.ItemFactory;
import biblioteca.creacional.LibroFactory;
import biblioteca.creacional.RevistaFactory;
import biblioteca.estructural.ItemConResena;
import biblioteca.estructural.ItemDigital;
import biblioteca.modelo.Item;

import java.util.Scanner;

public class BibliotecaMain {
    private static Biblioteca biblioteca = new Biblioteca();
    private static Scanner scanner = new Scanner(System.in);

    public static void main(String[] args) {
        System.out.println("=== Sistema de Gestión de Biblioteca ===");
        
        // Crear usuarios observadores
        UsuarioBiblioteca usuario1 = new UsuarioBiblioteca("Juan");
        UsuarioBiblioteca usuario2 = new UsuarioBiblioteca("María");
        
        // Registrar observadores
        biblioteca.agregarObservador(usuario1);
        biblioteca.agregarObservador(usuario2);
        
        // Ejemplo de uso de Factory Method
        ItemFactory libroFactory = new LibroFactory(
                "Cien años de soledad", 
                "Gabriel García Márquez", 
                1967, 
                "978-0307474728", 
                417);
        
        ItemFactory revistaFactory = new RevistaFactory(
                "National Geographic", 
                "National Geographic Society", 
                2023, 
                "0027-9358", 
                256);
        
        // Crear ítems usando las fábricas
        Item libro = libroFactory.crearItem();
        Item revista = revistaFactory.crearItem();
        
        // Ejemplo de uso de Decorator
        Item libroDigital = new ItemDigital(libro, "PDF", 8.5);
        Item revistaConResena = new ItemConResena(revista);
        
        // Agregar ítems a la biblioteca
        biblioteca.agregarItem(libro);
        biblioteca.agregarItem(libroDigital);
        biblioteca.agregarItem(revista);
        biblioteca.agregarItem(revistaConResena);
        
        // Ejemplo de agregar reseñas
        if (revistaConResena instanceof ItemConResena) {
            ((ItemConResena) revistaConResena).agregarResena("Excelente edición con fotografías impresionantes.");
            ((ItemConResena) revistaConResena).agregarResena("Artículos muy informativos.");
        }
        
        boolean salir = false;
        while (!salir) {
            mostrarMenu();
            int opcion = obtenerOpcion();
            
            switch (opcion) {
                case 1:
                    listarInventario();
                    break;
                case 2:
                    prestarItem();
                    break;
                case 3:
                    devolverItem();
                    break;
                case 4:
                    verResenas();
                    break;
                case 5:
                    agregarResena();
                    break;
                case 6:
                    salir = true;
                    System.out.println("¡Gracias por usar el Sistema de Gestión de Biblioteca!");
                    break;
                default:
                    System.out.println("Opción no válida. Intente de nuevo.");
            }
        }
        
        scanner.close();
    }
    
    private static void mostrarMenu() {
        System.out.println("\n--- Menú Principal ---");
        System.out.println("1. Listar inventario");
        System.out.println("2. Prestar ítem");
        System.out.println("3. Devolver ítem");
        System.out.println("4. Ver reseñas");
        System.out.println("5. Agregar reseña");
        System.out.println("6. Salir");
        System.out.print("Seleccione una opción: ");
    }
    
    private static int obtenerOpcion() {
        try {
            return Integer.parseInt(scanner.nextLine());
        } catch (NumberFormatException e) {
            return -1;
        }
    }
    
    private static void listarInventario() {
        System.out.println("\n=== Inventario de la Biblioteca ===");
        for (Item item : biblioteca.listarInventario()) {
            System.out.println(item.getInformacion() + " | Disponible: " + (item.isDisponible() ? "Sí" : "No"));
        }
    }
    
    private static void prestarItem() {
        System.out.print("\nIngrese el título del ítem a prestar: ");
        String titulo = scanner.nextLine();
        if (biblioteca.prestarItem(titulo)) {
            System.out.println("Préstamo realizado con éxito.");
        } else {
            System.out.println("No se pudo realizar el préstamo. Verifique el título y disponibilidad.");
        }
    }
    
    private static void devolverItem() {
        System.out.print("\nIngrese el título del ítem a devolver: ");
        String titulo = scanner.nextLine();
        if (biblioteca.devolverItem(titulo)) {
            System.out.println("Devolución realizada con éxito.");
        } else {
            System.out.println("No se pudo realizar la devolución. Verifique el título y estado del ítem.");
        }
    }
    
    private static void verResenas() {
        System.out.print("\nIngrese el título del ítem para ver reseñas: ");
        String titulo = scanner.nextLine();
        Item item = biblioteca.buscarItem(titulo);
        
        if (item instanceof ItemConResena) {
            ((ItemConResena) item).mostrarResenas();
        } else {
            System.out.println("Este ítem no tiene sistema de reseñas.");
        }
    }
    
    private static void agregarResena() {
        System.out.print("\nIngrese el título del ítem para agregar reseña: ");
        String titulo = scanner.nextLine();
        Item item = biblioteca.buscarItem(titulo);
        
        if (item instanceof ItemConResena) {
            System.out.print("Ingrese su reseña: ");
            String resena = scanner.nextLine();
            ((ItemConResena) item).agregarResena(resena);
        } else {
            System.out.println("Este ítem no tiene sistema de reseñas.");
        }
    }
}
```

## Paso 7: Compilación y Ejecución

Para compilar y ejecutar el proyecto:

1. Asegúrate de que todos los archivos estén en la estructura de carpetas correcta.
2. Compila el proyecto con `javac` o usando tu IDE.
3. Ejecuta la clase `BibliotecaMain`.

## Explicación de los Patrones Implementados

En este proyecto, hemos implementado tres patrones de diseño:

1. **Patrón Creacional (Factory Method)**:
   - Permite crear diferentes tipos de ítems (libros y revistas) sin especificar la clase concreta.
   - Delega la responsabilidad de instanciación a las subclases.

2. **Patrón Estructural (Decorator)**:
   - Permite añadir funcionalidades a los ítems de manera dinámica.
   - Hemos implementado `ItemDigital` y `ItemConResena` que extienden la funcionalidad básica.

3. **Patrón de Comportamiento (Observer)**:
   - Permite notificar a múltiples usuarios sobre cambios en la biblioteca.
   - La clase `Biblioteca` actúa como sujeto y `UsuarioBiblioteca` como observador.

## Conclusión

Has creado un sistema de gestión de biblioteca funcional que demuestra tres patrones de diseño diferentes. Este proyecto te ha permitido aplicar conceptos de programación orientada a objetos y patrones de diseño en un contexto práctico.

Puedes extender este proyecto agregando más tipos de ítems, más decoradores o implementando otros patrones de diseño según tus necesidades de aprendizaje.
