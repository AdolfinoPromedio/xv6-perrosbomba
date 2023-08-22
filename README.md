# Tarea 1 - Sistemas Operativos

1. Se agrega la siguiente línea de código en syscall.h: 
```C
#Define SYS_getprocs
```

2. Se agrega la siguiente línea de código en syscall.c:
```C
[SYS_getprocs]   sys_getprocs,
```

3. Se agrega la siguiente línea de código en syscall.c:
```C
extern int sys_getprocs(void);
```

4. Se agrega la siguiente línea de código en sysproc.c:
```C
int sys_getprocs(void){
        return getprocs();
}
```

5. Se agrega la siguiente línea de código en proc.c:
```C
int sys_getprocs(void) {
        struct proc *p;
        int count = 0;

        acquire(&ptable.lock);
        for (p = ptable.proc; p < &ptable.proc[NPROC]; p++) {
          if (p->state != UNUSED) {
            count++;
          }
        }
        release(&ptable.lock);

        return count;
} 
```

6. Se agrega la siguiente línea de código en defs.h:
```C
  // proc.c
  .  
  .
  .
  int             getprocs(void);

  // syscall.c
  int             argint(int, int);
  int             argptr(int, char, int);
  .
  .
  .
  int             getprocs(void);
```

7. Se agrega la siguiente línea de código en user.h:
```C
  // system calls
      int fork(void);
      int exit(void) __attribute__((noreturn));
      .
      .
      .
      int getprocs(void);
```

8. Se agrega la siguiente línea de código en usys.S:
```C
  SYSCALL(fork)
  SYSCALL(exit)
  SYSCALL(wait)
  .
  .
  .
  SYSCALL(getprocs)  
```    

9. Se crea el archivo cuenta.c y se agrega el siguiente código:
```C
  #include "types.h"
  #include "stat.h"
  #include "user.h"

  int main(void) {
    printf(1, "La cantidad de procesos en ejecucion en la CPU es %d\n", getprocs());
    exit();
  }
```   

10. Se modifica el Makefile, agregando las siguientes líneas de código:
```C
  _UPROGS=\
  ...
  _cuenta\

  EXTRA=\
  ...
  cuenta.c\
```   
---
Fuentes:
1. https://www.youtube.com/watch?v=21SVYiKhcwM
2. https://stackoverflow.com/questions/57745250/how-to-add-a-system-call-to-find-the-number-of-processes-in-xv6
3. https://stackoverflow.com/questions/8021774/how-do-i-add-a-system-call-utility-in-xv6
4. https://www.geeksforgeeks.org/xv6-operating-system-adding-a-new-system-call/
