---
title: "Searching through data"
teaching: 20
exercises: 15
questions:
- "How to search in parallel"
objectives:
- "Use general parallel sections"
- "Have a single thread execute code"
- "Use a reduction operator"
keypoints:
- "Reduction operators handle the common case of summation, and analogous operations"
- "OpenMP can manage general parallel sections"
- "You can use 'pragma omp single' to have a single thread execute something"
---

So far, we have looked at parallelizing loops. OpenMP also allows you to use general parallel sections where code blocks are executed by all of the available threads. In these cases, it is up to you as a programmer to manage what work gets done by each thread. A basic example would look like the following.

~~~
#include <stdio.h>
#include <stdlib.h>
#include <omp.h>

int main(int argc, char **argv) {
   int id, size;
   #pragma omp parallel private(id,size)
   {
      size = omp_get_num_threads();
      id = omp_get_thread_num();
      printf("This is thread %d of %d\n", id, size);
   }
}
~~~
{: .source}

> ## Private variables
> What happens if you forget the private keyword?
{: .challenge}

Using this as a starting point, we could use this code to have each available thread do something interesting. For example, we could write the text out to a series of individual files.

## Single threaded function

There are times when you may need to drop out of a parallel section in order to have a single one of the threads executing some code. There is a keyword available, 'single', that allows the first thread to see it to execute some single code chunk.

> ## Which thread runs first?
> Modify the following code to find out which thread gets to run first in the parallel section.
> You should be able to do it by adding only one line.
> Here's a (rather dense) reference guide in which you can look for ideas:
> <a href="https://www.openmp.org/wp-content/uploads/OpenMP-4.5-1115-CPP-web.pdf">Directives and Constructs for C/C++</a>.
> 
> ~~~
> #include <stdio.h>
> #include <stdlib.h>
> #include <omp.h>
>
> int main(int argc, char **argv) {
>    int id, size;
>    #pragma omp parallel private(id,size)
>    {
>       size = omp_get_num_threads();
>       id = omp_get_thread_num();
>       printf("This thread %d of %d is first\n", id, size);
>    }
> }
> ~~~
> {: .source}
>
> > ## Solution
> > ~~~
> > #include <stdio.h>
> > #include <stdlib.h>
> > #include <omp.h>
> > 
> > int main(int argc, char **argv) {
> >    int id, size;
> >    #pragma omp parallel private(id,size)
> >    {
> >       size = omp_get_num_threads();
> >       id = omp_get_thread_num();
> >       #pragma omp single
> >       printf("This thread %d of %d is first\n", id, size);
> >    }
> > }
> > ~~~
> > {: .source}
> {: .solution}
{: .challenge}

## Searching

Let's say that we need to search through an array to find the largest value. How could we do this type of search in parallel? Let's begin with the serial version.

~~~
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char **argv) {
   int size = 10000;
   float rand_nums[size];
   int i;
   float curr_max;
   for (i=0; i<size; i++) {
      rand_nums[i] = rand();
   }
   curr_max = 0.0;
   for (i=0; i<size; i++) {
      if (curr_max < rand_nums[i]) {
         curr_max = rand_nums[i];
      }
   }
   printf("Max value is %f\n", curr_max);
}
~~~
{: .source}

The first stab would be to make the for loop a parallel for loop. You would
want to make sure that each thread had a private copy of the 'curr_max'
variable, since it will be written to. But, how do you find out which thread
has the largest value?


> ## Reduction Operators
> You could create an array of `curr_maxes`, but getting that to work right
> would be messy--- How do you adapt to different NUM_THREADS?
> 
> The keys here are
> 1) to recognize the analogy with the problem of `total` from the last episode, and
> 2) to know about *reduction variables*.
>
> A reduction variable is used to accumulate some value over parallel threads,
> like a sum, a global maximum, a global minimum, etc. 
> The reduction operators that you can use are: +, *, -, &, |, ^, &&, ||, max, min.
> 
> ~~~
> #include <stdio.h>
> #include <stdlib.h>
>
> int main(int argc, char **argv) {
>    int size = 10000;
>    float rand_nums[size];
>    int i;
>    float curr_max;
>    for (i=0; i<size; i++) {
>       rand_nums[i] = rand();
>    }
>    curr_max = 0.0;
>    #pragma omp parallel for reduction(max:curr_max)
>    for (i=0; i<size; i++) {
>       if (curr_max < rand_nums[i]) {
>          curr_max = rand_nums[i];
>       }
>    }
>    printf("Max value is %f\n", curr_max);
> }
> ~~~
> {: .source}
{: .solution}

