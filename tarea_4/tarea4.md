# Pasos para la tarea 4: 

Agregar una nueva llamada de sistema en XV6 que, dada una dirección virtual, retorne la dirección física, o diga que la dirección no es válida.

## Paso 1: Modificar el archivo syscall.h

Agregar la siguiente línea al final del archivo:
```
#define SYS_v2p    22
```

## Paso 2: Modificar el archivo syscall.c

Agregar la siguiente línea luego de int argstr:
```
extern int sys_v2p(void);
```
Agregar la siguiente línea en static int
```
[SYS_v2p]    sys_v2p,
```

## Paso 3: Modificar el archivo sysproc.c:

Añadir al final del archivo:
```
int
sys_v2p(void)
{
  int vaddr;
  if (argint(0, &vaddr) < 0) // Obtiene la dirección virtual como argumento
    return -1;

  int paddr = V2P((void*)vaddr); // Llama a la función v2p para convertir la dirección virtual en física
  return paddr;
}
```

## Paso 4: Modificar el archivo usys.S

Añadir al final del archivo:
```
SYSCALL(v2p)
```

## Paso 5: Modificar el archivo user.h

Añadir en system calls:
```
int v2p(void*);
```

## Paso 6: Modificar el archivo vm.c

Añadir al inicio del archivo:
```
int
v2p(void *vaddr)
{
  pde_t *pgdir = myproc()->pgdir;
  uint va = (uint)vaddr;

  // Verifica si la dirección virtual es válida
  if(va >= KERNBASE)
    return -1;

  // Realiza la traducción de dirección virtual a dirección física
  pde_t *pde = &pgdir[PDX(va)];
  pte_t *pgtab;

  if(!(*pde & PTE_P))
    return -1;

  pgtab = (pte_t*)P2V(PTE_ADDR(*pde));
  pte_t *pte = &pgtab[PTX(va)];

  if(!(*pte & PTE_P))
    return -1;

  uint pa = PTE_ADDR(*pte) | (va & 0xFFF);
  return pa;
}
```

## Paso 7: Crear el archivo v2p.c

```
#include "types.h"
#include "stat.h"
#include "user.h"

int
main(int argc, char *argv[])
{
  if (argc != 2) {
    printf(2, "Usage: v2p <virtual_address>\n");
    exit();
  }

  int vaddr = atoi(argv[1]);
  int paddr = v2p((void*)vaddr);

  if (paddr == -1)
    printf(2, "Invalid virtual address\n");
  else
    printf(1, "Physical address for virtual address 0x%x: 0x%x\n", vaddr, paddr);

  exit();
}
```

## Paso 8: Modificar el archivo Makefile

Añadir en UPROGS:
```
_v2p\
```
Añadir en EXTRA:
```
v2p.c\
```

## Paso 9: Compilar y ejecutar
```
make qemu-nox
```

## Paso 10: Ingresar dirección

Por ejemplo:
```
v2p 0x1000
```
Referencias:
- https://medium.com/@mahi12/adding-system-call-in-xv6-a5468ce1b463
- https://github.com/CelestialZeus/Virtual_To_Physical_Memory_Translator/blob/master/README.md
- https://xiayingp.gitbook.io/build_a_os/virtual-memory/untitled-1/how-to-display-physical-memory
- https://xiayingp.gitbook.io/build_a_os/virtual-memory/xv6-virtual-memory
- https://github.com/yspkm/xv6-virtual-memory
- https://github.com/zarif98sjs/xv6-memory-management-walkthrough

