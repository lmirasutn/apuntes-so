# Procesos

Un **proceso** es un código en ejecución, una entidad activa, mientras que el programa una entidad pasiva.

- **Concurrencia**: en un intervalo de tiempo se ejecutan varias tareas. Un núcleo del procesador ejecuta **solamente** un proceso a la vez; en concurrencia, las tareas se van alternando muy rápido, lo que da la sensación de simultaneidad.

- **Paralelismo**: El paralelismo implica concurrencia, pero ahora sí hay simultaneidad, igual al número de núcleos del procesador.

---

El proceso, como entidad activa, tiene una estructura llamada imagen del proceso para administrarlo:

- **Heap**: hace referencia a la memoria dinámica (pedir memoria y liberarla).
- **Stack**: memoria estática.
- **Datos**: variables globales y estáticas (`static int acumulador = 20;`).
- **Variables**: locales, normales. Se guarda en el stack y desaparecen al terminar la función.

Entonces, el proceso es dinámico, crece y decrece constantemente.

---

## PCB: Process Control Block

### Posee la información necesaria para que el SO administre el proceso, guarde todo el contexto de ejecución del proceso, todos sus registros. Se encuentra siempre cargado en RAM.

- ID
- Estado del proceso
- Program Counter / Instruction Pointer
- Registros de la CPU
- Información de planificación
- Información de gestión de memoria
- Información contable
- Información de estado de E/S
- Punteros

---

## Estados de un proceso

- **New**: cuando creamos un nuevo proceso pero todavía no está listo para ejecutar.
- **Ready**: listo para ejecutarse (es planificable su ejecución).
- **Waiting**: estado bloqueado a la espera de una interrupción.
- **Running**: en ejecución.
- **Terminated**: finalizado.

## Swapping

Swapping: mover procesos bloqueados temporalmente a disco (el PCB permanece en la RAM) para no ocupar siempre la RAM.

- **SWAP OUT**: suspender el proceso y escribirlo en disco.
- **SWAP IN**: traerlo de vuelta a RAM.

Un proceso en espera es buen candidato a ser "suspendido en espera".  
Cuando el proceso está listo, pasa a "suspendido listo".

---

## Planificadores

### Corto Plazo
Se controla el grado de multiprocesamiento.
- (`ready ↔ exec`) y viceversa
- Controla entre los procesos ready, quien ejecuta, y de los que ejecutan, quienes vuelven a ready.

### Medio Plazo
Controla los procesos suspendidos (`suspended ready ↔ ready`) y viceversa.

### Largo Plazo
Controla la carga de procesos en el sistema, es decir, cuántos procesos hay activos en total (ready,running, waiting y cualquier estado -> exit).

---

## Cambio de Contexto

- Sucede cuando necesitamos ejecutar otro proceso, como por ejemplo, para atender una interrupción(ejecutará el interrupt handler), ejecutar una syscall.
- Cuando se cambia el proceso se debe guardar su contexto de ejecución para luego reanudarlo en el lugar interrumpido (En el PCB).

- El sistema operativo guarda el estado del proceso actual (registros, contador de programa, etc.).
- El tiempo que tarda este cambio se considera **overhead**.

---

## Creación de Procesos

A través de una syscall (`fork()`), un proceso padre puede crear un proceso hijo.

- Cada proceso tiene un **PID** (Process ID) único.
- El proceso padre puede:
  - Continuar ejecutando en paralelo al hijo.
  - Esperar a que el hijo termine (`wait()`).

En la creación por default, el proceso hijo es una copia exacta del padre. Pero se puede asignar una nueva imagen que reemplaza la anterior.

### Ejemplo de fork:
```c
#include <stdio.h>
#include <unistd.h>

int main(int argc, char *argv[]) {
    int fork_result;
    fork_result = fork();
    
    if (fork_result < 0) {
        /* Inicio código de error */
        fprintf(stderr, "Falló");
        exit(-1);
        /* Fin de código de error */
    } 
    else if (fork_result == 0) {
        /* Inicio código de Proceso Hijo */
        execlp("/bin/ls", "ls", NULL);
        // …
        /* Fin código de Proceso Hijo */
    } 
    else {
        /* Inicio código de Proceso Padre */
        // …
        wait(NULL);
        printf("Child Process Complete");
        exit(0);
        // …
        /* Fin código de Proceso Padre */
    }
}
```
## Comunicación entre Procesos (IPC)

### Formas básicas de IPC:

- Memoria compartida: Los procesos establecen una zona de memoria compartida, la cual se usa para comunicarse leyendo y escribiendo datos en la misma.
- Paso de mensajes: Útil para intercambiar pequeñas cantidades de datos a través de sockets. Es más lento ya que requiere cambios de contexto (syscalls).
