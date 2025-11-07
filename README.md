import java.util.ArrayList;
import java.util.Scanner;

// Clase base para los materiales de la biblioteca (libros, revistas, etc.)
class MaterialBibliografico {
    // Los atributos se declaran como 'protected' para que no sean accesibles directamente
    // desde fuera de la clase, solo por sus clases hijas.
    protected String titulo;
    protected String autor;
    protected boolean disponible = true; // por defecto está disponible

    // Constructor
    public MaterialBibliografico(String titulo, String autor) {
        this.titulo = titulo;
        this.autor = autor;
    }

    // Métodos para obtener la info
    public String getTitulo() { return titulo; }
    public String getAutor() { return autor; }
    public boolean isDisponible() { return disponible; }

    // Marca el material como prestado
    public void prestar() {
        disponible = false;
    }

    // Marca el material como devuelto
    public void devolver() {
        disponible = true;
    }
    
    
    //Polimorfismo = mismo nombre de método, diferentes comportamientos.
    // Muestra los datos del material
    @Override
    public String toString() {
        return "Título: " + titulo + " | Autor: " + autor + " | Disponible: " + (disponible ? "Sí" : "No");
    }
}
// La clase 'Libro' hereda de 'MaterialBibliografico'.
// HERENCIA: Libro obtiene todos los atributos y métodos de la clase padre.
// POLIMORFISMO: La clase Libro redefine el método 'toString()' con un comportamiento propio.

class Libro extends MaterialBibliografico {
    private String genero;

    public Libro(String titulo, String autor, String genero) {
        super(titulo, autor);
        this.genero = genero;
    }
    // POLIMORFISMO:
    // Se sobrescribe el método toString() para agregar el género del libro 
    public String getGenero() { return genero; }

    @Override
    public String toString() {
        return super.toString() + " | Género: " + genero;
    }
}
//CLASE BIBLIOTECA: ABSTRACCIÓN Y USO DE OBJETOS
// Clase principal con el menú y las opciones
class Biblioteca {
    private final ArrayList<MaterialBibliografico> inventario = new ArrayList<>();
    private final Scanner sc = new Scanner(System.in);

    // Constructor: agrega algunos libros de ejemplo
    public Biblioteca() {
        inventario.add(new Libro("Cien Años de Soledad", "Gabriel García Márquez", "Realismo Mágico"));
        inventario.add(new Libro("Don Quijote de la Mancha", "Miguel de Cervantes", "Aventura"));
        inventario.add(new Libro("El Principito", "Antoine de Saint-Exupéry", "Fábula"));
    }

    public void menu() {
        int opcion;
        do {
            System.out.println("\n===== MENÚ BIBLIOTECA =====");
            System.out.println("1. Inventario completo");
            System.out.println("2. Préstamo de libro");
            System.out.println("3. Devolución de libro");
            System.out.println("4. Calcular multa");
            System.out.println("5. Listado de libros");
            System.out.println("6. Salir");
            System.out.print("Seleccione una opción: ");
            opcion = sc.nextInt();
            sc.nextLine(); // limpiar el buffer

            switch (opcion) {
                case 1 -> mostrarInventario();
                case 2 -> realizarPrestamo();
                case 3 -> realizarDevolucion();
                case 4 -> calcularMulta();
                case 5 -> mostrarListadoLibros();
                case 6 -> System.out.println("Saliendo del sistema...");
                default -> System.out.println("Opción no válida.");
            }
        } while (opcion != 6);
    }

    // Muestra todos los libros del inventario con detalles
    public void mostrarInventario() {
        if (inventario.isEmpty()) {
            System.out.println("\nNo hay libros en el inventario. Agreguemos uno:");
            agregarLibro();
        } else {
        
            System.out.println("\n--- Inventario de la Biblioteca ---");

             //POLIMORFISMO

            // al llamar toString() en cada objeto, se ejecuta el método correspondiente
            for (int i = 0; i < inventario.size(); i++) {
                System.out.println((i + 1) + ". " + inventario.get(i));
            }
        }
    }

    // Nuevo método: muestra solo título y autor
    public void mostrarListadoLibros() {
        if (inventario.isEmpty()) {
            System.out.println("\nNo hay libros registrados aún.");
        } else {
            System.out.println("\n--- Listado de Libros ---");
            for (int i = 0; i < inventario.size(); i++) {
                MaterialBibliografico libro = inventario.get(i);
                System.out.println((i + 1) + ". " + libro.getTitulo() + " - " + libro.getAutor());
            }
        }
    }

    // Permite agregar un nuevo libro
    public void agregarLibro() {
        System.out.print("Título: ");
        String titulo = sc.nextLine();
        System.out.print("Autor: ");
        String autor = sc.nextLine();
        System.out.print("Género: ");
        String genero = sc.nextLine();

        inventario.add(new Libro(titulo, autor, genero));
        System.out.println("Libro agregado correctamente.");
    }

    // Hace el préstamo de un libro
    public void realizarPrestamo() {
        if (inventario.isEmpty()) {
            System.out.println("No hay libros disponibles para préstamo.");
            return;
        }

        mostrarInventario();
        System.out.print("Ingrese el número del libro a prestar: ");
        int indice = sc.nextInt() - 1;

        if (indice >= 0 && indice < inventario.size()) {
            MaterialBibliografico libro = inventario.get(indice);
            if (libro.isDisponible()) {
                libro.prestar();
                System.out.println("Libro prestado con éxito.");
            } else {
                System.out.println("Ese libro ya está prestado.");
            }
        } else {
            System.out.println("Selección inválida.");
        }
    }

    // Permite devolver un libro
    public void realizarDevolucion() {
        mostrarInventario();
        System.out.print("Ingrese el número del libro a devolver: ");
        int indice = sc.nextInt() - 1;

        if (indice >= 0 && indice < inventario.size()) {
            MaterialBibliografico libro = inventario.get(indice);
            if (!libro.isDisponible()) {
                libro.devolver();
                System.out.println("Libro devuelto correctamente.");
            } else {
                System.out.println("Ese libro ya estaba disponible.");
            }
        } else {
            System.out.println("Selección inválida.");
        }
    }

    // Calcula la multa según los días de retraso
    public void calcularMulta() {
        System.out.print("Ingrese el número de días de retraso: ");
        int dias = sc.nextInt();
        double multaPorDia = 500; // valor fijo por día
        double total = dias * multaPorDia;

        if (dias <= 0) {
            System.out.println("No hay multa.");
        } else {
            System.out.println("Multa total: $" + total);
        }
    }
}

// Clase principal (punto de entrada del programa)
public class BibliotecaApp {
    public static void main(String[] args) {
        Biblioteca biblioteca = new Biblioteca();
        biblioteca.menu(); // inicia el programa
    }
}
