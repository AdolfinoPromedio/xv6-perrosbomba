1-  Se pone #Define SYS_getprocs  en syscall.h 
2-  Se pone  [SYS_getprocs]   sys_getprocs, en syscall.c
3-  Se pone  extern int sys_getprocs(void); en syscall.c

4- Se coloca Sysproc.c:
        int sys_getprocs(void){
      return getprocs();
    }

5-  Se agrega la funcion  en proc.c:
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

6- En defs.h coloca :
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

7-En user.h coloca int getprocs(void);:
  // system calls
      int fork(void);
      int exit(void) __attribute__((noreturn));
      .
      .
      .
      int getprocs(void);
8-En usys.S  en pone SYSCALL(getprocs)
  SYSCALL(fork)
  SYSCALL(exit)
  SYSCALL(wait)
  .
  .
  .
  SYSCALL(getprocs)      

9-  crea cuenta.c y coloca el codigo:
  #include "types.h"
  #include "stat.h"
  #include "user.h"

  int main(void) {
    printf(1, "La cantidad de procesos en ejecucion en la CPU es %d\n", getprocs());
    exit();
  }
10- modificar Makefile :
  _UPROGS=\
  ...
  _cuenta\

  EXTRA=\
  ...
  cuenta.c\
