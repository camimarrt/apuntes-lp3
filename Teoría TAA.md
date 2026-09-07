# En qué consisten los Procesos Livianos y Pesados? Cuál es la diferencia y el objetivo de las mismas.
Un proceso pesado es una unidad de ejecución independiente que tiene su propio espacio de memoria. Como los procesos. Se utilizan para tareas de aislamiento, independientes entre sí.
Un proceso liviano es una unidad de trabajo más fina que vive dentro de un proceso. Es liviano porque al creado no se crea un nuevo espacio de memoria, sino que comparte el espacio de el proceso que lo creó. Se utilizan para tareas donde existe una dependencia, por ejemplo acceso al mismo recurso.
# Define con tus palabras:
Proceso, Hilo, Programa. 
Un proceso es una instancia de la ejecución de un programa, abstracción de una tarea. Ambos, procesos e hilos, son mecanismos que permiten a un programa hacer más de una tarea a la vez pero se diferencian en un proceso hereda una copia exacta de la memoria virtual del padre y los hilos comparten un espacio de memoria.
Un programa es el archivo que contiene el código fuente que ejecuta un proceso o hilo.
# Cómo un proceso se convierte en un programa?
Compilando el código fuente y ejecutando el código binario creado por el compilador. El proceso es el programa en ejecución.
# Cómo se puede acceder a un entorno de proceso? 

# Explique que hace la función fork().
La función fork() crea un nuevo proceso que hereda una copia de la memoria virtual del padre. Es una bifurcacion, tiene una entra (proceso padre) y dos salidas (padre e hijo). El padre se sigue ejecutando y el hijo continua desde el mismo lugar. Fork() retorna 0 al proceso hijo y el PID del hijo al padre, a modo de que el programa pueda identificar los procesos y separar la lógica que ejecutarán.
# Explique que hace la función exec().
La función exec() hace que un proceso ejecute otra programa. El proceso pasa de ser la instancia de un programa, a ser la instancia de otro. Ejecuta otro programa desde cero. Si tiene éxito, nunca retorna debido a que todo sobre el proceso anterior es descartado (sus datos, pila de ejecucion, etc.) y el CPU no tiene el contexto para saber como retornar al proceso padre.
# Mencione una situación donde conviene más una llamada al sistema exec en vez de fork.

# Realice un pseudocódigo donde se utilice la función wait() y explique su función.
int main () {
	.
	.
	pid_t pid = fork ();
	if (pid == 0) {
		 *codigo para el hijo*
	} else if ( pid > 0) {
		*codigo padre*
		wait();
	}
	.
	.
}
La función es de extrama importancia para no dejar procesos zombies. Un padre siempre tiene que recibir el valor de retorno de sus hijos para que el sistema operativo puede liberar toda la memoria del proceso hijo. 
# Explica para que sirven las señales.
Las señales son mensajes asíncronos que se envían entre procesos o del sistema al proceso. Un proceso no espera a terminar la ejecución para leerlas. Pueden ignorar las señales recibidas o reaccionar y cambiar su comportamiento. 
# Qué es el lifetime de una señal y qué es signal handler?
El lifetime de una señal es su tiempo de vida o línea de tiempo.
- Se manda la señal.
- Es reciba por otro proceso.
- Este proceso decide si reaccionar o ignorar, depende del handler.
El signal handler es una función que define el comportamiento de un proceso antes las señales que recibe.
Se define de esta manera: sigaction(señal, funcion_manejadora, NULL).
sigaction asocia la señal con su handler que es una funcion que se ejecuta cuando recibe X señal.
# Explica signal masks y signal sets:
- Signal sets: Conjuntos de señales que un proceso puede recibir o hilo.
- Signal masks: es la **máscara de bloqueo actual del proceso o del hilo**. Es un conjunto de señales que el Kernel consulta antes de entregar una señal. Si una señal está **en la máscara de bloqueo**: la señal no se descarta; se queda en estado **pendiente** (_pending_). En cuanto el programa desbloquee esa señal, el Kernel entregará la señal pendiente de inmediato.
# En qué consiste una condición de carrera? Dé un ejemplo y mencione como solucionar.
Una condición de carrera es cuando dos o más hilos quieren acceder a una recurso compartido o realizar el mismo trabajo. Una sección o trabajo donde hay que tener precaucion se le llama sección crítica. Puede ocurrir que dos hilos quieran modificar una variable compartido, por ejemplo el dinero de una cuenta bancaria, y si no se cuida el acceso a la línea de codigo donde se hace el descuento del dinero, dos hilos puede tomar el mismo trabajo y descontar dos veces. 
Para solucionar este problema se deben proteger las secciones críticas usando mutexes, semáforos o condiciones de variable que restringen el acceso de un hilo a una seccion de codigo.
# Segmentos de Memoria:
- Código: Contiene el código ejecutabler del programa. Es solo de lectura.
- Variables globales + constantes: 
	- Data: variables globales y estáticas inicializadas.
	- BSS: variables globales y estáticas NO inicializadas.
	- Rodata: Constantes.
- Stack, Pila de llamadas: Gestiona las llamadas a funciones como stack frames que incluyen variables locales, parametros o direcciones de retorno. BAJO.
- Heap: Espacio dinámico solicitado en tiempo de ejecucion, por ejemplo malloc(), free(). ALTO.
# Mutex vs Semáforos:
Ambos son mecanismos ante condiciones de carrera.
Los semáforos son contadores y permiten que más de un hilo acceda a una sección critica dependiendo de los tickets que contenga.  Pueden ser implementados bajo hilos o procesos.
Un mutex solo deja pasar a UN hilo a la sección crítica y bloquea a todos los demás hilos hasta que el mutex sea liberado. Es como que un hilo toma una llave para a acceder  un lugar y no la devuelve hasta salir de ese lugar. Los demás hilos deben esperar. Solo se usa en hilos.
# Explique la formas de IPC y compare las mismas.
- Memoria Compartida: Permite a los procesos compartir un espacio de memoria dentro del kernel del sistema. Es más rápido.
- Memoria Mapeada: Similar a la memoria compartida, solo que está asociada a un archivo que se encuentra en el sistema de archivos. Es más lento.
- Pipes: Permite una comunicación secuencial unidireccional en procesos relacionados (padre e hijo).
- FIFOs: Similar a pipe pero se comunican por un archivo, y no hace falta que los procesos sean relacionados. 
- Sockets: Es una abstracción de software, un canal de comunicacion bidireccional entre procesos ya sean de la misma máquina o no. 
# Qué es una sección crítica?
Es un segmento de código que accede y/o modifica modifica recursos compartido. No debe ser interrumpido para evitar dejar tareas incompletas o fugas de memoria o modifcar datos de la memoria que afectan el funcionamiento del resto de los hilos o el resultado del programa. Dependiendo de la acción del bloque de código, se debe restringir el acceso de varios hilos con mutexes o semáforos.
# Qué es el valor niceness de un proceso?
Indica la prioridad de un proceso para usar la CPU. Tiene un rango de -19 a 20. 
nice: permite iniciar un proceso con una prioridad espefica y renice modifica el nivel de prioridad de un proceso
**nice -n (programa nuevo)** // **renice -n -p PID**
# Enlace dinámico vs Enlace Estático.
- Enlace Estático: El código de las librerías se copia dentro del ejecutable en el momento de compilación. El binario resultante incluye todo lo necesario para ejecutarse, sin depender de archivos externos. Se usa cuando se requiere máxima estabilidad, portabilidad o evitar que cambios/actualizaciones externas rompan el programa
- Enlace dinámico: El ejecutable no contiene el código de las librerías sino referencias. En tiempo de ejecución, el S.O carga las librerías compartidas (.so) que el programa necesita. Se usa cuando se quiere ahorrar espacio en disco y memoria, facilitar actualizaciones de seguridad, o cuando múltiples programas comparten las mismas librerías.
# Qué se entiende por Socket Locales?
Los sockets locales son mecanismos de comunicación entre procesos (IPC) que se ejecutan en la misma computadora. Utilizan nombres de archivo como direcciones, aunque en realidad ese archivo no contiene datos, solo sirve como punto de conexión.
# ¿En qué consiste un sistema operativo desde la perspectiva de la programación orientada al sistema? Explique sus funciones principales desde los puntos de vista de Gestión de Procesos, de la Memoria, de Archivos y Dispositivos y de Seguridad y Control de Acceso.

El sistema operativo tiene como núcleo al kernel, que se encarga de controlar el hardware y asignar recursos. El sistema operativo provee interfaces que los programas utilizan para interactuar con el entorno.
- Gestión de procesos: Crea, destruye y coordinar procesos. Asigna el CPU a cada proceso mediante planificacion. Facilita la comunicación de procesos.
- Gestion de Memoria: Controla la memoria física y virtual. Administra segmentos. Asigna espacios a procesos.
- Gestion de archivos: Crea, controla permisos y elimina archivos. (Descriptores, dispositivos, sockets).
- Gestión de dispositivos: Usa drivers para abstraer el hardware y ofrecer interfaces estándar. Aplica políticas de permisos de usuarios o programas.
# Qué son despertares espurios?
Es cuando un hilo se despierta por un aviso falso del  sistema operativo de que se liberó la condicion de variable. 