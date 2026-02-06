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

## Bitácora de aplicación 



## Bitácora de reflexión

