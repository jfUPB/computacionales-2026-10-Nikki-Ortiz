Nota del profesor: sin evidencia en la fase de aplicación

# Unidad 3

## Bitácora de proceso de aprendizaje
### ACTIVIDAD 01

**Q: ¿Para qué sirven los breakpoints?**
A: Sirven para correr el programa paso a paso y detenerse en una instrucción para trabajar de forma detallada y ver lo que esta pasando dentro del programa el funcionamiento de manera clara

**Q: ¿Para qué se usa la ventana de depuración Autos?**
A: Te deja ver qué valores tienen tus variables en ese momento del programa, sin que tú tengas que agregarlas manualmente.

### ACTIVIDAD 02


``` C++
#include <iostream>

using namespace std;

// Función que intercambia los valores que tienen a y b
void swapPorValor(int a, int b) {

    int c = b;
    b = a;
    a = c;    
}

// Función que modifica el parámetro pasado por referencia
void swapPorReferencia(int& a, int& b) { // N se vuelve una referencia a b
    int c = b;
    b = a;
    a = c;
}

// Función que modifica el parámetro utilizando punteros
void swapPorPuntero(int* a, int* b) {
    int c = *b;
    *b = *a;
    *a = c;
}

int main() {
    int a = 10;
    int b = 30;
    
    swapPorValor(a, b);
    cout << "a: " << a << endl;
    cout << "b: " << a << endl;

    return 0;
}
```

para ejecutar valor por puntero hay que hacer un cambio en las lineas antes del return 

``` C++
{
swapPorPuntero(&a, &b);
    cout << "a: " << a << endl;
    cout << "b: " << a << endl;
    return 0;
}
```
---
### ACTIVIDAD 3
Como no se tienen las preguntas, se hará el mapa de memoría y se explicará que se aprendió de la actividad.

```c++
+-----------------------------------------------------------------------+
|                          Segmento de código                           |
|                    (instrucciones y funciones)                        |
|                                                                       |
| - Funciones: main, suma, funcionConStatic, crearArrayHeap             |
| - Texto fijo: "Hola, memoria de solo lectura"                        |
+-----------------------------------------------------------------------+
|                    Variables globales y estáticas                     |
|                                                                       |
| - global_inicializada                                             |
| - global_no_inicializada                                              |
| - var_estatica (dentro de funcionConStatic)                           |
+-----------------------------------------------------------------------+
|                                 Heap                                  | 
|                                                                       |
| - El bloque de memoria para 10 enteros (new int[tam])                 |
| - Los valores guardados: 0, 1, 2, 3, 4, 5, 6, 7, 8, 9                 |
|                                                                       |
+-----------------------------------------------------------------------+
|                                 Stack                                 | 
|                                                                       |
| - De main: a, b, c, tamArray, arrayHeap (el puntero)                  |
| - De suma: a, b, c                                                    |
| - De crearArrayHeap: tam, arr (el puntero)                            |
+-----------------------------------------------------------------------+
```
> NOTA: Preguntar para entender bien si el mapa está bien hecho.

**EXPLICACIÓN**

> NOTA: Para entender bien los conceptos (y aunque suene absurdo, sorry) voy a comparar las partes del mapa con un chef preparando una receta 

- Segmento de Código (Sería como el Libro de Recetas): Es el manual que dice paso a paso qué hacer. El chef puede leerlo, pero no puede tachar ni cambiar las instrucciones mientras cocina. Aquí están tus funciones (los pasos de la receta) y los textos fijos que nunca cambian.

- Variables Globales y Estáticas (Sería como la Despensa): Son ingredientes que están ahí desde que la cocina abre hasta que cierra. Todos los cocineros pueden verlos y usarlos en cualquier momento. No se tiran a la basura hasta que se termina la jornada de trabajo.

- Heap (Sería como el Buffet o el Menú): Es como cuando un cliente pide un plato especial que requiere una mesa nueva. El chef tiene que "pedir permiso" para usar ese espacio (new). Lo más importante: el chef debe acordarse de limpiar esa mesa cuando el cliente termine (delete), porque si no, se quedaría sin espacio para nuevos clientes.

- Stack (Sería como la Tabla de Picar): Es el espacio de trabajo inmediato. Cuando el chef corta una cebolla, usa ese espacio un momento y, en cuanto termina ese paso, limpia la tabla para lo siguiente. Es automático y rápido, pero el espacio es pequeño. Si intenta picar 100 sacos de papas a la vez en esa tablita, se desborda (Stack Overflow).

---  

### ACTIVIDAD 4

<details>

<summary>Experimento 1</summary>

### Modificar el segmento de texto:

```c++
   #include <iostream>
#include <cstdlib>

using namespace std;


int main() {
    // Variable local (stack)
    int a = 10;
    int b = 20;

    /**********************************************************
        EXPERIMENTO 1
    ***********************************************************/

    void* ptr = reinterpret_cast<void*>(&main);
    cout << "Voy a modificar la memoria en la dirección: " << ptr << endl;
    *reinterpret_cast<int*>(ptr) = 0;

    /********************************************************/

    return 0;
}
```
**EXPLICACIÓN DE LO QUE SE ENTENDIÓ DEL CÓDIGO:**
(paso a paso)  

- void* ptr = reinterpret_cast<void*>(&main); --> Aquí se obtiene la dirección de memoria donde empieza la función main. Como vimos en el mapa de memoria, las funciones viven en el Segmento de Código (Text). El "reinterpret_cast" le dice al programa: "Olvida que esto es una función, trátalo como una dirección de memoria cualquiera".

- cout << "Voy a modificar..." << ptr << endl; --> Simplemente imprime la ubicación exacta en la memoria (por ejemplo: 0x401010) donde están guardadas las instrucciones binarias de tu programa.
- *reinterpret_cast<int*>(ptr) = 0; --> Este es el momento del "atentado" [profe yo lo estoy escribiendo así para entender, yo se que da risa :´)] porque se está intentando escribir un 0 encima de la primera instrucción de la función main. Es decir, se está intentando modificar el Segmento de Código en tiempo de ejecución.

- ¿Qué ocurre? El programa se cerrará abruptamente (se "crashea") 
- ¿Por qué ocurre? Ocurre por dos razones principales, una de Hardware y otra de Software: La primera es porque como se mencionó en la descripción del mapa de memoria, el Segmento de Código se marca como "Solo Lectura" en la tabla de páginas de la memoria gestionada por el Sistema Operativo. la segunda es que cuando el procesador intenta ejecutar la instrucción de escribir (= 0) en esa dirección, la Unidad de Gestión de Memoria (MMU) detecta que esa zona está protegida. El procesador lanza una alerta y el Sistema Operativo, para proteger la integridad de la computadora, "mata" tu programa inmediatamente.
</details>


<details>
<summary>Experimento 2</summary>

### Modificar el segmento de datos (constante global):

```c++
#include <iostream>
#include <cstdlib>

using namespace std;

// Constante global
const char* const mensaje_ro = "Hola, memoria de solo lectura";


int main() {
    // Variable local (stack)
    int a = 10;
    int b = 20;


    /**********************************************************
        EXPERIMENTO 2
    ***********************************************************/

    char* ptr = (char*)&mensaje_ro;
    cout << "Voy a modificar la memoria en la dirección: " << ptr << endl;
    *ptr = 0;

    /********************************************************/

    return 0;
}
```
**EXPLICACIÓN DE LO QUE SE ENTENDIÓ DEL CÓDIGO:**
(paso a paso)  

- const char* const mensaje_ro = "..." --> Se ha creado una constante doblemente protegida. No solo el texto es constante, sino que el "puntero" (la dirección que apunta al texto) también lo es. En nuestro mapa de memoria, esto vive en la zona de Solo Lectura del segmento de datos.

- char* ptr = (char*)&mensaje_ro; --> Se usa un puntero normal (char*) para apuntar a algo que es constante.
- *ptr = 0; --> Se intenta escribir un cero en esa dirección.

¿Qué ocurre? Al igual que en el experimento anterior, el programa se detendrá violentamente.
¿Por qué ocurre? Cuando se compila el programa, el sistema identifica a "mensaje_ro" como algo que jamás debe cambiar. Por lo tanto, lo guarda en una sección de la memoria llamada ".rodata (Read-Only Data)." Cuando el programa se carga en la memoria RAM, el Sistema Operativo marca esas páginas de memoria con un permiso de "Solo Lectura" y aunque en el código C++ se "engañó" al compilador usando un puntero "char*" para saltarte las reglas del lenguaje, no puedes engañae al Hardware, por lo que, cuando el procesador intenta ejecutar la orden de escribir en esa dirección, la unidad de control de memori envía una señal de error y el Sistema Operativo mata el proceso para evitar que la memoria se corrompa.
</details>

<details>
<summary>Experimento 3</summary>

### Modificar el segmento de datos (variables globales):

```c++
#include <iostream>
#include <cstdlib>

using namespace std;

// Variables globales
int global_inicializada = 42;
int global_no_inicializada;


int main() {
    // Variable local (stack)
    int a = 10;
    int b = 20;

    /**********************************************************
        EXPERIMENTO 3
    ***********************************************************/

    cout << "global_inicializada: " << global_inicializada << endl;
    cout << "global_no_inicializada: " << global_no_inicializada << endl;


    global_inicializada = 69;
    global_no_inicializada = 666;

    cout << "global_inicializada: " << global_inicializada << endl;
    cout << "global_no_inicializada: " << global_no_inicializada << endl;

    /********************************************************/

    return 0;
}
```
**EXPLICACIÓN DE LO QUE SE ENTENDIÓ DEL CÓDIGO:**
(paso a paso)  

- int global_inicializada = 42; y int global_no_inicializada; --> Estas variables se crean fuera de cualquier función. En el mapa de memoria, se ubican en el Segmento de Datos. A diferencia del segmento de código o las constantes, esta zona está marcada con permisos de Lectura y Escritura (Read/Write).

global_inicializada = 69; --> Aquí el programa busca la dirección de memoria de esa variable global y sobrescribe el 42 con un 69. El Sistema Operativo lo permite porque esa zona de memoria fue diseñada precisamente para guardar datos que cambian durante la vida del programa.

global_no_inicializada = 666; --> Lo mismo ocurre aquí y el programa le asigna el valor 666 sin ningún problema 

¿Qué ocurre? El programa se ejecuta normalmente, primero imprime: global_inicializada: 42 y global_no_inicializada: 0. Luego, después de modificarlas, imprime: global_inicializada: 69 y global_no_inicializada: 666, y aca no ocurre ningun error

¿Por qué ocurre? Esto ocurre porque las variables globales sí pueden modificarse durante la ejecución del programa, ya que ambas están en la zona de datos globales, que es una región de memoria escribible, por eso se pueden cambiar sin problema, "global_inicializada" empieza en 42 porque fue declarada con un valor inicial y "global_no_inicializada" aparece como 0 porque las variables globales que no se inicializan explícitamente se inicializan automáticamente en 0.
</details>

    
<details>
<summary>Experimento 4</summary>

### Modificar la variable local estática de una función por fuera de ella:

```c++
#include <iostream>
#include <cstdlib>

using namespace std;

// Función de ejemplo que muestra la dirección de su variable local estática
void funcionConStatic() {
    static int var_estatica = 100;
    cout << "Dirección de var_estatica (static): " << &var_estatica << endl;
}


int main() {
    // Variable local (stack)
    int a = 10;
    int b = 20;

    /**********************************************************
        EXPERIMENTO 4
    ***********************************************************/

    var_estatica = 42;

    cout << "var_estatica: " << var_estatica << endl;

    /********************************************************/
    return 0;
}
```
**EXPLICACIÓN DE LO QUE SE ENTENDIÓ DEL CÓDIGO:**

¿Qué ocurre? El programa directamente no compila. El compilador muestra un error diciendo que var_estatica no está declarada en este ámbito.
¿Por qué ocurre?  Porque var_estatica fue declarada dentro de la función funcionConStatic(), aunque sea static (lo que significa que vive durante toda la ejecución del programa), su alcance sigue siendo local a esa función.

¿Qué pasa con las variables cada que entras y sales de la función? Las variables locales normales (no estáticas) se crean cada vez que entras a la función y se destruyen cuando sales de ella
¿Qué pasa con las variables locales estáticas? Las variables locales estáticas se crean una sola vez y conservan su valor entre llamadas a la función
</details>


<details>
<summary>Experimento 5</summary>

### Variables locales estática vs no estática:
```c++
#include <iostream>
#include <cstdlib>

using namespace std;

// Función de ejemplo que muestra la dirección de su variable local estática
void funcionConStatic() {
    static int var_estatica = 100;
    cout << "var_estatica: " << var_estatica << endl;
    var_estatica++;
}

void funcionSinStatic() {
    int var_no_estatica = 100;
    cout << "var_no_estatica: " << var_no_estatica << endl;
    var_no_estatica++;
}


int main() {
    // Variable local (stack)
    int a = 10;
    int b = 20;

    /**********************************************************
        EXPERIMENTO 5
    ***********************************************************/

    for (int i = 0; i < 5; i++) {
        cout << "Iteración " << i << endl;
        funcionSinStatic();
        funcionConStatic();
    }

    /********************************************************/

    return 0;
}
```
**EXPLICACIÓN DE LO QUE SE ENTENDIÓ DEL CÓDIGO:**

¿Qué ocurre? Cada vez que se imprime la funcion "funcionSinStatic()" siempre da el valor de 100 mientras que "funcionConStatic()" cada vez que se itera se le está sumando +1
¿Por qué ocurre? Ya que "var_no_estatica" se crea desde cero cada vez que entras a "funcionSinStatic()", entonces siempre empieza en 100, la incrementas, pero al salir de la función se pierde ese cambio. Mientras que, "var_estatica" se crea una sola vez, la primera vez que llamas "funcionConStatic()" y luego recuerda su valor, así que cada vez que vuelves a entrar, continúa desde donde quedó.

¿Ves alguna diferencia entre locales estáticas y no estáticas? Las locales no estática se “reinicia” en cada llamada y las locales estática conserva su valor entre llamadas.
</details>


<details>
<summary>Experimento 6</summary>

### Modificar el segmento de heap:
```c++
#include <iostream>
using namespace std;

int main() {
    // Tamaño del arreglo dinámico
    int tam = 5;

    // Asignar memoria en el Heap para un arreglo de enteros
    int* arrayHeap = new int[tam];

    // Inicializar y mostrar los valores y direcciones de memoria
    for (int i = 0; i < tam; i++) {
        arrayHeap[i] = (i + 1) * 10;
        cout << "arrayHeap[" << i << "] = " << arrayHeap[i]
            << " en dirección " << (arrayHeap + i) << endl;
    }

    // Liberar la memoria asignada en el Heap
    delete[] arrayHeap;

    /**********************************************************
        EXPERIMENTO 6
    ***********************************************************/

    cout << arrayHeap[0] << endl;


    /********************************************************/


    return 0;
}
```
**EXPLICACIÓN DE LO QUE SE ENTENDIÓ DEL CÓDIGO:**

El código suelta error porque se intenta usar un puntero que no tiene contenido ya que inmediatamente antes fue eliminado
¿Qué diferencias notas entre el comportamiento y la gestión del Heap en comparación con el Stack? En el Stack las variables se crean y se borran solas al entrar/salir de funciones (automático, rápido). Mientras que, en el Heap tú pides memoria con new y tú mismo debes devolverla con delete (manual). Si la liberas y la vuelves a usar, pasan errores como este.

¿Qué consecuencias tendría no liberar la memoria reservada con new? Se produce una fuga de memoria, provocando que el software consuma cada vez más recursos con el tiempo. Esto suele derivar en un rendimiento lento, inestabilidad o el bloqueo del sistema

¿Por qué es importante usar delete[] al liberar memoria asignada para un arreglo? Porque new int[tam] reserva un arreglo (varios elementos), delete[] le dice al sistema: “voy a liberar todo el arreglo correctamente”. Si usas delete en vez de delete[], estás liberando “como si fuera un solo elemento” y no todo el conjunto
</details>

---   

### ACTIVIDAD 5

**Q: Explica qué ocurre al copiar un objeto en C++ y en C#. ¿Qué diferencias encuentras?**

A: Al copiar un objeto en C++ se crea una nueva direccion con el mismo contenido del de la original dentro del stack permitiendo su uso independiente teniendo como base el contenido de la original. En cambio, si copias un objeto en C#, se crea un puntero con la direccion del objeto creado en el stack y este se guarda en el heap, el cual guarda la informacion que contiene original (el puntero), o sea la direccion del objeto y no el contenido de dicha direccion, resultando en 2 punteros hacia el mismo objeto.

**Q: ¿Qué es copia en C++ y en C#?**
A: Para el caso de C++ una copia es exactamente lo que su nombre lo indica. Por otro lado, en C# es un nuevo puntero

**Q:¿Es una copia independiente de original?**
A: en C++ si, en C# no

### ACTIVIDAD INTEGRADORA DE INVESTIGACIÓN

<details>

<summary>CÓDIGO MUESTRA</summary>

```C++
#include <iostream>

int contador_global = 100;

void ejecutarContador() {
    static int contador_estatico = 0;
    contador_estatico++;
    std::cout << "  -> Llamada a ejecutarContador. Valor de contador_estatico: " << contador_estatico << std::endl;
}

void sumaPorValor(int a) {
    a = a + 10;
    std::cout << "  -> Dentro de sumaPorValor, 'a' ahora es: " << a << std::endl;
}

void sumaPorReferencia(int& a) {
    a = a + 10;
    std::cout << "  -> Dentro de sumaPorReferencia, 'a' ahora es: " << a << std::endl;
}

void sumaPorPuntero(int* a) {
    *a = *a + 10;
    std::cout << "  -> Dentro de sumaPorPuntero, '*a' ahora es: " << *a << std::endl;
}

int main() {
    int val_A = 20;
    int val_B = 20;
    int val_C = 20;

    std::cout << "--- Experimento con paso de parámetros ---" << std::endl;
    std::cout << "Valor inicial de val_A: " << val_A << std::endl;
    sumaPorValor(val_A);
    std::cout << "Valor final de val_A: " << val_A << std::endl << std::endl;

    std::cout << "Valor inicial de val_B: " << val_B << std::endl;
    sumaPorReferencia(val_B);
    std::cout << "Valor final de val_B: " << val_B << std::endl << std::endl;

    std::cout << "Valor inicial de val_C: " << val_C << std::endl;
    sumaPorPuntero(&val_C);
    std::cout << "Valor final de val_C: " << val_C << std::endl << std::endl;

    std::cout << "--- Experimento con variables estáticas ---" << std::endl;
    ejecutarContador();
    ejecutarContador();
    ejecutarContador();

    return 0;
}
```
</details>

A. Predicción (sin ejecutar el código):
- ¿Cuál será la salida final en la consola de este programa? Para val_A termina con valor de 20 porque se cambia es una copia y las otras 2 (val_B Y val_C) terminan con 30 porque se hacen por referencia y por puntero lo que cambian la original. Y se imprime contador_estatico con 1, 2 y 3
  
- Escribe la salida completa que esperas.
  
- Dibuja un mapa de memoria conceptual de este programa justo antes de que main finalice. Debes indicar en qué segmento de memoria (Stack, Heap, Datos Globales/Estáticos, Código) se encontraría cada una de las siguientes variables: contador_global, contador_estatico, val_A, val_B, val_C (dentro de main), el parámetro a de la función sumaPorValor, la función main misma.

```c++
+-----------------------------------------------------------+
|                   SEGMENTO DE CÓDIGO (TEXT)               |
|          (Solo lectura - Instrucciones del programa)      |
|                                                           |
|  [ main() ]                                               |
|  [ ejecutarContador() ]                                   |
|  [ sumaPorValor(int) ]                                    |
|  [ sumarPorReferencia(int&) ]                             |
|  [ sumarPorPuntero(int*) ]                                |
+-----------------------------------------------------------+
|                 DATOS GLOBALES / ESTÁTICOS                |
|          (Permanecen durante toda la ejecución)           |
|                                                           |
|  - contador_global                                        |
|  - contador_estatico (persistente entre llamadas)         |
+-----------------------------------------------------------+
|                            HEAP                           |
|           (Vacío - No se solicitaron "new" o "malloc")    |
|                                                           |
|                             |                             |
|                             v                             |
+-----------------------------------------------------------+
|                             ^                             |
|                             |                             |
|                            STACK                          |
|          (Memoria temporal - Lógica de funciones)         |
|                                                           |
|  - val_A  (Variable local)                                |
|  - val_B  (Variable local)                                |
|  - val_C  (Variable local)                                |
+-----------------------------------------------------------+
```

B. Verificación y análisis (usando el depurador):

Ejecuta el programa paso a paso (F10) con un breakpoint al inicio de main.

- Compara la salida real con tu predicción. Si hubo diferencias, explica por qué ocurrieron. Evidencia clave: capturas de pantalla antes y después de los puntos de interés (¿Cuáles son esos puntos? -> tu tarea analizarlo).

- Describe qué demuestran estas capturas sobre la diferencia entre los diferentes tipos de paso por parámetros analizados.

- Explica con tus propias palabras el comportamiento de contador_estatico. ¿Por qué “recuerda” su valor entre llamadas a la función ejecutarContador? ¿En qué se diferencia de una variable local normal? Un contador_estatico recuerda porque se le declara como estatico, ya que se crean solo una vez y estos quedan guardados en la seccion de globales y estaticos, por lo que perduran al cambiar de funciones (a diferencia de las variables locales normales que se crean al entrar y se borran al salir de las funciones), esto les permite acumular cambios y duran hasta que termina el programa.

### ACTIVIDAD 6

<details>

<summary>CÓDIGO C#</summary>

```c#
   using System;

public class Punto
{
    public int x;
    public int y;

    public Punto(int _x, int _y)
    {
        x = _x;
        y = _y;
        Console.WriteLine($"Constructor: Punto({x}, {y}) creado.");
    }

    ~Punto()
    {
        Console.WriteLine($"Destructor: Punto({x}, {y}) destruido.");
    }

    public void Imprimir()
    {
        Console.WriteLine($"Punto({x}, {y})");
    }
}

class Program
{
    static void Main(string[] args)
    {
        Punto p = new Punto(10, 20);
        p.Imprimir();
    }
}
```

</details>

<details>

<summary>CÓDIGO C++</summary>

```c++
#include <iostream>
using namespace std;

class Punto {
public:
    int x;
    int y;

    // Constructor
    Punto(int _x, int _y) : x(_x), y(_y) {
        cout << "Constructor: Punto(" << x << ", " << y << ") creado." << endl;
    }

    // Destructor
    ~Punto() {
        cout << "Destructor: Punto(" << x << ", " << y << ") destruido." << endl;
    }

    // Método para imprimir valores
    void imprimir() {
        cout << "Punto(" << x << ", " << y << ")" << endl;
    }
};

int main() {
    // Coloca un breakpoint en la siguiente línea
    Punto p(10, 20);

    // Muestra el contenido del objeto
    p.imprimir();

    // Utiliza el depurador para inspeccionar 'p', observa la dirección de memoria y el valor de x e y.
    return 0;
}
```
</details>






## Bitácora de aplicación 



## Bitácora de reflexión



