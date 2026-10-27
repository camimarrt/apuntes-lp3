# cap4

Estos ejercicios le ayudarán a dominar las expresiones regulares. 
1. Muestre una lista de todos los usuarios en su sistema que inician sesión con el shell Bash por defecto. 
2. Desde el directorio /etc/group, muestre todas las líneas que comiencen con la cadena "daemon". 
3. Imprima todas las líneas del mismo archivo que no contengan la cadena. 1. 2. 3. 4.
4. Muestre información de localhost del archivo 5. 6. 7. 8. 9. 10. 11. 12. /etc/hosts , muestre el/los número(s) de línea que coinciden con la cadena de búsqueda y cuente el número de ocurrencias de la cadena.
5. Muestre una lista de subdirectorios de /usr/share/doc que contengan información sobre shells. 
6. ¿Cuántos archivos README contienen estos subdirectorios? No cuente nada en la forma de "README.a_string". 
7. Haga una lista de archivos en su directorio de inicio que fueron cambiados hace menos de 10 horas, usando grep , pero omita los directorios. 
8. Ponga estos comandos en un script de shell que genere una salida comprensible.
9. ¿Puede encontrar una alternativa para wc -l , usando grep?
10. Usando la tabla del sistema de archivos (por ejemplo,  /etc/fstab )enumere los dispositivos de disco locales.
11. Haga un script que compruebe si un usuario existe en `/etc/passwd`.
12. Muestre los archivos de configuración en `/etc` que contengan numros en sus nombres.

# cap5

Estos ejercicios tienen el propósito de demostrar aún más lo que sed puede hacer. 
1. Imprima una lista de archivos en su directorio de scripts, que terminen en ".sh". Tenga en cuenta que es posible que tenga que quitar el alias (unalias) de ls. Ponga el resultado en un archivo temporal. 
2. Haga una lista de los archivos en /usr/bin que tengan la letra "a" como segundo carácter. Ponga el resultado en un archivo temporal. 
3. Elimine las primeras 3 líneas de cada archivo temporal. Imprima en la salida estándar solo las líneas que contienen el patrón "an". 1. 2. 3. 4. 5. 
4. Cree un archivo que contenga comandos sed para realizar las dos tareas anteriores. Agregue un comando adicional a este archivo que agregue una cadena como "*** Esto podría tener algo que ver con man y las páginas man ***" en la línea que precede a cada ocurrencia de la cadena "man". Compruebe los resultados. 6. 7. 
5. Se utiliza un listado largo del directorio raíz, comandos / , como entrada. Cree un archivo que contenga sed que verifiquen los enlaces simbólicos y los archivos de texto plano. Si un archivo es un enlace simbólico, precédalo con una línea como "--Esto es un enlace simbolico--". Si el archivo es un archivo plano, agregue una cadena en la misma línea, agregando un comentario como "<--- este es un archivo plano".
6. Cree un script que muestre las líneas que contienen espacios en blanco finales de un archivo. Este script debe usar un script sed y mostrar información sensata al usuario

# cap6


Estos son algunos ejemplos prácticos en los que awk puede ser útil.
1. Para el primer ejercicio, su entrada son líneas en el siguiente formato: `Username:Firstname:Lastname:Telephone number `
Haga un script awk que convierta dicha línea en un registro LDAP en este formato:
![[Pasted image 20261024185039.png|295]]

2. Cree un script Bash usando awk y comandos estándar de UNIX que mostrará los tres principales usuarios del espacio en disco en el sistema de archivos /home (si no tiene el directorio de los homes en una partición separada, haga el script para la partición /; esta está presente en todos los sistemas UNIX). Primero, ejecute los comandos desde la línea de comandos. Luego, póngalos en un script. El script debe crear una salida sensata (sensata en el sentido de que sea legible por el jefe). Si todo demuestra funcionar, haga que el script le envíe sus resultados por correo electrónico (use por ejemplo mail -s "Disk space usage" < result). Si el daemon quota está ejecutándose, use esa información; si no, use find.
3. Cree una salida estilo XML a partir de una lista separada por tabulaciones (Tab-separated) con el siguiente formato:
![[Pasted image 20261024185131.png]]
La salida debería leerse:

![[Pasted image 20261024185140.png]]

Adicionalmente, si sabe algo sobre XML, escriba un script con BEGIN y END para completar la tabla. O hágalo en HTML.