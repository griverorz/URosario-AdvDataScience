Estas notas están basadas en los apuntes de [Gloria Li y Jenny
Bryan](http://stat545.com/block022_regular-expression.html).

Una "expresión regular" es un patrón que define una cadena de texto con
una particular estructura. Las expresiones regulares son muy útiles en
programación en general, pero aquí las usaremos para limpiar y preparar
texto. En `R` hay varios paquetes que proveen de herramientas para
trabajar con expresiones regulares, como `stringr`. Algunas de las cosas
que podemos hacer con expresiones regulares:

-   identificar cadenas que encajan con un patron:
    `grep(..., value = FALSE)`, `grepl()`, `stringr::str_detect()`.
-   extraer cadenas que encajan con un patrón:
    `grep(..., value = TRUE)`, `stringr::str_extract()`,
    `stringr::str_extract_all()`
-   localizar patrones dentro de una cadena. `regexpr()`, `gregexpr()`,
    `stringr::str_locate()`, `string::str_locate_all()`
-   reemplazar cadenas dentro de un patrón: `gsub()`,
    `stringr::str_replace()`, `stringr::str_replace_all()`
-   separar una cadena usando un patrón: `strsplit()`,
    `stringr::str_split()`

Sintaxis de las expresiones regulares
-------------------------------------

Las expresiones regulares especifican caracteres (o clases de
caracteres) para identificar repeticiones y localizaciones en cadenas de
texto. Para conseguir eso, algunos símbolos tienen un significado
especial, como `$`, `*`, `(` o `)`.

Escapes
-------

Hay algunos caracteres en `R` que no pueden convertirse directamente a
una cadena. Por ejemplo, supongamos que especificamos un patrón que
incluye una comilla para encontrar expresiones que usen el genitivo
sajón. Para conseguir eso tendríamos que "escapar" la comilla, ya que
una única comilla produciría un error en el intérprete de `R`. Para
*escapar* expresiones usamos `\\`:

    grep('\'', c("its", "it's"), value = TRUE)

    ## [1] "it's"

Hay otros caracteres que requiren ser escapados:

-   `\\'`: Comilla.
-   `\"`: Doble comilla.
-   `\n`: Nueva linea.
-   `\r`: Retorno.
-   `\t`: Tabulación.

Cuantificadores
---------------

Los cuantificadores permiten especificar el número de repeticiones en un
patrón.

-   `*`: El carácter puede aparecer al menos 0 veces.
-   `+`: El carácter puede aparecer al menos 1 vez.
-   `?`: El carácter puede aparecer hasta 1 vez.
-   `{n}`: El carácter aparece n veces.
-   `{n,}`: El carácter puede aparecer al menos n veces.
-   `{n,m}`: El carácter puede aparecer entre m y n veces.

Veamos unos ejemplos:

    (strings <- c("a", "ab", "acb", "accb", "acccb", "accccb"))

    ## [1] "a"      "ab"     "acb"    "accb"   "acccb"  "accccb"

    grep("ac*b", strings, value = TRUE)

    ## [1] "ab"     "acb"    "accb"   "acccb"  "accccb"

    grep("ac+b", strings, value = TRUE)

    ## [1] "acb"    "accb"   "acccb"  "accccb"

    grep("ac?b", strings, value = TRUE)

    ## [1] "ab"  "acb"

    grep("ac{2}b", strings, value = TRUE)

    ## [1] "accb"

    grep("ac{2,}b", strings, value = TRUE)

    ## [1] "accb"   "acccb"  "accccb"

    grep("ac{2,3}b", strings, value = TRUE)

    ## [1] "accb"  "acccb"

Posiciones del patrón dentro de la cadena
-----------------------------------------

También podemos encajar caracteres por posición:

-   `^`: Principio de la cadena,
-   `$`: Final de la cadena.
-   `\b`: Límite de palabra.

<!-- -->

    (strings <- c("abcd", "cdab", "cabd", "c abd"))

    ## [1] "abcd"  "cdab"  "cabd"  "c abd"

    grep("ab", strings, value = TRUE)

    ## [1] "abcd"  "cdab"  "cabd"  "c abd"

    grep("^ab", strings, value = TRUE)

    ## [1] "abcd"

    grep("ab$", strings, value = TRUE)

    ## [1] "cdab"

    grep("\\bab", strings, value = TRUE)

    ## [1] "abcd"  "c abd"

Operadores
----------

-   `.`: Empareja con cualquier caracter.
-   `[...]`: Establece una lista de caracteres.
-   `[^...]`: El inverso de la lista de caracteres: empareja los
    caracteres que *no* estén en la lista.
-   `\\`: Escapa metacaracteres en una expresión regular. Por ejemplo,
    para emparejar `\.` o `\[`. Nótese que `\\` es en sí un caracter que
    necesita ser escapado, con lo que en `R` usaremos `\\.` o `\\[`.
-   `|`: El operador OR.
-   `(...)`: Agrupamiento. Nos permite marcar y definir secciones dentro
    de la expresión. Cada uno de estos agrupamientos puede ser
    recuperado usando `\\n`, en el que `n` es el número de
    agrupamientos.

<!-- -->

    (strings <- c("^ab", "ab", "abc", "abd", "abe", "ab 12"))

    ## [1] "^ab"   "ab"    "abc"   "abd"   "abe"   "ab 12"

    grep("ab.", strings, value = TRUE)

    ## [1] "abc"   "abd"   "abe"   "ab 12"

    grep("ab[c-e]", strings, value = TRUE)

    ## [1] "abc" "abd" "abe"

    grep("ab[^c]", strings, value = TRUE)

    ## [1] "abd"   "abe"   "ab 12"

    grep("^ab", strings, value = TRUE)

    ## [1] "ab"    "abc"   "abd"   "abe"   "ab 12"

    grep("\\^ab", strings, value = TRUE)

    ## [1] "^ab"

    grep("abc|abd", strings, value = TRUE)

    ## [1] "abc" "abd"

    gsub("(ab) 12", "\\1 34", strings)

    ## [1] "^ab"   "ab"    "abc"   "abd"   "abe"   "ab 34"

Clases de caracteres
--------------------

La clases de caracteres nos permiten listar tipos de caracteres, como
letras, números, puntuación, ... Hay dos tipos de notación para clases
de caracteres: una usa `[:` y la otra escapa un caracter especial, como
`\\w`.

-   `[:digit:]` or `\d`. Dígitos del 0 al 9, equivalente a `[0-9]`.
-   `\D`: Inverso de `\d`, es decir, equivalente a `[^0-9]`.
-   `[:lower:]`: Minúsculas, equivalente a `[a-z]`.
-   `[:upper:]`: Mayúsculas, equivalente a `[A-Z]`
-   `[:alpha:]`: Mayúsculas y minúsculas.
-   `[:alnum:]`: Caracteres alfanuméricos. Dígitos, mayúsculas y
    minúsculas. Equivalente a `\w`.
-   `\W`: Inverso de `w`.
-   `[:blank:]`: Espacio y tabulación.
-   `[:space:]`: Espacio: tabulación, nueva línea, retorno de línea y
    espacio.
-   `[:punct:]`: Puntuación.

Modos generales de patrones
---------------------------

`R` ofrece dos sintaxis para la gestión de expresiones regulares.

-   `POSIX`, que es activado por defecto.
-   `Perl`

El argumento `perl` en funciones como `grep` o `gsub` nos permite
cambiar entre un estilo u otro.
