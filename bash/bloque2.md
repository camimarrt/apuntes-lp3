
# cap4

>[!danger] **Trampa de examen: Globbing vs Expresiones Regulares**
> - **Globbing (Comodines de la Shell):** Es lo que usa la terminal para buscar archivos (ej. `ls *.txt` donde `*` significa "cualquier cosa")
> - **Expresiones Regulares (Regex):** Es el lenguaje que usan comandos como `grep`, `sed` o `awk` para buscar **patrones dentro del contenido de un texto** (donde `*` significa "repite el carácter anterior 0 o más veces").

## Sintaxis Básica:
**Expresiones Regulares:** Una expresión regular es un patrón que describe un conjunto de cadenas. Puede ir seguida de uno de varios operadores de repetición (metacaracteres)

> [!NOTE]
> 
> `grep [opciones] 'patrón' [archivo]`
> 

| Símbolo                | Significa                                 |
| ---------------------- | ----------------------------------------- |
| `.`                    | Cualquier carácter (uno)                  |
| `*`                    | Lo anterior, 0 o más veces                |
| `?`                    | Lo anterior, 0 o 1 vez (extendida)        |
| `+`                    | Lo anterior, 1 o más veces (extendida)    |
| `{N}`, `{N,}`, `{N,M}` | Exactamente N, N o más, entre N y M veces |
| `^` y `$`              | Inicio y fin de línea                     |
| `\<` y `\>`            | Inicio y fin de palabra                   |
| `[abc]`, `[a-z]`       | Uno de la lista o del rango               |
| `[^abc]`               | Uno que **no** esté en la lista           |
| `\.`                   | Un punto literal                          |
| `[[:digit:]]`          | Clase POSIX                               |
| `(este)\|(oeste)`      | OR de patrones                            |

Los metacaracteres pierden su significado especial. Para usar su significado especial hay que agregar `\` antes del caracter (caracter de escape). Se pueden concatenar, alternar con | y agrupar con  (). Precedencia: repetición, concatenación, alternancia. 

**Comandos para implementar — Cap 4**

```bash
grep 'patron' archivo          # líneas que coinciden
grep -v 'patron' archivo       # líneas que NO coinciden
grep -c 'patron' archivo       # cuántas LÍNEAS coinciden.
grep -o 'patron' archivo       # cuántas repeticiones tiene por línea
grep -n 'patron' archivo       # con número de línea
grep -i 'patron' archivo       # ignora mayúsculas
grep -w 'palabra' archivo      # solo palabra completa
grep -q 'patron' archivo       # silencioso: solo el código de salida
grep -r/R 'patron' archivo     # busca dentro de todas las carpetas
grep -E 'a+|b?' archivo        # regex extendida
grep '^root' /etc/passwd       # empieza con root
grep ':$' /etc/passwd          # termina en :
grep '\<c...h\>' archivo       # palabra de 5 letras de c a h
grep '*' archivo               # asterisco literal (SIEMPRE entre comillas simples)
```

## Expresiones Regulares Extendidas:

En las expresiones regulares básicas, ciertos operadores requieren una barra invertida `\` para funcionar como cuantificadores (como `\+` o `\?`). Para evitar escribir barras invertidas en todo el código, usaremos **`grep -E`** (Extended Regex):

==🟠Permite usar `+`, `?`, `|`, `()`, `{n,m}` sin barras invertidas==

**Prueba de regex extendida en terminal:**

> [!NOTE]
> `grep -E '^(root|nobody)' /etc/passwd`


# cap5

sed = Stream Editor

Es la herramienta para buscar y filtrar texto. **Modifica** el texto al vuelo y envía el resultado a la salida estándar (`stdout`), dejando el archivo original intacto a menos que fuerces la escritura.

| Comando           | Qué hace                                                             |
| ----------------- | -------------------------------------------------------------------- |
| `p`               | Imprimir                                                             |
| `d`               | Borrar                                                               |
| `s/viejo/nuevo/`  | Buscar y reemplazar (solo la **primera** coincidencia de cada línea) |
| `s/viejo/nuevo/g` | Reemplazar **todas** las coincidencias de cada línea                 |
| `a\`, `i\`, `c\`  | Agregar debajo, insertar arriba, cambiar la línea                    |
| `r`, `w`          | Leer de un archivo, escribir a un archivo                            |
| `-n`              | Modo silencioso. Imprime las lineas que coinciden                    |
| `-e` o `;`        | agrega otro comando                                                  |
| `-f`              | lee los comandos de un archivo.                                      |
| Rangos `(x,y)`    | A partir de X hasta Y (inclusivo).                                   |
| `i`               | Ignora las mayusculas y minusculas.                                  |
| `/n`              | Reemplaza la enésima ocurrencia.                                     |

**Comandos para implementar — Cap 5**

```bash
sed -n '/error/p' archivo           # imprime solo las líneas con "error"
sed '/error/d' archivo              # borra esas líneas
sed '2,4d' archivo                  # borra las líneas 2 a 4
sed '3,$d' archivo                  # borra de la 3 al final
sed -n '/inicio/,/fin/p' archivo    # rango entre dos patrones
sed 's/viejo/nuevo/' archivo        # reemplaza la 1ª aparición por línea
sed 's/viejo/nuevo/g' archivo       # reemplaza TODAS las de cada línea
sed -e '/^#/d' -e '/^$/d' archivo   # varios comandos
sed -f comandos.sed archivo         # comandos desde un archivo
sed 's/a/b/' archivo > salida.txt   # guardar el resultado
sed 's/error/OK/gi' archivo         # i no discrimina M y m
sed 's/a/X/2' archivo               # Reemplaza la segunda ocurrencia 
sed '1d' archivo                    # elimina la primera línea
sed '/^#/d' archivo                 # elimina comentarios
sed -e '/^#/d' -e '/^$/d' ~/scripts/web.conf
sed '/^#/d; /^$/d' ~/scripts/web.conf
```

### DIAGONALITIS

Cuando necesitas reemplazar rutas que contienen barras diagonales `/` (por ejemplo `/var/www`), usar la barra `/` como delimitador en `s/viejo/nuevo/` te obliga a escapar cada barra con una diagonal invertida (`\/`), lo cual es propenso a errores.

En `sed`, puedes reemplazar las barras de la sintaxis por **cualquier otro símbolo**, como `#`, `@` o `|`:

- **Difícil de leer (Escapando barras):** 
> [!NOTE]
> `sed 's/\/var\/www/\/srv\/logs/' archivo`

- **Limpio y legible (Usando `#` como delimitador):** 
> [!NOTE]
> `sed 's#/var/www#/srv/logs#' archivo`

## Agregar por cada ocurrencia.


En `sed`, la orden para insertar una línea **antes** (preceder) de una coincidencia es **`i\`** (_insert_).  La regla se escribe así: 

>[!NOTE] 
>`/man/i\*** comentario ***`


# cap6

`awk` analiza el texto como si fuera una tabla de datos. Procesa datos organizados en columnas o campos.
## **Sintaxis:**

> [!NOTE]
> `awk -F'separador' 'patrón { acción }' archivo`

- **`-F`**: Define el carácter que separa las columnas (si no lo pones, usa espacios o tabulaciones por defecto).

- **`patrón`**: Condición o filtro para elegir qué filas procesar. Si no pones patrón, se procesan **todas** las filas.

- **`{ acción }`**: La instrucción a ejecutar (casi siempre es `print`).

## Las Variables de Columna

Cada vez que `awk` lee una línea, asigna sus partes a estas variables automáticas:

| Variable             | Qué es                                                           |
| -------------------- | ---------------------------------------------------------------- |
| `FS`                 | Separador de campos de entrada (se fija en `BEGIN`, o con `-F:`) |
| `OFS`                | Separador de campos de salida (por defecto, un espacio)          |
| `ORS`                | Separador de registros de salida (por defecto `\n`)              |
| `NR`                 | Número de registros (líneas) leídos hasta ahora                  |
| `NF`                 | Cantidad de campos de la línea actual                            |
| **`$0`**             | La línea entera sin dividir.                                     |
| **`$1`, `$2`, `$3`** | Columna 1, columna 2, columna 3...                               |


## Los Bloques `BEGIN` y `END`:


> [!NOTE]
> `awk -F: 'BEGIN { print "=== INICIO ===" } NR <= 3 { print $1 } END { print "=== FIN ===" }' /etc/passwd` 
> 

- **`BEGIN { ... }`**: Se ejecuta **una sola vez ANTES** de empezar a leer el archivo. (Sirve para imprimir títulos o encabezados).
- **`END { ... }`**: Se ejecuta **una sola vez DESPUÉS** de procesar todo el archivo. (Sirve para imprimir totales o conclusiones).

## Variales y Contadores:


No necesitas declararlas previamente; `awk` las inicializa automáticamente en `0` (para números) o `""` (para texto).

#### Pasar Variables:

> [!NOTE]
> `awk -v VARIABLE_AWK="$VARIABLE_BASH" 'patrón { acción }' archivo`

#### printf

**Regla de oro:** `printf` no agrega un salto de línea automático al final; siempre debes incluir **`\n`**.

**Especificadores principales:**
- **`%s`**: Texto (_string_).
- **`%-15s`**: Texto alineado a la izquierda ocupando 15 espacios de ancho.
- **`%d`**: Número entero.

> [!NOTE]
> - Con print (salida simple):
> `awk -F: 'NR <= 2 { print $1, $3 }' /etc/passwd`
> 
> - Con printf (salida formateada y alineada):
> `awk -F: 'NR <= 2 { printf "Usuario: %-10s | UID: %d\n", $1, $3 }' /etc/passwd`

## Condicionales:

⚠️ **Regla de oro:** La condición **siempre** va entre paréntesis `(...)`.

> [!NOTE]
> `prom = ($2 + $3 + $4) / 3`
> 
> `if (prom >= 70) {`
>     # Instrucciones si se cumple
> `} else {`
>     # Instrucciones si NO se cumple
> `}`

