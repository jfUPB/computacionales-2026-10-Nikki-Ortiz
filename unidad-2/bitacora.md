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




## Bitácora de aplicación 



## Bitácora de reflexión
