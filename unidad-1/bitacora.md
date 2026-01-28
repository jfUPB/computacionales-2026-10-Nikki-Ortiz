# Unidad 1

## Bitácora de proceso de aprendizaje

### ACTIVIDAD 01

**EXPERIMENTO 1 - Q&A**

``` asm
@1
D=A
@2
D=D+A
@16
M=D
@END
(END)
0;JMP

```

*Q: ¿Qué sucede? ¿Qué valor se almacena en la dirección de memoria 16? ¿Por qué crees que es ese valor? ¿Qué instrucciones se ejecutan en cada ciclo Fetch-Decode-Execute? ¿Qué cambios observas en el contenido de la memoria y los registros?*

A: En la dirección de la memoria 16 se almacena el valor 3, este valor se debe a que se suma D+A y este valor pasa a guardarse en M que es la RAM por lo que en el slot de memoria 16 se guarda 3 que es la suma de D + A, en este ciclo el pc aumenta cada vez que se hace step (fetch), interpreta la instruccion, ya sea asignar el valor en el slot, sumar o hacer un salto (decode) y solo identifica que tiene que hacer pata despues ejecutarla (execute), en este caso termina en la ultima instruccion porque no hace ningun salto (tiene un 0).  

**EXPERIMENTO 2 - CÓDIGO Y OBSERVACIONES**

``` asm
@5
D=A // D = 5
@10
D=D+A // D = 5 + 10 = 15
@20
M=D // Guarda 15 en la dirección de memoria 20
@END
(END)
0;JMP

```
> NOTA: En este código se tiene como observación que se puede emular el experimento 1 y simplemente cambiar los números

**¿Qué diferencia hay entre los datos almacenados en la memoria ROM y en la RAM?**

A: El RAM (Random Access Memories) es un dispositivo de lectura y escritura usado para almacenar datos **que son temporales**, ya que es una memoria dinámica, que además se puede cambiar de forma constante durante la ejecución del programa. Luego está ROM (Read Only Memory) la cual es la encargada de **almacenar el programa** (osea, el conjunto de instrucciones secuenciales) que va a ejecutarse, de forma importante es un programa de solo lectura, por lo que no puede modificarse en medio de la ejecución.

La respuesta se basó en [COSAIR - ¿Cuál es la diferencia entre RAM y ROM?](https://www.corsair.com/es/es/explorer/diy-builder/memory/whats-the-difference-between-ram-and-rom/#:~:text=Diferencia%20entre%20RAM%20y%20ROM:%20Versi%C3%B3n%20r%C3%A1pida,BIOS%20de%20tu%20placa%20base.)

### ACTIVIDAD 02  

> NOTA: Las respuestas a esta pregunta serán basadas en el libro y el código proporcionados

**Q: Identifica una instrucción que use la ALU y explica qué hace.**  

A: Las intrucciones tipo C nos dicen: que computar, donde guardarlo, y que hacer como siguinete paso:  Instrucción que usa la ALU y su función. Dentro de estás intrucciones esta la "Especificación de Computación"  en la que se encentra la ALU (Unidad Aritmético Lógica). la cual esta diseñada para computar un set fijo de funciones en los registros D, A, y M.

En el caso del ejercicio, ordena a la ALU realizar una resta entre el valor almacenado actualmente en el registro D y el valor en el registro A. El resultado de esta operación se almacena de nuevo en el registro D.
    
**Q: ¿Para qué sirve el registro PC?**  

A: el PC (Program Counter) tiene como función contener la dirección de la próxima instrucción que debe ser buscada y ejecutada por la CPU. Normalmente se incrementa de manera automática para ejecutar el programa de forma lineal, pero cuando ocurre algún salto ( JMP, JNE, tc.), el PC se carga con el valor del registro A para cambiar el flujo del programa.  

**Q: ¿Cuál es la diferencia entre @i y @READKEYBOARD?**

A:
- **@i:** Es una variable definida por el usuario. El ensamblador le asigna una dirección única en la memoria RAM (generalmente a partir de la dirección 16) para almacenar datos que el programa necesita recordar.
- **@READKEYBOARD:** Es una etiqueta (label) de destino para saltos. Define simbólicamente una dirección en la memoria de instrucciones (ROM) y se utiliza para que el programa sepa a qué punto del código debe regresar o saltar. Es decir, es como decirle al programa "Si pasa X cosa, salta directtamente a la lectura del teclado".

**Q: Describe qué se necesita para leer el teclado y mostrar información en la pantalla.**  

A: De acuerdo con la especificación de Imput/Output en el librp:

- **Para leer el teclado:** Se debe acceder a la dirección de memoria 24576 (identificada por el símbolo predefinido KBD). Si una tecla está presionada, su código ASCII aparece en esa dirección; si no, el valor es 0.
- **Para mostrar información en pantalla:** Se debe escribir en el segmento de memoria que comienza en la dirección 16384 (símbolo SCREEN). Cada bit en este mapa de memoria representa un píxel en la pantalla física (1 para negro, 0 para blanco).

**Q: Identifica un bucle en el programa y explica su funcionamiento.**  

A: El programa contiene un bucle principal que comienza en la etiqueta (READKEYBOARD) y termina con la instrucción @READKEYBOARD seguida de 0;JMP al final del código. Este es un bucle infinito donde el programa lee constantemente el estado del teclado (@KBD, D=M). Si no se detecta ninguna tecla presionada, el flujo continúa para limpiar o retroceder la posición en pantalla y luego vuelve al inicio para verificar el teclado nuevamente.


**Q: Identifica una condición en el programa y explica su funcionamiento.**  

A: Una condición se encuentra en el bloque:

``` asm
@KBD
D=M
@KEYPRESSED
D;JNE
```

1. Primero, carga el valor actual del teclado en el registro D.
2. Luego, utiliza la instrucción de salto condicional JNE (Jump if Not Equal).
3. La condición es: Si el valor en D no es cero (es decir, si D es diferente de 0, lo que significa que se ha presionado una tecla), el programa salta a la sección etiquetada como (KEYPRESSED). Si el valor es cero, la condición no se cumple y el programa sigue con la siguiente instrucción de forma secuencial.


### ACTIVIDAD 03
   
**INSTRUCCION:** Escribe un programa que compare el valor almacenado en la dirección de memoria 5 con el valor 10. Si el valor es menor que 10, guarda el valor 1 en la dirección 7. Si el valor es mayor o igual a 10, guarda el valor 0 en la dirección 7.

**CÓDIGO COMENTADO:**

``` asm
// Cargar el valor de la dirección 5
@5
D=M
@10
D=D-A

// D < 0, saltar a LESS
@LESS
D;JLT

// Caso: valor >= 10
@7
M=0
@FIN
0;JMP

(LESS)
// Caso: valor < 10
@7
M=1

(FIN)
```

**ANOTACIÓN: En el caso de este codigo pasa que siempre va a tener el 1 guardado en la dirección 7 porque el valor de la memoria 5 por defecto es 0, la condición de que sea menor que 10 siempre se va a cumplir y en vez de seguir al "else" que seria poner en la direccion 7 el valor 0 (que sería como no hacer ningún cambio) este salta al final del programa porque no se cumple esa condición (si ubieramos cargado algun valor a la memoria 5 entonces el programa se saltaria el primer condicional y ejecutaria el segundo) 


## Bitácora de aplicación 



## Bitácora de reflexión




