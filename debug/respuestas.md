Ejercicios HO Clase 4

bugs/
Ejecuto "make segfault"
gcc -c add_array_segfault.c -o add_array_segfault_c.o
gcc add_array_segfault_c.o -o add_array_segfault.e

Cuando compila y luego ejecuto me da error de Segmentation Fault, al hacer debug me dice que no debugging symbols, eso es porque no tenia el flag para gdb. Entonces voy al Makefile y modifico lo siguiente:
"CC = gcc" por "CC = gcc -g -O0"
Tengo que hacer make clean y luego de nuevo make segfault

cuando hago gdb salta un error en la linea 19, el problema es que b no esta alocado. 
Este error esta resuelto utilizando malloc para que busque el archivo y lo pueda alocar. Esto es el que se ejecuta con make dynamic:
gcc -g -O0 -c add_array_dynamic.c -o add_array_dynamic_c.o
gcc -g -O0 add_array_dynamic_c.o -o add_array_dynamic.e

Al ejecutarlo la suma da 6 y es correcto, corre bien.

Siguiente ejemplo es el add_array_static. 
Con make static: 
gcc -g -O0 -c add_array_static.c -o add_array_static_c.o
gcc -g -O0 add_array_static_c.o -o add_array_static.e

En esta maquina la suma da 6, pero en otras maquinas si bien no tira error da un numero muy grande y raro. Ese caso seria un error muy dificil si a priori no se supiera que resultado se espera. 

Al hacer gdb y marcar un break point en donde esta la funcion: br add_array

make nobugs:
gcc -g -O0 -c add_array_nobugs.c -o add_array_nobugs_c.o
gcc -g -O0 add_array_nobugs_c.o -o add_array_nobugs.e

El ejecutable corre normalmente.

fpe/
Con test1: ejemplos de ejecucion:
a=2,b=3, c=inf
a=3,b=3, c=inf
a=0,b=0, c=0.000000

Con test2:
a=1,b=1, c = 1.000000
a=2,b=3, c = 1.000000
a=0,b=0, c=-1.000000 

Para que haga include TRAPFPE debo compilar con el flag -DTRAPFPPE
gcc -DTRAPFPE -Ifpe_x87_sse -c test_fpe1.c -o test_fpe1.o
Luego compilo ejecutable linkeando el fpe_x87_sse y test_fpe1, agregando la libreria m, ya que dentro del fpe_x87_sse habia funciones sin definir si no llamo a la libreria. 
gcc ./fpe_x87_sse/fpe_x87_sse.o test_fpe1.o -lm -o test_fpe1.e


Sin la opcion -DTRAPFPE corria pero daba infinito (c=inf) y cuando hacia 0/0 daba 0. En cambio incluyendola se logra que de resultado correcto, y en caso de hacer 0/0 al ser una indeterminacion el programa se cierra diciendo que es una excepcion de ncoma flotante ('core' generado). Esto es correcto, ya que no podria dar un valor como resultado.


Segmentation Fault:
source.f90, my_func.f
Cuando corro el big.x el problema de segmentation fault se debe a que el size=< error de lectura de variable: No se puede acceder a la memoria 

valgrind/
compilo (con flag -g) 
gcc -g -c source1.c
gcc source1.o -o source1.e

y ejecuto valgrind ./source1.e
$ valgrind --leak-check=full --track-origins=yes ./source1.e
==4837== Memcheck, a memory error detector
==4837== Copyright (C) 2002-2013, and GNU GPL'd, by Julian Seward et al.
==4837== Using Valgrind-3.10.1 and LibVEX; rerun with -h for copyright info
==4837== Command: ./source1.e
==4837== 
==4837== 
==4837== HEAP SUMMARY:
==4837==     in use at exit: 3,960,000 bytes in 99 blocks
==4837==   total heap usage: 101 allocs, 2 frees, 4,040,000 bytes allocated
==4837== 
==4837== 3,960,000 bytes in 99 blocks are definitely lost in loss record 1 of 1
==4837==    at 0x4C2AB80: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==4837==    by 0x4005D6: mat_Tmat_mul (source1.c:30)
==4837==    by 0x4007C8: main (source1.c:59)
==4837== 
==4837== LEAK SUMMARY:
==4837==    definitely lost: 3,960,000 bytes in 99 blocks
==4837==    indirectly lost: 0 bytes in 0 blocks
==4837==      possibly lost: 0 bytes in 0 blocks
==4837==    still reachable: 0 bytes in 0 blocks
==4837==         suppressed: 0 bytes in 0 blocks
==4837== 
==4837== For counts of detected and suppressed errors, rerun with: -v
==4837== ERROR SUMMARY: 1 errors from 1 contexts (suppressed: 0 from 0)

Es un problema de Memory LEAK, me marca que el problema esta en el for de 99 bloques (yo cambie el FOR de 1 a SIZE=100) y luego en el LEAK summary me da informacion que el error lo tiene en la funcion mat_Tmat_mul, lineas 30, y del main linea 59. (La info del numero de lineas me la da si compilo con flag -g).


funny/
compilo sin -DDEBUG:
gcc -g -c test_oob2.c
gcc test_oob2.o -o test_oob2.e
Cuando ejecuto ./test_oob2.e me tira violacion de segmento.

Cuando compilo con -DDEBUG:
gcc -g -c -DDEBUG test_oob2.c 
gcc test_oob2.o -o test_oob2.e

Cuando ejecuto me tira violacion de segmento pero tambien me hace un print del string I'm HERE!!! diciendome donde esta el bug.

