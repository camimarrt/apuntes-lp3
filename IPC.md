# Inter Process Comunication.

Guía rápida para ejercicios.
## 1. Memoria Mapeada:
Varios programas comparten un espacio de memoria en el kernel del sistema operativo.
 **Estructura:**
 
 >[!warning]
 >1. `shmget(clave, tamaño, permisos)` --> Reservar / Obtener el ID del segmento en el Kernel
 >
>2. `shmat(id, NULL, 0)`   --> Mapear / Adjuntar el bloque de RAM a nuestro puntero
   >/*LEER O ESCRIBIR EN LA MEMORIA COMPARTIDA (Tantas veces como sea necesario)*/
>
> 3. `shmdt(puntero) `  --> Desadjuntar el puntero de nuestro proceso
> 
> 4. `shmctl(id, IPC_RMID, NULL)`  --> Destruir el segmento en el Kernel (solo cuando NINGÚN proceso lo necesite.


## 2. Semáforos de Proceso:
Son mecanismos del kernel para sincronizar y coordinar el acceso de varios procesos a un recurso compartido.
>[!warning]
>1. `semget(clave, num_semaforos, permisos)`: Crear u obtener el ID del conjunto de semáforos en el Kernel
>2. `semctl(semid, num_sem, orden, ...)` : Inicializar el valor de cada semáforo (p. ej. SETVAL = 1)
   /*ESPERAR O AVISAR (Tantas veces como se acceda al recurso compartido)*/
   >3. `semop(semid, &operacion, num_ops)`: Ejecuta de forma atómica una o más operaciones de decremento (espera) o incremento (aviso) sobre el semáforo mediante la estructura `struct sembuf`.
   > - semop() con op < 0  --> WAIT / P()   (Restar 1: se bloquea si el semáforo es 0)
   >- semop() con op > 0  --> SIGNAL / V() (Sumar 1: incrementa y despierta a otros)
> 1. `semctl(semid, &operacion, num_ops)`: Destruir el semáforo en el Kernel (IPC_RMID) al finalizar todo el sistema.

## 3. Memoria Mapeada:
Asocia un archivo del disco duro directamente con una región de memoria RAM en el espacio de direcciones de tu proceso. Al leer o escribir en ese puntero de memoria, estás leyendo o escribiendo automáticamente el archivo en el disco sin necesidad de usar `read()` o `write()`.

>[!warning] 
>1. `mmap(addr, length, prot, flags,  fd, offset)`: Vincular el archivo a un puntero de memoria RAM
   >/* LEER O ESCRIBIR DIRECTAMENTE EN EL PUNTERO (Tantas veces como sea necesario) */ 
>2. `msync()`    --> (Opcional) Forzar el volcado/sincronización de los datos al disco 
>3. `munmap()`   --> Liberar el mapeo de memoria cuando se termina de usar 
### Modos Principales (`flags`)
- **MAP_SHARED:** Los cambios realizados en la memoria se guardan automáticamente en el archivo en disco y los ven en tiempo real los demás procesos que mapeen el mismo archivo (es el modo usado para IPC).
- **MAP_PRIVATE:** Usa _Copy-on-Write_. Los cambios hechos por el proceso quedan en una copia privada en la RAM y no se escriben en el archivo.
## 4. Pipes:
Existe únicamente en la memoria RAM gestionada por el kernel. Como no tiene un nombre en el sistema de archivos, **solo sirve para comunicar procesos emparentados** (padre e hijo creados con `fork()`)

>[!warning]
>1. `pipe(fds)`    --> Crear la tubería en RAM (obtiene fds[0] lectura, fds[1] escritura)
>2. `fork()`       --> Duplicar el proceso (el hijo hereda los descriptores fds)
   /* CADA PROCESO CIERRA EL EXTREMO QUE NO VA A USAR*/
   /* COMUNICACIÓN*/
>3. `close()`      --> Cerrar el extremo activo restante en cada proceso al terminar
## 5. FIFOs:
Es una pipe que posee un nombre en el sistema de archivos (se crea como un archivo especial `p`). Esto permite que **dos procesos completamente independientes** (sin parentesco) puedan comunicarse simplemente abriendo el archivo con `open()`.

>[!warning]
>1. `mkfifo(ruta, permisos)` --> Crear el archivo FIFO en el sistema de archivos (una sola vez)
> /*PROCESO A (Escritor)  open(ruta, O_WRONLY)*/               
>  /*PROCESO B (Lector) open(ruta, O_RDONLY)*/
2a. `write(fd, buffer, tam) `              2b. `read(fd, buffer, tam)`
3a.`close(fd) `                           3b. `close(fd)`

## 6. Sockets:
Un **socket** es un mecanismo de comunicación bidireccional que permite la transferencia de datos entre dos procesos, ya sea que se ejecuten en la **misma máquina** o en **computadoras distintas a través de una red**.

**Estructura del Servidor:**
>[!warning] 
>1. `socket(dominio, tipo, protocolo)` --> Crear el punto de conexión inicial en el kernel 
> 2. `bind(fd_socket, &direccion, tamaño)` --> Enlazar el socket a una dirección (IP/Puerto o archivo) 
>3. `listen(fd_socket, cola_espera)` --> Habilitar el socket para escuchar conexiones de clientes 
>4. `accept(fd_socket, &direccion, &tamaño)` --> Bloquear hasta recibir un cliente y obtener un NUEVO socket dedicado 
   >/ENVIAR O RECIBIR DATOS/
   `read(cfd, buffer, tamaño) / write(cfd, mensaje, tamaño) `
>5. `close(cfd)`--> Cerrar el socket de comunicación con el cliente 
>6. `close(fd_socket) `--> Cerrar el socket principal de escucha al apagar el servidor 

**Estructura del Cliente:** 
>[!warning]
>1. `socket(dominio, tipo, protocolo)` --> Crear la toma de red 
>2. `connect(fd_socket, &direccion_servidor, tamaño) `--> Solicitar conexión con el servidor 
   /ENVIAR O RECIBIR DATOS/ 
   `write(fd_socket, mensaje, tamaño) / read(fd_socket, buffer, tamaño)` 
>3. `close(fd_socket)` --> Cerrar la conexión 

