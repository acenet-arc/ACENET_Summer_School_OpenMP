---
title: "Introduction"
teaching: 30
exercises: 0
questions:
- "What is shared-memory programming?"
- "What is OpenMP?"
objectives:
- "Understand the shared-memory programming model"
- "Know that OpenMP is a standard"
keypoints:
- "OpenMP programs are limited to a single physical machine"
- "You use OpenMP with C, C++, or Fortran"
---

Parallel programs come in two broad flavors: shared-memory and message-passing. In this workshop, we will be looking at shared-memory programming, with a focus on OpenMP programming. 

What is shared-memory programming? In any parallel program, the general idea is to have multiple threads of execution so that you can break up your problem and have each thread handle one part. These multiple threads need to be able to communicate with each other as your program runs. In a shared-memory program, this communication happens through the use of global variables stored in the global memory of the local machine. This means that communication between the various threads is extremely fast, as it happens at the speed of RAM access. But your program will be limited to a single physical machine, since all threads need to be able to see the same RAM.

OpenMP is one way of writing shared-memory parallel programs. OpenMP is actually a specification, which has been implemented by many interested groups. 

> <a href="https://www.openmp.org/specifications/">OpenMP specifications</a>
{: .callout}

The standard describes extensions to a C/C++ or FORTRAN compiler. This means that you need use a compiler that supports OpenMP. Unfortunately, there have been several versions of the OpenMP specification, and several sections of it are optional, so it is up to the programmer to investigate the compiler they want to use and see if it supports the parts of OpenMP that they wish to use. Luckily, the vast majority of OpenMP behaves the way you expect it to with most modern compilers. When possible, we will try and highlight any odd behaviors.

Since OpenMP is meant to be used with either C/C++ or FORTRAN, you will need to know how to work with at least one of these languages. This workshop will use C as the language for the examples. As a reminder, a simple hello world program in C would look like the following.

~~~
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char **argv) {
   printf("Hello World\n");
}
~~~
{: .language-c}

In order to compile this code, you would need to use the following command:

~~~
gcc -o hello_world hello_world.c
~~~
{: .language-bash }

This gives you an executable file that will print out the text "Hello World". You can do this with the command:

~~~
./hello_world
~~~
{: .language-bash }

~~~
Hello World
~~~
{: .output }

># GCC on Compute Canada
>
> The default environment on the general purpose clusters (Beluga, Cedar, Graham) includes a gcc compiler. Not all systems are set up this way, though. On Niagara, for instance, the default environment is very minimal and you must load a module explicitly to access any compiler: 
>
> ~~~
> $ module load gcc
> $ gcc --version
> gcc (GCC) 7.3.0
> ~~~
> {: .language-bash }
{: callout}
