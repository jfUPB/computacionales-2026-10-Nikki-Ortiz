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

## Bitácora de aplicación 



## Bitácora de reflexión
