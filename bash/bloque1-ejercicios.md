# cap1

1. **Where is the bash program located on your system?**
En etc/shells
2. **Use the --version option to find out which version you are running.**
bash --version
3. **Which shell configuration files are read when you login to your system using the graphical user interface and then opening a terminal window?** 
4. **Are the following shells interactive shells? Are they login shells?**
 - A shell that you get after issuing the command ssh localhost. 
 - A shell that you get when logging in to the console in text mode. 
 - A shell obtained by the command xterm &. 
 - A shell opened by the mysystem.sh script. 
 - A shell opened by clicking on the background of your graphical desktop, selecting "Terminal" or such from a menu. A shell that you get on a remote host, for which you didn't have to give the login and/or password because you use SSH and maybe SSH keys.
 
1.  **Can you explain why bash does not exit when you type Ctrl+C on the command line?** 
Tiene el SIGINT configurado.

2. **Display directory stack content.** 
dirs: muestra directorio actual

3. **If it is not yet the case, set your prompt so that it displays your location in the file system hierarchy, for instance add this line to ~/.bashrc: export PS1="\u@\h \w> "**
PS1 es una variable para cambiar el aspecto de la terminal

4. **Display hashed commands for your current shell session.**
hash: muestra donde están los comandos utilizados recientemente, los guarda en un hash table

5. **How many processes are currently running on your system? Use ps and wc, the first line of output of ps is not a process!** 

6. **How to display the system hostname? Only the name, nothing more!**

# cap2

This exercise will help you to create your first script. 
1. Write a script using your favorite editor. The script should display the path to your homedirectory and the terminal type that you are using. Additionally it shows all the services started up in runlevel 3 on your system. (hint: use HOME, TERM and ls /etc/rc3.d/S*) 
2. Add comments in your script. 
3. Add information for the users of your script. 
4. Change permissions on your script so that you can run it. 
5. Run the script in normal mode and in debug mode. It should run without errors. 
6. Make errors in your script: see what happens if you misspell commands, if you leave out the first line or put something unintelligible there, or if you misspell shell variable names or write them in lower case characters after they have been declared in capitals. Check what the debug comments say about this.
# cap3


1. Bash compila el script antes de ejecutarlo.
FALSO. Es interpretado.
2. `cd` es un built-in porque si corriera en un proceso hijo el cambio de directorio no afectaría a la shell.
VERDADERO
3. Si un comando no se encuentra, Bash devuelve el código 1.
FALSO
Si tiene /, ejecuta es ruta.
Si no, busca en la lista de funciones.
Si no, busca en la lista de comando integrados.
Si no, busca en el PATH
Si ahí ya no encuentra, devuelve el codigo **127.** 
4. `source script.sh` necesita que el script tenga permiso de ejecución.
FALSO, porque se ejecuta en el mismo proceso.
5. Una variable creada dentro de un script ejecutado con `./script.sh` sigue existiendo después en la terminal.
FALSO, porque es local.
6. `MI_VAR = 5` asigna 5 a `MI_VAR`.
FALSO. No debe tener espacios.
7. Si un proceso hijo modifica una variable exportada, el padre ve el nuevo valor.
FALSO. El padre no puede ver los cambios.
8. `$#` contiene la cantidad de argumentos recibidos sin contar `$0`.
VERDADERO.
9. `"$@"` y `"$*"` dan siempre el mismo resultado.
Entre comillas, `$@` da una palabra por argumento y `$*` las junta en una sola.
10. `echo '$HOME'` imprime la ruta del directorio personal.
FALSO. No tiene doble comilla.
11. La expansión de llaves ocurre antes que la de variables.
VERDADERO.
12. `$(( 7 / 2 ))` da `3.5`.
FALSO. Redondea hacia abajo
13. Un alias definido en la terminal se puede usar dentro de un script ejecutado con `./`.
FALSO. No hace falta el `./` si el alias ya está definido en bashrc
14. El shebang puede ir en la segunda línea si la primera es un comentario.
FALSO. Siempre debe ir primero.
