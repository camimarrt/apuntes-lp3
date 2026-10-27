Abarca los capitulos 1, 2 y 3
# cap1
Shell: El programa de UNIX interpreta comandos de usuarios, desde la entrada de un usuario o un leyendo un archivo llamado **script**. 
Script: Archivo de texto que guarda una secuencia de comandos. Son interpretados, no compilados.
- `#!/bin/bash` va al principio de cualquier script.
- Shebang o `#!`  le indican al sistema que use el interprete de bash.
- `chmod` es un comando para dar permiso de ejecución. Ejemplo: `chmod u+x script.sh`
- Para ejecutar un script se escribe `./script.sh` en la terminal.

![[Pasted image 20261005111456.png]]
## 1. Tipos de Shell:
- **sh - Bourne Shell:** La shell básica y original de sistemas UNIX y basados en UNIX. Es compatible en todos los sistemas Linux con compatilidad de UNIX.
- **bash - Bourne Again Shell:** La shell estándar de GNU conocida como un *superset*  de Shell. Es compatible con sh, todos los comandos de sh funcionan en bash pero no al revés.
- **csh - C Shell:** Shell del lenguaje C.
- **tcsh - TENEZ C shell:** Una versión de csh mejorada en facilidad de uso y velocidad.
- **ksh - Korn Shell:** Superconjunto de sh, apreciado por quienes vienen de UNIX.

La shell por defecto está definida en la carpeta `/etc/passwd`. 
Para cambiar de shell se debe ingresar el nombre de la nueva shell. El sistema encuentra el shell en el PATH y la ejecuta.   `user:~>tsch`
Para ver las shells disponibles: `cat etc/shells`

## 2.Ventajas de Bash:

### Tipos de Invocación:
Además de pasar comandos al kernel, el shell ofrece un entorno de usuario configurable con archivos de recursos.
Son archivos de configuración para el arranque del programa. Bash lee diferentes archivos de configuración dependiendo del tipo de sesión:
- **Shell Login Intereactiva:** Se inicia después de que te autenticas con usuario y contraseña. Lee primero `/etc/profile` y luego busca `~/.bash_profile`.
- **Interactiva sin Login:** Al iniciar una terminal desde el menú del sistema. En este caso, lee el archivo `~/.bashrc`.
- **No Interactiva:** Es la manera en la que corren todos los scripts. Lee el archivo que se encuentre indicado en la variable `$BASH_ENV`.
- **Invocada como sh:** Cuando Bash se invoca como sh, imita al bourne shell histórico. En este modo, lee los archivos `/etc/profile` y `~/.profile`.
---
- **Expresiones condicionales:** Son utilizadas por el comando compuesto `[[` y comandos integrados `[`. Pueden ser unarias o binarias. Las expresiones unarias se utilizan para examinar y trabajar sobre archivos, requieren de un sólo objeto. Las binarias incluyen operadores de cadenas de texto y comparación, requieren dos objetos para la evaluación.]]
- **Aritmética de Shell:** Permite evaluar expresiones aritméticas. No verifica desbordamientos. Captura la división entre 0. Los operadores se manejan como en C.
- **Aliases:** Substituye una cadena de texto por una palabra que es un comando simple. La shell tiene una lista de aliases y comandos.  : Bash lee al menos una línea completa de entrada antes de ejecutar sus comandos; los alias se expanden al leer el comando, no al ejecutarlo. 
- **Arrays:** Provee arreglos unidimensionales. No existe un tamaño límite, ni requiere indexar los elementos o ingresarlos de forma secuencial. Se declara como `declare -a`. 
- **Pila de Directorios:** Mantiene un historial de directorios visitados. `pushd/popd` agrega/quita un directorio a la pila y cambia el directorio actual. Se puede visualizar el contenido con `dirs/DIRSTACK`.
- **Shell restringida:** `rbash` o `-r` aplica restricciones de seguridad. Deshabilita el comando cd, prohíbe: cambiar variables, usar /, exec, definir variables en PATH, comandos -f y -d, operadores de redirección de salida como |, >>, >&, etc. 
## 3. Ejecución de Comandos:
Bash determina el tipo de programa que será ejecutado. Programas normales son comandos compilados del sistemas, cuando se ejecutan se crea un nuevo proceso con fork() y exec(). 
**Comandos integrados del sistema:**
Están contenidos dentro de la propia shell, que lo ejecuta directamente sin crear un nuevo proceso. 
Ej.: break, cd, continue, exit, set, alias, echo, help, enable...
**Ejecución de Scripts:**
Cuando se ejecuta un script de shell, bash crea un nuevo proceso con fork(). Este proceso hijo interpreta y ejecuta el script línea por línea. Cuando finaliza, el padre despierta mostrando un nuevo indicador (prompt).
*Cómo se buscan los comandos?*
Si tiene barras /, lo ejecuta en esa ruta.
Si no, busca en la lista de funciones.
Si no es función, busca en la lista de comandos integrados.
Si no es integrado, analiza las variables PATH.
Si no lo encuentra, indica que ocurrió un error y vuelve el código de salida 127.

--- 
**Sintaxis de Shell:** La Shell lee el comando de entrada, divide el comando en palabras y operadores. Realiza operaciones de análisis de comandos para identificar el proceso, ejecuta los comandos y recopila su código de salida.
**Funciones de Shell:** Agrupan comandos bajo un sólo nombre. Cuando se utiliza ese nombre en u comando, se ejecuta la lista de comando asociada a ese nombre.
**Expansiones de Shell:** Se realizan tras dividir la línea en palabras: expansiones de llaves, de tilde, de parámetros/variables, sustitución de comandos, expansión aritmética, división de palabras y expansión de nombre de archivo.
**Redirecciones:** Redirigen la entrada y salida de una ejecución o gestionar archivos descriptores.

## 4. Buenas Prácticas de Scripts:
#### Propiedades:
1. Un buen script corre sin errores.
2. Realiza la tarea para lo que fue creado.
3. Tiene una lógica definida y aparente.
4. No realiza ningún trabajo de más aparte de su objetivo.
5. Debe de ser reutilizable.
#### Estructura:
Debe asegurar una lógica correcta y un control de flujo eficiente. 
Para desarrollar scripts, hágase las siguiente preguntas:
- ¿Voy a necesitar alguna información del usuario o del entorno del usuario? 
- ¿Cómo voy a almacenar esa información? 
- ¿Hay algún archivo que necesite ser creado? 
- ¿Dónde y con qué permisos y propietarios? •
- ¿El usuario necesita alguna notificación? ¿Cuándo y por qué?

# cap2

Un **script** es una secuencia de comandos reutilizable. 
Se ejecuta ingresando el nombre del script en la línea de comando. 

Si en algún momento necesitas que un script modifique tu entorno actual (por ejemplo, para guardar variables que vas a seguir usando), debes ejecutarlo con el comando `source script.sh`

- `rbash:` Restricted bash
Limita lo que un usuario o script puede hacer. Deshabilita comandos y acciones como: cd, modificar el PATH, ejecutar comandos con ruta absoluta, sobreescribir archivos.
- `sh:` iniciar con sh hace que el script se ejecute con como shell y no bash.
- `bashx` Depuración. Esto activará el modo de rastreo (xtrace), y Bash imprimirá en pantalla cada comando precedido por un signo `+` justo antes de ejecutarlo, seguido del resultado real. Si tienes un script largo y sabes que el error está en una sección específica, puedes escribir `set -x` dentro de tu código justo antes de la zona conflictiva para encender la depuración, y `set +x` justo al terminar esa zona para apagarla.
- `set +-f`: Desactiva los comodines (meta caracteres) como `*`, `?`. Se interpretan como texto plano.
- `set +-v:` Hace que bash imprima cada linea de codigo antes de ejecutarla.
Se pueden especificar en el script mismo como: `#!/bin/bash -xvf`

# cap3

## 1. Archivos de Inicializacion de la Shell.

- `etc/profile`: se lee cuando se invoca con login o sh.
- `/etc/bashrc`: configuraciones especificas de bash. definiciones para funciones y aliases
- `~/.bash_profile`: archivo de configuracion para usarios individuales
- `~/bash_login:` se lee si .bash_profile no existe
- `~/profile`: se lee si los dos anterios no existen
- `~/.bashrc:` shells sin login, la terminal
- `~/.bash_logout`: instrucciones de cerrar sesion

## 2. Variables.

**Tipos de Variables**:

- Globales
- Locales
- Por contenido

**Exportar variables:**
Una variable creada de esta forma es local: los procesos hijos no serán conscientes de ella. Para pasar variables a una subshell, necesitamos exportarlas usando export: export VARNAME="valor". Una subshell puede cambiar las variables que heredó, pero esos cambios no afectan al padre.
**Variables Reservadas:**
![[Pasted image 20261020221925.png]]![[Pasted image 20261020221934.png]]

**Parametros Especiales:**
![[Pasted image 20261020221805.png]]


## 3. Comillas
Se usan para eliminar el significado especial de caracteres.
Caracteres de escape: \

**Comillas simples:**
se usan para preservar el valor literal de cada carácter encerrado entre ellas. Ninguna comilla simple puede ocurrir dentro de comillas simples, ni siquiera precedida por una barra invertida

**Comillas Dobles:**
se preserva el valor literal de todos los caracteres encerrados, excepto para el signo de $ ), los backticks ( ` `) y la barra invertida ( expansión de variables).

## 4. Expansion de Shell

El comand se divide en tokens y lleva a cabo la expansion de tokens.
- Expansion de llaves: pueden generarse cadenas arbitrarias. Las expansiones de llaves pueden anidarse
	`>> echo sp{el,il,al}l `
	`>> spell spill spall`
- Expasion de tilde: Si una palabra comienza con un carácter tilde ("~") no entrecomillado, El reemplazo toma lugar trocando todo el símbolo por la variable HOME del usuario.
- Expansion de parametros:  `$$$$`\
- Sustitucion de comandos:permite que la salida de un comando suplante o reemplace al mismo comando que obró la respectiva ejecución. Se llaman con `$` o `''` (acentos).
`echo 'date'` 
`Thu Feb 6 10:06:20 CET 200`
- Expansion Aritmetica: permite ejecutar la evaluación, resolución y decodificación de sentencias estructuradas matemáticas con el posterior recambio. La presentación se rige como `$((EXPRESSION)).`
- Sustitucion de procesos: Toma la forma de <(LISTA) o >(LISTA) . El proceso LISTA se ejecuta con su entrada o salida conectada a un FIFO o algún archivo en /dev/fd.
- Division de palabras: La shell trata cada carácter de $IFS como un delimitador y divide los resultados de otras expansiones en palabras basándose en estos caracteres
- Expansion de nombre de archivo: Después de la división, Bash escanea en busca de * , ?, y `[`. Si aparecen, la palabra se considera un PATRÓN y se reemplaza con una lista ordenada de archivos coincidentes.
## 5. Alias

Permiten sustituir una cadena por una palabra cuando se usa como la primera palabra de un comando. Se administran con los comandos  alias o unalias