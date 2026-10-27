# cap7 - condicionales

**Sintaxis:** 

> [!NOTE]
> `if TEST-COMMANDS; then CONSEQUENT-COMMANDS; fi`

**Comandos para implementar:**

| Primario            | Significado                                                                                               |
| :------------------ | :-------------------------------------------------------------------------------------------------------- |
| `[-a FILE]`         | Verdadero si el archivo existe.[cite: 1]                                                                  |
| `[-b FILE]`         | Verdadero si el archivo existe y es un archivo especial de bloque.[cite: 1]                               |
| `[-c FILE]`         | Verdadero si el archivo existe y es un archivo especial de caracteres.[cite: 1]                           |
| `[-d FILE]`         | Verdadero si el archivo existe y es un directorio.[cite: 1]                                               |
| `[-e FILE]`         | Verdadero si el archivo existe.[cite: 1]                                                                  |
| `[-f FILE]`         | Verdadero si el archivo existe y es un archivo regular.[cite: 1]                                          |
| `[-g FILE]`         | Verdadero si el archivo existe y su bit SGID está establecido.[cite: 1]                                   |
| `[-h FILE]`         | Verdadero si el archivo existe y es un enlace simbólico.[cite: 1]                                         |
| `[-k FILE]`         | Verdadero si el archivo existe y su bit "sticky" está establecido.[cite: 1]                               |
| `[-p FILE]`         | Verdadero si el archivo existe y es una tubería con nombre (FIFO).[cite: 1]                               |
| `[-r FILE]`         | Verdadero si el archivo existe y es legible.[cite: 1]                                                     |
| `[-s FILE]`         | Verdadero si el archivo existe y tiene un tamaño mayor a cero.[cite: 1]                                   |
| `[-t FD]`           | Verdadero si el descriptor de archivo FD está abierto y se refiere a una terminal.[cite: 1]               |
| `[-u FILE]`         | Verdadero si el archivo existe y su bit SUID está establecido.[cite: 1]                                   |
| `[-w FILE]`         | Verdadero si el archivo existe y es escribible.[cite: 1]                                                  |
| `[-x FILE]`         | Verdadero si el archivo existe y es ejecutable.[cite: 1]                                                  |
| `[-z FILE]`         | Verdadero si está vacia.                                                                                  |
| `[-O FILE]`         | Verdadero si el archivo existe y es propiedad del ID de usuario efectivo.[cite: 1]                        |
| `[-G FILE]`         | Verdadero si el archivo existe y es propiedad del ID de grupo efectivo.[cite: 1]                          |
| `[-L FILE]`         | Verdadero si el archivo existe y es un enlace simbólico.[cite: 1]                                         |
| `[-N FILE]`         | Verdadero si el archivo existe y ha sido modificado desde la última vez que se leyó.[cite: 1]             |
| `[-S FILE]`         | Verdadero si el archivo existe y es un socket.[cite: 1]                                                   |
| `[FILE1 -nt FILE2]` | Verdadero si FILE1 ha sido modificado más recientemente que FILE2, o si FILE1 existe y FILE2 no.[cite: 1] |
| `[FILE1 -ot FILE2]` | Verdadero si FILE1 es más antiguo que FILE2, o si FILE2 existe y FILE1 no.[cite: 1]                       |
| `[FILE1 -ef FILE2]` | Verdadero si FILE1 y FILE2 se refieren a los mismos números de dispositivo e inodo.[cite: 2]              |
| `[-o OPTIONNAME]`   | Verdadero si la opción del shell                                                                          |

```bash
if [ -f "$1" ]; then ... fi              # existe y es archivo regular
if [ -d "$1" ]; then ... fi              # es directorio
if [ -z "$x" ]; then ... fi              # cadena vacía
if [ "$a" = "$b" ]; then ... fi          # cadenas iguales
if [ "$n" -gt 5 ]; then ... fi           # número mayor que 5
if [[ $resp == s* ]]; then ... fi        # compara contra un patrón
if (( anio % 4 == 0 )); then ... fi      # aritmética (sin $)
if ! grep -q "$u" /etc/passwd; then ... fi   # usar un comando como condición
[ $# -eq 1 ] || { echo "Uso: $0 arg"; exit 1; }  # validar argumentos
exit 0                                   # terminar el script con código
case "$1" in
  start)   iniciar ;;
  a|b)     echo "a o b" ;;
  [0-9]*)  echo "empieza con número" ;;
  *)       echo "Uso: $0 {start|a|b}"; exit 1 ;;
esac
```

**Trampa clásica**: `[ 10 > 9 ]` no compara números, redirige a un archivo llamado `9`. Para números: `-gt` o `(( 10 > 9 ))`.

| Forma   | Para qué                                                        | Ejemplo               |
| ------- | --------------------------------------------------------------- | --------------------- |
| `[ ]`   | Test POSIX clásico; variables entre comillas                    | `[ -f "$ARCH" ]`      |
| `[[ ]]` | Test de Bash; no parte palabras; `==` compara con un **patrón** | `[[ $resp == s* ]]`   |
| `(( ))` | Aritmética entera; variables sin `$`                            | `(( anio % 4 == 0 ))` |
##### **Estructura Case:**

```
case "$VARIABLE" in
    opcion1)
        # comandos
        ;;
    opcion2|opcion3)
        # comandos si es opcion2 O opcion3
        ;;
    *)
        # Opción por defecto (equivalente al 'else')
        ;;
esac
```

##### **Condicionales:**

- **Doble Corchete:** Es una versión mejorada de `[ ... ]`. Aporta tres ventajas fundamentales: 
	- **Soporta operadores lógicos nativos `&&` y `||`:**
    
    - En `[ ]`:  ==🟡(POSIX)==
	    `[ "$A" -gt 0 ] && [ "$B" -gt 0 ]` (o `[ "$A" -gt 0 -a "$B" -gt 0 ]`)
    - En `[[ ]]`: 
		`[[ "$A" -gt 0 && "$B" -gt 0 ]]`
	- **Soporta coincidencia de patrones y comodines (`*`):**
```
if [[ "$NOMBRE" == os* ]]; then
	echo "El nombre empieza con 'os'"
fi
```

- **Doble Paréntesis:** exclusivamente para **operaciones y comparaciones numéricas**. Te permite usar los operadores matemáticos tradicionales (`>`, `<`, `>=`, `<=`, `==`, `!=`) en lugar de `-gt`, `-lt`, `-eq`.
	- **Con corchetes `[ ... ]`:** `if [ "$EDAD" -ge 18 ]; then`
	- **Con `(( ... ))`:** `if (( EDAD >= 18 )); then` _(Nota: dentro de `(( ))` ni siquiera hace falta poner el signo `$` a las variables)._

|**Característica**|**[ ... ] (POSIX)**|**[[ ... ]] (Bash)**|**(( ... )) (Aritmético)**|
|---|---|---|---|
|**Uso principal**|Archivos, cadenas, números|Cadenas avanzadas, patrones|Solo números / matemática|
|**Operador 'Y' / 'O'**|`-a` / `-o` (o `] && [`)|`&&` / `\|`|`&&` / `\|`|
|**Comparación numérica**|`-eq`, `-gt`, `-lt`|`-eq`, `-gt`, `-lt`|`==`, `>`, `<`|
|**Comodines / Patrones**|No|Sí (`*`, `?`)|No|
# cap8

