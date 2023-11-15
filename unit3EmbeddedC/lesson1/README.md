Agenda 
- Bare metal vs operating systems
- optimization
    - optimization issues
	- solution by volatile modifier
	- const with volatile benifits
- Toolchain  
## Bare metal vs operating system
- bare metal archticture : Application can call hardware liberaries to make change (read , write ) on hardware direct
- os archticture : Application can call system calls only to convert from user mode to kernal mode.

This is playlist for [Bare metal](https://www.youtube.com/watch?v=qWqlkCLmZoE&list=PLERTijJOmYrDiiWd10iRHY0VRHdJwUH4g&index=1)

## Optimization 

*is the series of actions that taken by the compiler.*

*To compile code with specific optimization level*
```
$ gcc -Og  p1.c p2.c -o p
```
*replace g with desired level  for example*
> gcc -01 p1.c -o p
#### optimization target 
is to reduce memory access,so sometimes optimizing compiler caches global variable to register to improve effeciency,but sometimes it makes tricky bugs. 
#### ***why?***

- according to os : Signal handling is one of the thornier aspects of Linux system-level programming.  Handlers run concurrently with the main program and share the same global variables, and thus can interfere with the main program 
- according to bare metal : interupt handling can share global variables with main and modify them.

> *Consider a handler and main routine that share a global variable g.*
> 
> The handler updates g, and main periodically reads g. according to an optimizing compiler, it would appear that the value of g never changes in main (no direct call of handlers), and thus it would be safe to use a copy of g that is cached in a register to satisfy every reference to g. 
>
> ***In this case, the main function would never see the updated values from the handler.***
>
#### Solution 
> #### volatile modifier in optimization
>
> *You can tell the compiler not to cache a variable by declaring it with
the volatile type qualifier. 
For example*: 
> ```
> volatile int g;
> ```
>
#### explaination
> *The volatile qualifier forces the compiler to read the value of g from memory each time it is referenced in the code. In general, as with any shared data structure, each access to a global variable should be protected by temporarily blocking signals.***  
if you want to study this case with application you can see my shell project.
>


#### volatile with const modifiers
> int volatile * const Preg1 = 
(int*) 0xFFFF0000;

* Preg1 is a const pointer pointing to volatile data of type uint32_t.
* In this case const is used just to guard the pointer from unexpected changes from the programmer.
* The volatile is used to tell compiler that data pointed could receive unexpected changes, the compiler has not to apply any optimization on that data.

> int const volatile * const Preg2 = (int*)0xFFFF0004;

* Preg2 is a const pointer pointing to volatile const data of type uint32_t.

* This qualifier (const+volatile) on data means that data pointed by the indicated address is volatile (unexpected changes) but the programmer shouldn’t change the data pointed to that address (const purpose). 

**In this case const is useful for (R read only access) register like the status register which is changed from the HW not from the programmer.**


## toolchain : 
consists of compiler,assempler, linker , debuger,liberaries, number of helper progtamms called utilities (such as objdump-readelf).

there are three different machinces to specify the type of toolchain 
1. build machinc : where the toolchain is built 
2. host machine : for building program(excute toolchain)
3. target machine : for running excutable file(generated by toolchain).

**So** the most important types of toolchains are: 
1. cross toolchain : can build program on host machince , but generate excutable file for different processor (target)
2. native toolchain : can build program and generate excutable file for the same processor.

**note** 
some important commands to generate each file created while building process.

- To specify the path of include files  
```
gcc main.c -I<path1.h> -I...
```
- To enable all warnings during compilation process
```
gcc -Wall main.c -o main
```
- To treat all warnings as errors
```
gcc -Wall -Werror main.c -o main
```
- to pass options in file instead of command
```
gcc main.c @options-file
```	
- to know more options to stop at spesific stage not complete compilation process
```
gcc --helper
```
- to make static liberary[my codeexample]()
```
$ gcc -c lib.c //will create lib.o
$ ar rcs lib.a lib.o lib2.o ..
```