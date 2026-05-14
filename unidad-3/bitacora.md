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

**PREGUNTAS**

**Q: Abre la calculadora de Windows y selecciona el modo de programador. Cambia a modo hexadecimal. Escribe 0a ¿Qué valor en decimal obtienes? Escribe 14 ¿Qué valor en decimal obtienes? ¿Qué observas?**

A: Se observa que 0a = 10, 14 = 20, por tanto, los valores de x & y se están almacenados directamente en memoria como enteros.

**Q: Si la arquitectura de tu computador fuera big-endian, ¿Cómo quedarían almacenados los bytes en la memoria de p?**

A: Los bytes en la memoria p quedarían almacenados como:  **00 00 00 0a 00 00 00 14**

**Reflexiona sobre las siguientes cuestiones:**

**Q:¿Cuál es la diferencia entre un constructor y un destructor en C++?**

A: En C++ los **Constructores** tienen como propósito inicializar un objeto y asignar recursos, esto se da cuando se cre< un objeto. Mientras que los **Destructores** se dan al momento de destruir el objeto (sale de ámbito o se borra, cuando termina el main) y se usa para liberar recursos y limpiar la memoria de ser necesario.

**Q: ¿Cuál es la diferencia entre un objeto y una clase en C++?**

A: Clase es el molde o plantilla (define qué tendrá el objeto), y objeto es la instancia real creada a partir de la clase.

**Q: ¿Qué diferencia notas entre el objeto Punto en C++ y C#?**

en C++ esta almacenada en el stack y es el objeto mismo, en C#, p no es el objeto sino una referencia, el objeto real esta en el heap

**Q: ¿Qué es p en C++ y qué es p en C#?**

A: en C++, p es un objeto y en C#, p es una referencia a un objeto

**Q:¿En qué parte de memoria se almacena p en C++ y en C#?**

A: en C++, p esta en el stack y en C#, p esta en el heap

**Q: ¿Qué observaste con el depurador acerca de p? Según lo que observaste ¿Qué es un objeto en C++?**

El depurador muestra que p ocupa espacio real en memoria, sus datos están almacenados directamente de forma contigua, no es un puntero ni una referencia, sino que es una estructura de datos con sus miembros almacenados uno después del otro.

---

### ACTIVIDAD 07 

<details>

<summary>CÓDIGO</summary>

```c+
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
    // Objeto en el stack
    Punto pStack(30, 40);
    pStack.imprimir();

    // Objeto en el heap
    Punto* pHeap = new Punto(50, 60);
    pHeap->imprimir();

    // Coloca breakpoints en la creación de pStack y pHeap
    // Inspecciona las direcciones de memoria de ambos objetos:
    // - pStack: dirección obtenida directamente.
    // - pHeap: la variable pHeap es un puntero que contiene la dirección del objeto en el heap.

    // Recuerda liberar la memoria del heap
    delete pHeap;

    return 0;
}
```

</details>

**Q: Explicación de la diferencia entre objetos creados en el stack y en el heap**

| Stack                                       | Heap                                                         |
| ------------------------------------------- | -----------------------------------------------------------  |
| cuando main termina se llama el destructor  | si no se hace detele, solo queda ocupando memoria            |
| se crea autamaticamente                     | se crea manualmente usando new                               |
| su direccion se obtiene con &pStack         | pHeap guarda la direccion del objeto real que esta en el heap|
| Punto pStack (30, 40);                      | Punto* pHeap = new Punto(50, 60);                            |
| vive solo dentro de main                    | vive hasta que se borre con delete                           |

**Q: pStack ¿Es un objeto o una referencia a un objeto?**
A: pStack es un objeto que esta guardado directamente en el stack
**Q: pHeap ¿Es un objeto o una referencia a un objeto? Si es una referencia, ¿A qué objeto hace referencia?**
A: pHeap es una referencia / puntero a un objeto, hace referencia referencia al objeto Punto(50,60) que está en el heap
**Q: Observa en Memory1 (Debug->Windows->Memory->Memory1) el contenido de la dirección de memoria de pHeap, recuerda escribir en la entrada de texto de Memory1 la dirección de memoria de &pHeap y presionar Enter. Compara el contenido de memoria con el contenido de pHeap en la pestaña de Locals (Debug->Windows->Locals). ¿Qué observas? ¿Qué significa esto?**
A: En locals, pHeap se muestra algo como 0x00000162f6545160{x=50 y=60} En Memory1, al mirar &pHeap, lo que aparece guardado ahí es 60 51 54 f6 62 01 00 00, lo que seria la dirección del objeto en little endidan seria 00 00 01 62 f6 54 51 60, lo que en hexadecimal seria 00 00 01 62 f6 54 51 60, Exactamente el valor que tenía pHeap en locals

---

### ACTIVIDAD 08

### ACTIVIDAD 09

## Bitácora de aplicación 

### ACTIVIDAD INTEGRADORA DE APLICACIÓN 02

<details>

<summary>PASO 1</summary>

### Diagnóstico del problema (análisis):

ERROR 1 - Fuga de Memoria (Memory Leak): Con este error la predicción sería que si en el juego se crean muchos NPCs, la memoria RAM se llenará progresivamente hasta que el motor se quede sin recursos y colapse. Esto ocurre ya que en el constructor, se usa "estadisticas = new int[3]" Lo cual reserva espacio en el Heap. Cuando la función "simularEncuentro" termina, el objeto heroe se destruye del Stack, pero el bloque de memoria en el Heap sigue ocupado. Por lo que, al no existir un "Destructor (~Personaje())" que ejecute delete[] estadisticas, el puntero desaparece pero la memoria queda "huérfana" porque el espacio esta ocupado pero sin objeto, ocacionando que la clase Personaje solicita memoria en el constructor, pero no tiene un mecanismo para devolverla.

ERROR 2 - Copia Incorrecta: Este error, en el escenario de una compañia de videojuegos provocaría problemas relacionados con la corrupción de datos y con cierres inesperados (Crashes) del motor de juego, en el caso particular del código este es el error que causa el "crash" inmediato y ocurre al ejecutar "Personaje copiaHeroe = heroe;". Sucede cuando C++ realiza una copia automática de todos los miembros, entonces, como "estadisticas" es un puntero (una dirección de memoria) y "copiaHeroe" recibe exactamente la misma dirección que heroe, ambos objetos terminan apuntando al mismo bloque de memoria en el Heap. Practicamente, es como si uno modificaraa los datos, el otro ve esos cambios sin saberlo, lo que resulta que al final, cuando ambos objetos intentan destruirse, el sistema intenta liberar la misma memoria dos veces y ocaciona un cierre súbito del programa.
</details>

<details>

<summary>PASO 2</summary>

### Uso del Depurador, Capturas de Pantalla y Análisis 

<img width="2489" height="1134" alt="ERROR 1" src="https://github.com/user-attachments/assets/124a4bef-085b-463e-a5ff-1f7dc5516c4a" />

En esta imagen se muestra como se reservo la memoria dinámica.

<img width="1237" height="341" alt="ERROR 2" src="https://github.com/user-attachments/assets/3201439a-38f0-4c88-ac47-4d0a877ee026" />
 Acá se muestra que el personaje de Aragorn y la Copia tienen la misma dirección en "Estadisticas"

</details>

<details>

<summary>PASO 3</summary>

### Induce los fallos:

**CÓDIGO**

```C++
#include <iostream>
#include <string>

class Personaje {
public:
    std::string nombre;
    int* estadisticas;

    Personaje(std::string n, int vida, int ataque, int defensa) {
        nombre = n;
        estadisticas = new int[3];
        estadisticas[0] = vida;
        estadisticas[1] = ataque;
        estadisticas[2] = defensa;
        std::cout << "Constructor: nace " << nombre << std::endl;
    }

    void imprimir() {
        std::cout << "Personaje " << nombre
            << " [Vida: " << estadisticas[0]
            << ", ATK: " << estadisticas[1]
            << ", DEF: " << estadisticas[2]
            << "]" << std::endl;
    }
};

void simularEncuentro() {
    std::cout << "\n--- Iniciando encuentro ---" << std::endl;
    Personaje heroe("Aragorn", 100, 20, 15);

    Personaje copiaHeroe = heroe;
    copiaHeroe.nombre = "Copia de Aragorn";

    std::cout << "Saliendo del encuentro..." << std::endl;
}

int main() {
    for (int i = 0; i < 200000; i++) { // Se agregó esta línea
    simularEncuentro();
    std::cout << "\nSimulación terminada." << std::endl;
    return 0;
}
```
¿QUÉ VA A PASAR? ¿POR QUÉ?

Al inducir este error debría pasar un Agotamiento de Memoria (Memory Leak) ya que cada vez que se llama a "simularEncuentro()", el programa reserva memoria en el Heap para 3 enteros (new int[3]). Como la clase Personaje no tiene un destructor que libere esa memoria (delete[]), multiplicar esos bytes por 200,000 veces creará una fuga masiva y generará un crash.

**EVIDENCIA**  


<img width="400" height="600" alt="ANTES (CON EL BREAKPOINT DONDE SE SALTABA LA LINEA DEL ERROR" src="https://github.com/user-attachments/assets/8b5d905a-108d-4220-a558-1fd366aa9487" />

<img width="400" height="600" alt="DESPUÉS" src="https://github.com/user-attachments/assets/23c01946-8fca-4730-9b36-547a36f622d3" />

---  
**CÓDIGO**

```C++
#include <iostream>
#include <string>

class Personaje {
public:
    std::string nombre;
    int* estadisticas;

    Personaje(std::string n, int vida, int ataque, int defensa) {
        nombre = n;
        estadisticas = new int[3];
        estadisticas[0] = vida;
        estadisticas[1] = ataque;
        estadisticas[2] = defensa;
        std::cout << "Constructor: nace " << nombre << std::endl;
    }

    void imprimir() {
        std::cout << "Personaje " << nombre
            << " [Vida: " << estadisticas[0]
            << ", ATK: " << estadisticas[1]
            << ", DEF: " << estadisticas[2]
            << "]" << std::endl;
    }
};

void simularEncuentro() {
    std::cout << "\n--- Iniciando encuentro ---" << std::endl;
    Personaje heroe("Aragorn", 100, 20, 15);

    Personaje copiaHeroe = heroe;
    copiaHeroe.nombre = "Copia de Aragorn";
    
    copiaHeroe.estadisticas[0] = 561532; // vida, aqui empieza lo que se añadió
    heroe.imprimir();
    copiaHeroe.imprimir(); // acá termina

    std::cout << "Saliendo del encuentro..." << std::endl;
}

int main() {
        simularEncuentro();
        std::cout << "\nSimulación terminada." << std::endl;
        return 0;
   
}

```
 ¿QUÉ SUCEDE?

Aunque solo se escribió copiaHeroe.estadisticas[0] = 561532;, al llamar a heroe.imprimir(), el héroe también tiene esa vida. Esto prueba que ambos objetos comparten la misma dirección de memoria. No son dos personajes independientes, son dos nombres apuntando a la misma dirección de estadísticas en el Heap.

<img width="926" height="574" alt="ERROR 2" src="https://github.com/user-attachments/assets/b414ddd5-c80a-466e-94d4-4f9c281ee1d1" />

</details>

<details>

<summary>PASO 4 y 5 </summary>

### Solución, refactorización (síntesis y creación) y justificación

**ERROR 1:**

En este caso en lugar de intentar corregir el problema agregando destructor y manejando manuealmente new y delete[], seria mejor eliminar la gestion manual de memoria dinamica dado que el arreglo de estadisticas tiene un tamaño fijo, entonces estaríamos reemplazando:

```c++
int* estadisticas;
```
por algo como un arreglo que permita almacenar las estadisticas directamente dentro del objeto, y que evita el uso de new y delete, permitiendo que la copia del objeto sea segura

```c++
std::array<int, 3> estadisticas;
```

**CÓDIGO REFACTORIZADO**

```c++
#include <iostream>
#include <string>
#include <array>

class Personaje {
public:
    std::string nombre;
    std::array<int, 3> estadisticas; // [vida, ataque, defensa]

    Personaje(const std::string& n, int vida, int ataque, int defensa)
        : nombre(n), estadisticas{vida, ataque, defensa} {
        std::cout << "Constructor: nace " << nombre << std::endl;
    }

    void imprimir() const {
        std::cout << "Personaje " << nombre
            << " [Vida: " << estadisticas[0]
            << ", ATK: " << estadisticas[1]
            << ", DEF: " << estadisticas[2]
            << "]" << std::endl;
    }
};
```
**ERROR 2**

En este caso vamos a eliminar el puntero dinamico y usar un arreglo para que sea seguro cuando se haga la copia del heroe, se copia el nombre, los 3 enteros de estadisticas y cada objeto tiene su propio almacenamiento independiente.


```c++
std::array<int, 3> estadisticas;
```

**CÓDIGO MODIFICADO**

```c++
#include <iostream>
#include <string>
#include <array>

class Personaje {
public:
    std::string nombre;
    std::array<int, 3> estadisticas; // [vida, ataque, defensa]

    // Constructor (sin new, sin delete)
    Personaje(const std::string& n, int vida, int ataque, int defensa)
        : nombre(n), estadisticas{ vida, ataque, defensa } {
        std::cout << "Constructor: nace " << nombre << std::endl;
    }

    // (No hace falta destructor: no hay memoria dinámica manual)
    // (No hace falta constructor de copia ni operador de asignación:
    //  std::string y std::array copian correctamente)

    void imprimir() const {
        std::cout << "Personaje " << nombre
            << " [Vida: " << estadisticas[0]
            << ", ATK: " << estadisticas[1]
            << ", DEF: " << estadisticas[2]
            << "]" << std::endl;
    }
};

void simularEncuentro() {
    std::cout << "\n--- Iniciando encuentro ---" << std::endl;

    Personaje heroe("Aragorn", 100, 20, 15);
    heroe.imprimir();

    // Ahora la copia es segura (copia real de estadisticas)
    Personaje copiaHeroe = heroe;
    copiaHeroe.nombre = "Copia de Aragorn";

    copiaHeroe.imprimir();

    std::cout << "Saliendo del encuentro..." << std::endl;
}

int main() {
    simularEncuentro();
    std::cout << "\nSimulación terminada." << std::endl;
    return 0;
}
```
En conclusión, se realizó la Eliminación de Fugas de Memoria usando un array que hace que no se requiera un  destructor manual "(delete[])". Lo que resulta en que cuando el objeto Personaje sale del Stack al finalizar "simularEncuentro()", sus estadísticas desaparecen automáticamente con él. Adicionalmente, se solucionó la copia de manera erronea y ahora al ejecutar Personaje "copiaHeroe = heroe;", C++ crea un bloque de memoria totalmente nuevo para la copia y clona los valores uno a uno, lo que debería evidenciarse al modificar "copiaHeroe.estadisticas[0]", la vida del heroe original permanecerá intacta y no habrá punteros duplicados peleando por la misma dirección.
</details>  

## Bitácora de reflexión  

### ACTIVIDAD 11

**PARTE 1 - PREGUNTAS**  

**Q: Explica con tus propias palabras qué es el stack y qué es el heap en C++. (Complemento de lo que s emencionó más arriba)**

A: El stack (pila) es la parte de memoria que se usa para guardar variables locales y las llamadas a funciones; Es automática, cuando se entra a una función, se reservan sus variables en el stack, y cuando la función termina, esa memoria se libera sola, además de ser rápida y ordenada. El heap, en cambio, es la memoria que se maneja manualmente con new y delete. Aquí se guardan datos que necesitan vivir más tiempo o cuyo tamaño no conozco en tiempo de compilación. No se libera sola, así que si no se usa delete, puedo tener fugas de memoria.

- Stack --> automática y temporal.
- Heap --> dinámica y controlada por el programador.

**Q: Describe las tres formas de pasar parámetros a una función en C++ (valor, referencia y puntero). Para cada una, explica qué sucede en memoria y cuándo usarías cada método.**

A: De la siguiente manera:

- Paso por valor: En memoria: se crea otra variable x dentro del stack de la función. Si modifico x, no cambia la variable original. Yo usaría paso por valor cuando el dato es pequeño (como int, char, etc.) y no necesito modificar el original.
  
- Paso por referencia (Se usa &): Es como un alias, no se crea una copia, sino que la función trabaja directamente con la variable original. En memoria, básicamente se usa la misma dirección. Yo lo usaría cuando quiera modificar el valor original y no quiera hacer copia porque el objeto es grande (como un vector o una clase grande).

- Paso por puntero (se pasa la dirección de memoria usando): Aquí se copia el puntero, pero el puntero apunta a la misma dirección de memoria del dato original. Entonces puedo modificar el contenido usando *. Lo usaría cuando mecesito trabajar explícitamente con direcciones y este trabajado con memoria dinámica.

**Q: ¿Qué diferencia hay entre una variable local, una variable global y una variable local estática? ¿En qué segmento del mapa de memoria se almacena cada una?**

| Variable Local                               | Variable Global                                                              | Variable Local Estática              |
|  ---                                         |      ---                                                                     |          ---                         |           
| Vive solo mientras se ejecuta la función.    | Vive durante todo el programa.                                               | Vive durante todo el programa.       |
| Se guarda en el stack.                       | Se guarda en el segmento de datos de la memoria.                             | Conserva su valor entre llamadas.    |
| Se destruye al salir de la función.          | Puede ser usada por varias funciones (aunque no siempre es buena práctica).  |Solo es visible dentro de la función. |


**Q: Explica qué es un objeto en C++ desde la perspectiva de memoria. ¿Dónde se almacenan los miembros de instancia y dónde los miembros estáticos?**

A: Un objeto en C++ es un bloque de memoria que guarda los datos propios de una clase. Cuando se crea un objeto, se reserva espacio para sus miembros de instancia, y cada objeto tiene su propia copia de esos datos. Los miembros estáticos no se guardan dentro del objeto, porque pertenecen a la clase completa. Por eso se almacenan en una zona de memoria aparte y son compartidos por todos los objetos.


