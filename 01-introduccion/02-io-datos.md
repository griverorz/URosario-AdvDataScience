### Estructuras de datos (continuación)

`data.frame` es la pieza fundamental de todo lo que queremos hacer
cuando hacemos análisis de datos. Es útil pensarla como una `matrix` que
puede mantener columnas de diferentes tipos y que además contiene
nombres para cada columna.

    states <- data.frame("code"       = c("CA", "NY", "NE", "AZ"), 
                         "population" = c(38.8, 19.7, 2.1, 6.8), 
                         "region"     = c("West", "Northeast", "Midwest", "West"), 
                         "landlock"   = c(FALSE, FALSE, TRUE, TRUE))

Podemos acceder los elementos a través de índices del mismo modo que
haríamos con una `matrix` o podemos acceder a través de nombre:

    states[, 3] 
    states[, "region"]
    states$region

También podemos añadir variables:

    states$spanish <- c(28.5, 15.7, NA, 19.5)

O podemos editar valores usando algunas de las herramientas que hemos
visto hasta ahora:

    states$population[states$code == "NE"] <- 1.8

Examinemos con un poco de detalle qué es lo que ha ocurrido en la linea
anterior. `states$code` extrae la columna `code` de nuestros datos.
`states$code == "NE"` devuelve un vector lógico en el que solo la tercer
observación tiene valor `TRUE`. `states$population[states$code == "NE"]`
accede al valor del vector `population` para Nebraska. Ahí asignaremos
el valor 1.8.

La misma aproximación puede usarse para hacer selecciones de la base de
datos:

    states[states$population > 10,] # Precaución con la coma
    subset(states, population > 10)

La transformación de variables es sencilla

    states$spanish <- states$spanish * states$population/100 # Aunque podríamos haber usado una nueva variable

La base de datos también es útil para pensar acerca de ciertos tipos de
variables. Pensad en la variable `region`: es un vector de caracteres,
pero querríamos considerarlo como una variable discreta en la que cada
valor (un número) tendría asociada una etiqueta (el nombre de la
región). Esta estructura se llama `factor` en `R`.

    states$region <- factor(states$region)
    states$region
    levels(states$region)

Dependiendo de con quién habléis, pueden amarlos u odiarlos.

### Input/Output

Podemos escribir en el disco con valores separados por comas usando la
función `write.csv`.

    write.csv(states, file="states.csv")

El argumento `file` nos permite especificar el nombre el archivo en el
que guardaremos el `data.frame` `states`. El archivo se guarda en el
directorio en el que estemos trabajando, pero podríamos haber
especificado cualquier otro directorio si hubiésemos pasado la ruta
completa. Para comprobar cuál es el directorio de trabajo podemos usar:

    getwd()

    ## [1] "/Users/gonzalorivero/advanced-dscience/lecture-scripts/01-introduction"

y podemos cambiar el valor usando `setwd()`.

Para leer datos a `R` podemos usar la función `read.csv`.

    states <- read.csv("states.csv")
    states

El formato más común para `R` de todos modos es su formato binario
nativo, que usa la extensión `.RData` y que se genera y lee usando las
funciones `save` y `load`.

    save(states, file="states.RData")
    load("states.RData")
    states

Pero `R` puede leer (y a veces escribir) datos en otros formatos
binarios como Stata, SAS, SPSS, o incluso Excel. Las funciones para
gestionar estos formatos vienen en el paquete `foreign` que se instala
con `R`. Echad un vistazo a [la
documentación](https://cran.r-project.org/web/packages/foreign/index.html).
En breve veremos cómo usar paquetes adicionales enseguida.
