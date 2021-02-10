# Lab1 PSO materials
Index
1. handout go through
1. process context switch
1. readylist and queue
1. timer interrupt
1. float-point operations, XINU and fixed-point library

</br>
</br>

------------------------------------------
</br>

### 1. Handout go through
1. Aging Scheduler [20pt]
1. Proportional Share Scheduler [50pt]
1. Multilevel Feedback Queueu Scheduler [70pt]
1. Process owernership [20pt]

[lab1 handout](https://www.cs.purdue.edu/homes/pfonseca/teaching/cs503/21spring/labs/lab1.html)


reminder: 
1. **Required**: Do **NOT** *define/initialize* new function or variables in `main.c`. Instead, you could *define/initialize* in the beginning of *initialized.c*, and *declare* them in *prototype.h*.
1. **Recommended**: you can use `XDEBUG_KPRINTF` function for your own debug kprint purpose, use 'XTEST_KPRINTF' function for the handout specified kprint purpose. The definitions of the above functions are under *./include/process.h*

</br>
</br>

-----------------------------------------

</br>

### 2. Context-Switch and `rescued()`


![processes ctxsw example](https://raw.githubusercontent.com/ProbShin/myCS503ProjectsRepo/main/PSO/lab1/img1.png)



```
process P1(){
  int i=0;
  while(1) {
     i++;
  }
  return OK;
}


process P2(){
  int x=0;
  while(true){
     x++;
  }
  return OK
}
```

</br>
</br>

-----------------------------------------

</br>

### 3. Readylist

XINU store all the `PR_READY` processes in a priority-queue data structure called **readylist**. It utilizes XINU's queue system. 
You need to implement your own operation on this data structure. i.e. `traverse`, `insert`, `remove`.

```
# An example of double linked list, (Note, this is not XINU used)
struct node {
  int    val;
  struct node*  prev;
  struct node*  next
}
```

```
# priority-queue operation demo
Head <-> 30 <-> 20 <-> 20 <-> 10 <-> Tail 
         |      |       |      |
         p1     p2      p3     p4
        

Demo: insert 40 (p5); insert 5 (p6); insert 20 (p7);

Head <-> 40 <-> 30 <-> 20 <-> 20 <-> 20 <-> 10 <-> 5 <-> Tail 
          |     |     |      |      |     |     |
         p5    p1     p2     p3     p7    p4    p6
```


The XINU's readylist related implementations.
* data structure and marco functions in *queue.h*
* `insert` in *insert.c*
* `dequeue` in *queue.c*


</br>
</br>

-----------------------------------------

</br>

### 4. Queue
XINU's queue system. Preassigned space for a **Head** and a **Tail** for per queue using table to implement double-linked-list.
* `dequeue`, `enqueue` in *queue.c*
* data structure and marco functions in *queue.h*
* assigned space in *initialize.c*

```
# each row of the table served as an node.
# each process assigned a node.
# each queue assigned two nodes (Head, tail)

      
rowNo.|    Key    | nxt | pre |
      |-----------------------|
              ...
      |   ...     |     |     |
      |-----------------------|
  4   |   10      |  q  |  5  |
      |-----------------------|
  5   |   20      | q+1 |  4  |
      |-----------------------|
      |           |     |     |
      |-----------------------|
      |           |     |     |
      |-----------------------|
  q   |    Head   | nxt | pre |    \
      |-----------------------|     |-> readylist
 q+1  |    Tail   | nxt | pre |    /
      |-----------------------|
      |           |     |     |
       ...
```

This example shows that there are only two process P4 and P5 which have priority 10 and 20 in the *readylist*.



</br>
</br>

**Traverse the queue**

```
# pseudo code, not the real code.
for i = Head.qnext; i != Tail; i = queuetab[ i ].qnext :
    print "process pid = %d\n"%i; # process node here
```

</br>
</br>


<!--
------------------------------------------------

</br>

### 4.1 Double-linked-list
basic operations of double-linked-list.
* `traverse`
* `insert`
* `remove`

</br>
</br>

-->
-----------------------------------------

</br>

### 5. Timer Interrupt 
* clkhandler.c
* clkinit.c
* clkdisp.S

`clkhandler()` by default would be triggered per 1ms. It would call `resched()` to "context-switch" processes depend on the `QUANTUM`.

</br>
</br>

-----------------------------------------

</br>

### 6. Float-point operations, XINU and Fixed-point library 

* Float-point operations are not unit operations which means they need special treatment. 
* Fixed-point library uses integer operations instead.
  - demo how to download, read and execute.
  - under XINU
      read `system/main.c` and `system/fix16test.c`
  i.e. to do `load_avg := (59/60) * load_avg + (1/60) * n_ready_processes` 
```
    int n_ready_processes = 3;
    fix16_t load_avg = fix16_from_int(0);
    
    load_avg = fix16_div( 
                    fix16_add( 
                          fix16_mul(fix16_from_int(59), load_avg)
                          , fix16_from_int(n_ready_processes) )
                     , fix16_from_int(60)
                );
    
    fix16_to_str(load_avg,output,6);
    char output[128];
    kprintf("load_avg = %s\n", output);
```
  
------------------------------------

Materials 
* fix16.tgz source code
* xinu *system/main.c* and *system/fix16test.c*
</br>
</br>

