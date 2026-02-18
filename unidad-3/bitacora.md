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

## Bitácora de aplicación 



## Bitácora de reflexión
