# Unidad 2

## Bitácora de proceso de aprendizaje
### ACTIVIDAD 01

``` asm
(start)
@SCREEN
M=1

@END
(END)
0;JMP
```
--- 

### ACTIVIDAD 02

``` asm
(start)
@SCREEN
M=-1

@END
(END)
0;JMP
```

**RETO:**  

``` asm
(start)
@7
D=A
@SCREEN
M=D

@END
(END)
0;JMP
```
--- 

### ACTIVIDAD 03
``` asm
(START)
// Inicio la pantalla pintando una linea
@SCREEN
D=A
@i
M=D
@SCREEN
M=-1

(LOOP)
@KBD 
D=M
@100
D=D-A
@RIGHT
D;JEQ

@KBD 
D=M
@105
D=D-A
@LEFT
D;JEQ
@LOOP
0;JMP

(RIGHT)
@i
A=M
M=0
A=A+1
M=-1
D=A
@i
M=D
@LOOP
0;JMP

(LEFT)
@i
A=M
M=0
A=A-1
M=-1
D=A
@i
M=D
@LOOP
0;JMP


@END
(END)
0;JMP
```
--- 

### ACTIVIDAD 05

**INTRUCCIONES:** Convierte estos programas a ensamblador y realiza la simulación paso a paso. Recuerda la metodología: predice, ejecuta, observa y reflexiona.  

 **TRADUCCIÓN #1:**
``` asm
// int a = 10 
@10
D=A
@a
M=D
//  int* p; y p = &a;
@a
D=A
@p
M=D
// *p = 20;
@20
D=A
// Tener A el valor asignado en p 
@p
A=M
M=D
```

**TRADUCCIÓN #2:**  
``` asm
//int a = 10 
@10
D=A
@a
M=D
// int b = 5
@5
D=A
@b
M=D
//  int *p; y p = &a;
@a
D=A
@p
M=D
// *p = 20; b = *p;
@p // A= contenido de p, pero p contiene la direccion de a
A=M // A=16
D=M // D= Contenido de la direccion 16
@b // A = la direccion de b ---> 17
M=D // Guardando en la 17 que es b el 10 que se tiene en D
```
---

### ACTIVIDAD 06

**COMO HICE LA ACTIVIDAD**

Nos basamos en el código:

```C++
int arr[] = {1,20,13,24,55,96,87,83,98,102};
int sum = 0;

for (int j = 0; j < 10; j++) {
    sum = sum + arr[j];
}
```
**EXPLICACIÓN** 
Para poder transcribir este código a hack primero se tienen que inicializar los valores del arreglo uno por uno, despues se debe tener en cuenta de que no podemos utilizar la varibale "SUM"en hack porque su valor aisgnado en la memoria interferiría con el funcionamiento del código, por lo que nos fijamos que lugar de la memoria ocupa cada valor del arreglo, y nos da que SUM deberia ocupar el lugar de la memoria 26, por lo que ese número pasará a ser donde se inicialice el contador y donde se hagan las sumas hasta guardar el resultado final, por ultimo, se implementa un cliclo para poder sumar los valores, teniendo en cuenta que cada valor del arregl se guarda en un lugar de la memoria diferente y LA SUMA de estos en la 26.

**CÓDIGO ASM FINAL:**

```asm
//INICIALIZAR ARREGLO

@1
D=A
@16
M=D

@20
D=A
@17
M=D

@13
D=A
@18
M=D

@24
D=A
@19
M=D

@55
D=A
@20
M=D

@96
D=A
@21
M=D

@87
D=A
@22
M=D

@83
D=A
@23
M=D

@98
D=A
@24
M=D

@102
D=A
@25
M=D

// NICIALIZAR VARIABLES 

// puntero = 16
@16
D=A
@0
M=D

// acumulador (direccion 26) porque no se puede usar sum 
@26
M=0

// contador j = 0
@1
M=0

//  CICLO 
(LOOP)

// if j == 10 -> END
@1
D=M
@10
D=D-A
@END
D;JEQ

// acumulador += *puntero
@0
A=M
D=M

@26
M=D+M

// puntero++
@0
M=M+1

// j++
@1
M=M+1

@LOOP
0;JMP

(END)
@END
0;JMP
```
**PRUEBAS:**

<img width="1851" height="1285" alt="Captura de pantalla 2026-02-14 155807" src="https://github.com/user-attachments/assets/c7fd2387-2e1c-4d2c-8fd6-fd7015e4e8fb" />

--- 
### ACTIVIDAD 07 - OBSERVACIONES

Este programa en C++ define una variable global result, una función sum(a, b) que devuelve la suma de dos números, y en main llama a esa función con los valores 3 y 4 para guardar el resultado y mostrarlo en pantalla. En la versión en Hack assembly todo eso se hace manualmente, porque Hack no tiene estas funciones. Primero se inicializa result en 0, luego se cargan los argumentos 3 y 4 en los registros R0 y R1 (simulando los parámetros), después se guarda en R15 la dirección a la que debe regresar el programa cuando termine la función (esto simula el “return”). El programa salta a la etiqueta sum, donde suma los valores de R0 y R1 y guarda el resultado en R0. Finalmente usa la dirección guardada en R15 para volver al punto de retorno y copiar el resultado en la variable result. En esencia, el código Hack está imitando manualmente cómo una CPU real maneja llamadas a funciones, parámetros y retornos.

## Bitácora de aplicación 

### ACTIVIDAD 08

**PROBLEMA 1:**

<img width="800" height="800" alt="RESULTADO - PRUEBA 1" src="https://github.com/user-attachments/assets/c6a17ec7-6209-49e5-9002-8820f6cc7ab4" />  
<img width="271" height="702" alt="PARTE DEL SIMULADOR - EXPLICACIÓN" src="https://github.com/user-attachments/assets/64e1684d-9031-4594-bf35-d25f04574328" />

En la imagen se observa que RAM[16] = 20 Y RAM[17] = 10, sin embargo no es lo que originalmente se habia inicializado que era "a" en la dirección 16 con valor 10 y "b" en la dirección 17 con valor 20. Después de ejecutar swap(&a,&b) los valores se invierten. Eso significa que la función realmente accedió a las direcciones usando punteros y modificó lo que había en memoria, no solo copió valores locales. Si el swap no hubiera funcionado, seguirías viendo 10 en 16 y 20 en 17. 

**QUE SE VE EN LA PARTE DEL SIMULADOR:**

- RAM[16] --> representa la variable a
- RAM[17] --> representa la variable b
- R0 --> guarda la dirección de a (porque es el primer argumento)
- R1 --> guarda la dirección de b (segundo argumento)
- R13 --> se usa como variable temporal tmp
- R15 --> guarda la dirección de retorno

**COMO FUNCIONA EL PROGRAMA:**

El programa primero guarda el valor 10 en la dirección 16 (variable a) y el valor 20 en la dirección 17 (variable b). Luego pasa las direcciones de esas variables a la función swap colocando 16 en R0 y 17 en R1, siguiendo el protocolo de llamado. Dentro de la función, R0 y R1 no contienen los valores 10 y 20, sino las direcciones donde están guardados. Por eso se usa A=M para acceder indirectamente a esas posiciones. Primero se guarda temporalmente el contenido de *pa en R13, luego se copia el valor de *pb sobre *pa, y finalmente se copia el valor temporal sobre *pb. Así se intercambian realmente los contenidos de memoria. Al terminar, se usa R15 para regresar al punto de retorno en main.

**CÓDIGO:**
```asm
// a en RAM[16]
@10
D=A
@16
M=D

// b en RAM[17]
@20
D=A
@17
M=D

// pasar &a y &b
@16
D=A
@R0
M=D

@17
D=A
@R1
M=D

// guardar retorno
@returnSwap
D=A
@R15
M=D

@swap
0;JMP

(returnSwap)

// fin
@END
(END)
0;JMP

(swap)

// tmp = *pa
@R0
A=M
D=M
@R13
M=D

// *pa = *pb
@R1
A=M
D=M
@R0
A=M
M=D

// *pb = tmp
@R13
D=M
@R1
A=M
M=D

// return
@R15
A=M
0;JMP
```
---

**PROBLEMA 2:**
<img width="800" height="800" alt="RESULTADO - PRUEBA 2" src="https://github.com/user-attachments/assets/e3eb5eed-069b-4b3a-a037-274bfa2a37d7" />
<img width="315" height="916" alt="PARTE DEL SIMULADOR - EXPLICACIÓN" src="https://github.com/user-attachments/assets/06fcd11d-685f-4a64-a3c2-e81ff5390987" />

En la imagen se observa que el arreglo en RAM[16–20] contiene a 10, 15, 2, 3, 50, el valor final acumulado es 80, El registro D muestra 80, el programa quedó en un loop final (PC en 35). Lo que significa que la función calSum recorrió correctamente las 5 posiciones del arreglo y sumó todos los elementos.

**QUE SE VE EN LA PARTE DEL SIMULADOR:**

- RAM[16–20] --> contienen los elementos del arreglo
- R0 --> contiene la dirección base del arreglo (primer argumento)
- R1 --> contiene el tamaño (segundo argumento)
- R13 --> actúa como variable local sum
- R14 --> actúa como variable local i
- R15 --> guarda la dirección de retorno
- R0 al final --> contiene el valor retornado (80)

**COMO FUNCIONA EL PROGRAMA:**

El programa primero almacena el arreglo {10,15,2,3,50} en memoria (por ejemplo desde la dirección 16 en adelante), luego pasa a la función calSum dos argumentos siguiendo el protocolo: en R0 coloca la dirección base del arreglo (16) y en R1 el tamaño del arreglo (5). Dentro de la función se inicializa un acumulador en R13 (que representa sum) y un contador en R14 (que representa i), y comienza un ciclo que se ejecuta mientras i sea menor que el tamaño; en cada iteración se calcula la dirección del elemento actual sumando la base del arreglo más el desplazamiento (parr + i), se accede al contenido de esa dirección usando direccionamiento indirecto, se suma ese valor al acumulador y se incrementa el contador. Cuando el contador alcanza el tamaño del arreglo, el ciclo termina, el valor acumulado se copia a R0 como valor de retorno y el programa regresa al inicio usando la dirección almacenada en R15, dejando finalmente en R0 el resultado total de la suma, que debe ser 80.

**CÓDIGO:**
```asm
// arreglo desde 16
@10
D=A
@16
M=D

@15
D=A
@17
M=D

@2
D=A
@18
M=D

@3
D=A
@19
M=D

@50
D=A
@20
M=D

// pasar argumentos
@16
D=A
@R0
M=D

@5
D=A
@R1
M=D

// retorno
@returnCal
D=A
@R15
M=D

@calSum
0;JMP

(returnCal)

// resultado queda en R0

@END
(END)
0;JMP

(calSum)

// sum = 0
@R13
M=0

// i = 0
@R14
M=0

(LOOP)

// if i == arrSize → END
@R14
D=M
@R1
D=D-M
@ENDSUM
D;JEQ

// D = parr + i
@R0
D=M
@R14
D=D+M
@R13
// (no guardar aún)

// acceder *(parr+i)
@R0
D=M
@R14
D=D+M
A=D
D=M

// sum += valor
@R13
M=D+M

// i++
@R14
M=M+1

@LOOP
0;JMP

(ENDSUM)

// retornar sum en R0
@R13
D=M
@R0
M=D

@R15
A=M
0;JMP
```
## Bitácora de reflexión

### ACTIVIDAD 09



