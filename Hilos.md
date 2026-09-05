Como los procesos, los hilos son mecanismos que permiten a un programa hacer más de una acción a la vez. Los corren concurrentemente, y el kernel de Linux se encarga de agendar y organizarlos de forma asíncrona, interrumpiendo el tiempo de CPU de un hilo para darle un turno de ejecución a otro.
En un programa, los procesos pueden hacer fork() y crear un proceso hijo. Ambos, padre e hijo, son procesos independientes. El hijo hereda una copia exacta de la memoria del padre, puede modificarla y no afectará al padre.  En cuanto a los *hilos*, tanto el hilo creador como creado comparten un espacio de memoria, con todos los recursos de memoria.  Si algún hilo del proceso modifica la memoria, todos los demás hilos se ven afectados por ese cambio. 
Un proceso siempre tiene un **hilo principal** que ejecuta el programa secuencialmente. De ese hilo nacen otros hilos que ejecutan diferentes partes del programa. El KERNEL de Linux es el "agenda/organiza" la ejecución de los hilos de forma asíncrona.

# Cómo crear un hilo:
Un hilo es identificado por su ID. Se debe usar el tipo de dato `pthread_t` para crear la variable con el ID.
La función `pthread_t` crea un nuevo hilo. Se deben proveer los parámetros:
1. Un puntero a la variable `pthread_t`, que almacena el ID retornado por la función.
2. Un puntero a el objeto `thread attribute`, que controla el comportamiento del hilo en el proceso. El parámetro `NULL` asigna atributos por defecto.
3. Un puntero a la _función hilo_, que es una función ordinaria que correrá en el nuevo hilo. 
4. Un argumento para la _función hilo_.

La llamada a la función creadora retorna de inmediato y el hilo principal sigue corriendo secuencialmente.

![[Pasted image 20260904214530.png]]

La funciín `pthread_self` retorna el ID del hilo que la llama. 
Se pueden comparar IDs con `pthread_equal`.
## Unir hilos:
Una solución es forzando al main a que un espere a que un hilo termine. La función `pthread_join` es similar a wait(). Recibe como parámetro:
1. El ID del hilo que espera.
2. Un puntero void* de una variable que recibe el valor de retorno del hilo. NULL si no tiene retorno. El valor de retorno también es void*. 

>[!warning] Si un hilo lo usa en sí mismo, retorna el error `EDEADLK`
## Atributos de un Hilo:
- **Joinable:** Un hilo que una vez terminado solo se limpia haciendo una llamada a `pthread_join` para recibir el valor de retorno. Si no, queda como un proceso zombie. 
- **Detached:** Se limpia automáticamente por el sistema al terminar. Para definir este atributo en un hilo: `pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_DETACHED).`

>[!warning] `pthread_detach()`convierte un hilo joinable en detached en cualquier momento — pero no al revés.

# Cancelar un hilo:
Normalmente el hilo termina cuando la función retorna. Si se quiere terminar un hilo, la función `pthread_exit` termina el hilo. Su único argumento será el valor de retorno. 
Para pedirle a un hilo que termine, pasa la ID del hilo a `pthread_cancel`. Un hilo cancelado sigue siendo joinable para ser limpiado. Su valor de retorno es `PTHREAD_CANCELED`. Para evitar fugas de memoria, un hilo puede controlar ser cancelado y cuando.  
**Estados de Cancelación de un Hilo:** 
- *Asíncrono cancelable:* Se puede cancelar en cualquier momento.
- *Síncrono cancelable:* Puede ser cancelado, pero en un punto específico.
- *Incancelable:* Los intentos son ignorados.
**Sección crítica no cancelable:**
Un hilo puede deshabilitar la cancelación de sí mismo por completo con la función `pthread_setcancelstate`. 

# Datos específicos de un hilo:
Los hilos comparten memoria y pueden trabajar en conjunto sin comunicación extra. Cada hilo tiene su propia *pila de llamadas*, que permite a cada hilo ejecutar diferentes códigos y retornar de sus subrutinas. A veces, cada hilo necesita su propia sección local de memoria *área de hilo-especifíco*, por ejemplo un archivo log. `pthread_key_create()` crea una clave compartida a todos los hilos, recibe un puntero a una variable `pthread_key_t` . Cada hilo lee o escribe su copia con `pthread_get(set)specific()`.   
El segundo argumento son funciones **Clean-up Handlers**, que limpian la memoria local de un hilo cuando este termina. Se registra con `pthread_cleanup_push()` y se retira con `pthread_cleanup_pop()`.
# Secciones Criticas:
Una *sección crítica* es una secuencia de código que debe ejecutarse en su totalidad o no ejecutarse en absoluto; en otras palabras, si un hilo comienza a ejecutar la sección crítica, debe continuar hasta el final de la sección crítica sin ser cancelado.
Como los hilos comparten memoria, varios hilos pueden escribir a la vez sobre una misma estructura. Para evitar ese caos, se utilizan: 
## Condiciones de Carrera:
Suponga que un programa tiene una lista de trabajos a ser procesados., puede ocurrir que dos hilos tomen el mismo trabajo. Para eliminar estas situaciones, se necesitan operaciones atómicas, que son operaciones ininterrumpibles. Una vez que comienzan, se deben terminar y no dará lugar a ninguna otra operación.
## Mutexes (MUTual EXclutions):
La solución a la condición de carrera es permitir que solo un hilo acceda a la cola de trabajos a la vez. Una vez que un hilo comience a mirar una cola, ningún otro hilo puede acceder hasta que este haya decido tomar un trabajo y lo elimine de la cola.
Un mutex es un bloqueo especial que solo un hilo puede bloquear a la vez. Si un hilo bloquea un mutex y luego un segundo hilo también intenta bloquear el mismo mutex, el segundo hilo se bloquea o se pone en espera. Solo cuando el primer hilo desbloquea el mutex, el segundo hilo se desbloquea, es decir, se le permite reanudar su ejecución.
Para crear un mutex, cree una variable de tipo `pthread_mutex_t`y pase un puntero a ella a `pthread_mutex_init`. El segundo argumento es un puntero a un objeto de atributo de mutex.`PTHREAD_MUTEX_INITIALIZER` inicializa un mutex global sin llamar a la función init.

![[Pasted image 20260904235053.png]]
Todos los accesos a job_queue, el puntero de datos compartido, vienen entre la llamada a `pthread_mutex_lock`y la llamada a `pthread_mutex_unlock`.

## Tipos de Mutexes:
- **Fast Mutexes:**  Produce un interbloqueo. Un intento de bloquear el mutex se bloquea hasta que el mutex se desbloquea. Pero debido a que el hilo que bloqueó el mutex está bloqueado en el mismo mutex, el bloqueo nunca podrá liberarse.
- **Recursive Mutex:** No causa un interbloqueo. Un mutex recursivo puede ser bloqueado de manera segura muchas veces por el mismo hilo. El mutex recuerda cuántas veces el hilo que retiene el bloqueo llamó a `pthread_mutex_lock` sobre él; ese hilo debe hacer la misma cantidad de llamadas a `pthread_mutex_unlock` antes de que el mutex se desbloquee realmente y se le permita a otro hilo bloquearlo.
- **Error Checking:** Linux detectará y señalará un doble bloqueo en un mutex de comprobación de errores que de lo contrario causaría un interbloqueo. La segunda llamada consecutiva a `pthread_mutex_lock`devuelve el código de fallo `EDEADLK`.

>[!tip] 
>`pthread_mutex_trylock()` intenta tomar el mutex sin esperar: si está ocupado devuelve EBUSY de inmediato, en vez de bloquear al hilo.
### Deadlocks:
Los mutexes proporcionan un mecanismo para permitir que un hilo bloquee la ejecución de otro. Esto abre la posibilidad de una nueva clase de errores, llamados interbloqueos (deadlocks). Un interbloqueo ocurre cuando uno o más hilos se atascan esperando que suceda algo que nunca ocurrirá.
## Semáforos:
Un semáforo es un contador que se puede utilizar para sincronizar varios hilos. Al igual que con un mutex, GNU/Linux garantiza que verificar o modificar el valor de un semáforo se puede hacer de manera segura, sin crear una condición de carrera. Cada semáforo tiene un valor de contador, que es un número entero no negativo. Un semáforo admite dos operaciones básicas:
- **sem_wait():** Antes de tomar un trabajo de la cola, el hilo espera. La función decrementa el contador; si ya es cero, bloquea hasta que otro hilo haga sem_post().
- **sem_post():** incrementa el contador y despierta a un hilo bloqueado, si hay alguno.
Un semaforo se representa con `sem_t`, se debe inicializar con `sem_init(&sem_t,0,value)`. 
Si ya necesita semáforo: `sem_destroy`.
## Variables Condicionales:
Es una sincronización más compleja. Permite implementar una condición bajo la cual un hilo se ejecuta y se bloquea. Permiten que un hilo espere hasta que otro le avise que una condición cambió. Siempre se usan junto a un mutex, para evitar una condición de carrera entre 'revisar' y 'esperar'. 
Al igual que con un semáforo, un hilo puede esperar en una variable de condición. Si el hilo A espera en una variable de condición, se bloquea hasta que otro hilo, el hilo B, señale la misma variable de condición. A diferencia de un semáforo, una variable de condición no tiene contador ni memoria; el hilo A debe esperar en la variable de condición antes de que el hilo B la señale. Si el hilo B señala la variable de condición antes de que el hilo A espere en ella, la señal se pierde y el hilo A se bloquea hasta que otro hilo vuelva a señalar la variable de condición.
![[Pasted image 20260905002601.png]]
### Deadlock entre múltiples hilos:
Ocurre cuando dos o más hilos esperan, cada uno, algo que solo el otro puede provocar.
El hilo A: Toma Mutex 1, después pide Mutex 2 — queda bloqueado.
El hilo B: Toma Mutex 2, después pide Mutex 1 — queda bloqueado.
Ambos quedan bloqueados en un mutex que tomó el otro, por lo que nunca se enviarán una señal para liberar el mutex.
**Solución:** Todos los hilos deben bloquear los mismos recursos siempre en el mismo orden — no solo Mutexes: también archivos o dispositivos.

### Manejo de señales:
Debido a que cada hilo es un proceso separado, y que una señal se entrega a un proceso particular, las señales son enviadas desde fuera del programa al hilo principal del programa.
Si un programa hace un fork() y el proceso hijo realiza un exec() a un programa multihilo, el proceso padre retendrá el ID de proceso del hilo principal del proceso hijo y usará ese ID de proceso para enviar señales a su hijo.
### Llamada al Sistema: Clone.
Esta llamada es como un fork() y pthread_create() pero que permite especificar qué recursos se compartirán entre le proceso creador y creado. Clone requiere especificar la región de memoria en donde se guarda la pila de ejecución que utilizará el nuevo proceso. 
# Procesos vs. Hilos:
Reglas para decidir si utilizar un proceso o hilo:
- Los hilos de un proceso corren en el mismo ejecutable. Un proceso hijo corre en un ejecutable diferente.
- Un hilo defectuoso puede afectar el funcionamiento de otros hilos al compartir una memoria virtual.
- Copiar la memoria de un proceso requiere más trabajo que crear un nuevo hilo. A excepción de cuando el proceso hijo solo leerá la memoria.
- Los hilos se usan cuando se requiere un paralelismo fino. Por ejemplo, cuando corren funciones muy similares.
- Compartir memoria entre hilos puede ser tanto una ventaja como desventaja, requiere de mucho cuidado ante situaciones como condiciones de carrera.
- Compartir memoria entre hilos requiere de mecanismos IPC, que pueden ser incómodos pero menos propensos a sufrir errores.