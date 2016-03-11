Compilo con distintas formas de optimizacion y analizo la performance con time:
gcc -g -O0 -c profile_me_1.c -o profile_me_1_O0.o
gcc profile_me_1_O0.o -o profile_me_1_O0.e
time ./profile_me_1_O0.e 
Violación de segmento (`core' generado)
real	0m0.099s
user	0m0.000s
sys	0m0.002s

gcc -g -O3 -c profile_me_1.c -o profile_me_1_O3.o
gcc profile_me_1_O3.o -o profile_me_1_O3.e
time ./profile_me_1_O3.e
real	0m0.006s
user	0m0.000s
sys	0m0.001s
Compilando con -O3 no da violacion de segmento.

gcc -g -O1 -c profile_me_1.c -o profile_me_1_O1.o
gcc profile_me_1_O1.o -o profile_me_1_O1.e
time ./profile_me_1_O1.e
Violación de segmento (`core' generado)
real	0m0.105s
user	0m0.000s
sys	0m0.002s

%%%Probando compilar con gprof (flag -pg) con las distintas optimizaciones:
gcc profile_me_1.c -pg -O0 -o profile_me_1_pg_O0.e
./profile_me_1_pg_O0.e
Violación de segmento (`core' generado)

gcc profile_me_1.c -pg -O1 -o profile_me_1_pg_O1.e
./profile_me_1_pg_O1.e
Violación de segmento (`core' generado)

gcc profile_me_1.c -pg -O3 -o profile_me_1_pg_O3.e
./profile_me_1_pg_O3.e

Solo cuando no tiene segmentation fault (que lo logra con optimizacion O3) puede generar el gmon.out y asi puedo crear el profile.info
./profile_me_1_pg_O3.e
gprof profile_me_1_pg_O3.e gmon.out  > profile.info

%%%Perf
$ perf stat ./profile_me_1_pg_O3.e
Performance counter stats for './profile_me_1_pg_O3.e':

          0,485381 task-clock (msec)         #    0,073 CPUs utilized          
                 6 context-switches          #    0,012 M/sec                  
                 0 cpu-migrations            #    0,000 K/sec                  
               139 page-faults               #    0,286 M/sec                  
         1.389.334 cycles                    #    2,862 GHz                    
           956.175 stalled-cycles-frontend   #   68,82% frontend cycles idle   
           785.425 stalled-cycles-backend    #   56,53% backend  cycles idle   
           851.180 instructions              #    0,61  insns per cycle        
                                             #    1,12  stalled cycles per insn
           165.395 branches                  #  340,753 M/sec                  
            11.421 branch-misses             #    6,91% of all branches        

       0,006613065 seconds time elapsed
Ese tiempo en seconds time elapsed coincide con el que nos dio al hacerle un time ./program.e

-Con optimizacion -O1
$ perf stat ./profile_me_1_pg_O1.e 
./profile_me_1_pg_O1.e: Violación de segmento

 Performance counter stats for './profile_me_1_pg_O1.e':

          0,763832 task-clock (msec)         #    0,008 CPUs utilized          
                 4 context-switches          #    0,005 M/sec                  
                 1 cpu-migrations            #    0,001 M/sec                  
               132 page-faults               #    0,173 M/sec                  
         1.184.853 cycles                    #    1,551 GHz                    
           742.446 stalled-cycles-frontend   #   62,66% frontend cycles idle   
           585.550 stalled-cycles-backend    #   49,42% backend  cycles idle   
           897.897 instructions              #    0,76  insns per cycle        
                                             #    0,83  stalled cycles per insn
           173.088 branches                  #  226,605 M/sec                  
             8.996 branch-misses             #    5,20% of all branches        

       0,099987875 seconds time elapsed


-Con optimizacion -O0
$ perf stat ./profile_me_1_pg_O0.e 
./profile_me_1_pg_O0.e: Violación de segmento

 Performance counter stats for './profile_me_1_pg_O0.e':

          0,781552 task-clock (msec)         #    0,009 CPUs utilized          
                 4 context-switches          #    0,005 M/sec                  
                 2 cpu-migrations            #    0,003 M/sec                  
               132 page-faults               #    0,169 M/sec                  
         1.213.650 cycles                    #    1,553 GHz                    
           764.129 stalled-cycles-frontend   #   62,96% frontend cycles idle   
           608.911 stalled-cycles-backend    #   50,17% backend  cycles idle   
           897.721 instructions              #    0,74  insns per cycle        
                                             #    0,85  stalled cycles per insn
           173.658 branches                  #  222,196 M/sec                  
             9.037 branch-misses             #    5,20% of all branches        

       0,090579899 seconds time elapsed







