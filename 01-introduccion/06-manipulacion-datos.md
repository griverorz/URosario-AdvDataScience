`R` incluye mucha funcionalidad para hacer la manipulación de datos más
sencilla y es imposible ni siquiera dar una panorámica de todas las
herramientas disponibles. Sin embargo, la mayor parte de las cosas que
haremos durante los próximos días caerán en tres categorías: agrupar
datos, fusionar dato y restructurar datos. Además, las tres categorías
resumen bien el modo en el que `R` opera en análisis más prácticos.

Split-apply-combine
-------------------

Consideremos la base de dato de impuestos al tabaco. Los datos estaban
agrupados por año y estado y teníamos información sobre la carga
impositiva al tabaco y la renta de cada estado. Imaginemos que estamos
interesados en la carga impositiva media para cada año con el objetivo
de ver cómo evoluciona en el tiempo. La estructura del problema es algo
que veremos en muchas ocasiones a lo largo de estos días. Queremos
dividir los datos por grupos definidos por años, calcular la media para
cada uno de esos grupos, y después juntar los grupos de nuevo para tener
una base de datos. Veremos por encima tres formas de conseguir ese
objetivo.

    tobacco <- read.csv("http://koaning.io/theme/data/cigarette.csv")

Empezaremos por funciones que vienen en el paquete base de `R` porque
ilustran bien el proceso que las otras dos aproximaciones intentan
simplificar. Esto es lo que queremos hacer, paso por paso

    head(tobacco)

    ##   state year  cpi      pop packpc    income  tax avgprs taxs
    ## 1    AL 1985 1.08  3973000    116  46014968 32.5  102.2 33.3
    ## 2    AR 1985 1.08  2327000    129  26210736 37.0  101.5 37.0
    ## 3    AZ 1985 1.08  3184000    105  43956936 31.0  108.6 36.2
    ## 4    CA 1985 1.08 26444000    100 447102816 26.0  107.8 32.1
    ## 5    CO 1985 1.08  3209000    113  49466672 31.0   94.3 31.0
    ## 6    CT 1985 1.08  3201000    109  60063368 42.0  128.0 51.5

    mean(tobacco$tax[tobacco$year == 1985])

    ## [1] 31.7

    mean(tobacco$tax[tobacco$year == 1986])

    ## [1] 32.8

    mean(tobacco$tax[tobacco$year == 1987])

    ## [1] 33.6

Ahora que sabemos hacer bucles, podemos pensar en la siguiente forma de
aproximar el problema

    for (i in 1985:1990) {
        print(mean(tobacco$tax[tobacco$year == i]))
    }

    ## [1] 31.7
    ## [1] 32.8
    ## [1] 33.6
    ## [1] 35.2
    ## [1] 35.8
    ## [1] 37.6

y podríamos añadir más información para saber a qué año se corresponde
cada media

    for (i in 1985:1990) {
        print(cbind(i, mean(tobacco$tax[tobacco$year == i])))
    }

    ##         i     
    ## [1,] 1985 31.7
    ##         i     
    ## [1,] 1986 32.8
    ##         i     
    ## [1,] 1987 33.6
    ##         i     
    ## [1,] 1988 35.2
    ##         i     
    ## [1,] 1989 35.8
    ##         i     
    ## [1,] 1990 37.6

e incluso almacenar la información

    output <- data.frame("year"=unique(tobacco$year), "ave"=NA)
    for (i in 1:nrow(output)) {
        output$ave[i] <-  mean(tobacco$tax[tobacco$year == output$year[i]])
    }

Aunque el proceso es intuitivo, necesitamos poner cierto esfuerzo para
interpretar el código. Una forma diferente de atacar el problema es
siendo más directo. En lugar de hacer un bucle, indicaremos a `R` cómo
hacer la partición.

    split_tobacco <- split(tobacco$tax, tobacco$year)
    head(split_tobacco)

    ## $`1985`
    ##  [1] 32.5 37.0 31.0 26.0 31.0 42.0 30.0 37.0 28.0 34.0 25.1 28.0 26.5 32.0
    ## [15] 19.0 32.0 42.0 29.0 36.0 37.0 34.0 29.0 27.6 32.0 18.0 34.0 34.0 33.0
    ## [29] 41.0 28.0 31.0 37.0 30.0 34.0 35.0 34.0 39.0 23.0 31.0 29.0 35.2 28.0
    ## [43] 18.5 33.0 39.0 41.0 33.0 24.0
    ## 
    ## $`1986`
    ##  [1] 32.5 37.0 31.0 26.0 31.0 42.0 30.0 37.0 28.0 40.0 25.1 32.7 26.5 38.0
    ## [15] 19.0 32.0 42.0 29.0 42.0 37.0 39.0 29.0 34.0 32.0 18.0 34.0 35.7 33.0
    ## [29] 41.0 28.0 31.0 37.0 30.0 34.0 41.0 34.0 39.4 23.0 39.0 29.0 36.3 28.0
    ## [43] 18.5 33.0 41.0 41.0 33.0 24.0
    ## 
    ## $`1987`
    ##  [1] 32.5 37.0 31.0 26.0 36.0 42.0 30.0 40.0 28.0 42.0 27.3 36.0 26.5 40.0
    ## [15] 19.0 32.0 42.0 29.0 44.0 37.0 40.2 29.0 34.0 32.0 18.0 34.0 39.0 33.0
    ## [29] 41.0 31.0 31.0 37.0 30.0 34.4 43.0 34.0 41.0 23.0 39.0 29.0 36.5 29.8
    ## [43] 18.5 33.0 47.0 41.0 33.0 24.0
    ## 
    ## $`1988`
    ##  [1] 32.5 37.0 31.0 26.0 36.0 42.0 30.0 40.0 28.0 44.7 34.0 36.0 31.5 40.0
    ## [15] 19.0 32.0 42.0 29.0 44.0 39.0 54.0 29.0 34.0 32.0 18.0 43.0 43.0 33.0
    ## [29] 43.0 31.0 36.0 37.0 34.0 39.0 43.0 34.0 41.0 23.0 39.0 29.0 40.6 39.0
    ## [43] 18.5 33.0 47.0 45.2 33.0 24.0
    ## 
    ## $`1989`
    ##  [1] 32.5 37.0 31.0 38.5 36.0 45.5 30.0 40.0 28.0 50.0 34.0 36.0 31.5 40.0
    ## [15] 19.0 32.0 42.0 29.0 44.0 41.0 54.0 29.0 34.0 32.0 18.0 43.0 43.0 33.0
    ## [29] 43.0 31.0 36.0 39.0 34.0 39.0 43.0 34.0 43.0 23.0 39.0 29.0 42.0 39.0
    ## [43] 18.5 33.0 47.2 46.0 33.0 24.0
    ## 
    ## $`1990`
    ##  [1] 32.5 37.0 31.0 51.0 36.0 56.0 30.0 40.0 28.0 47.0 34.0 46.0 31.5 40.0
    ## [15] 19.0 32.0 42.0 29.0 46.2 41.0 54.0 29.0 34.0 33.5 18.0 46.0 43.0 38.7
    ## [29] 43.0 31.0 51.0 49.5 34.0 39.0 43.7 34.0 53.0 23.0 39.0 29.0 42.0 39.0
    ## [43] 18.5 33.0 50.0 46.0 33.0 28.0

y a continuación aplicaremos una media sobre cada uno de los elementos.

    mean_tobacco <- lapply(split_tobacco, mean)
    head(mean_tobacco)

    ## $`1985`
    ## [1] 31.7
    ## 
    ## $`1986`
    ## [1] 32.8
    ## 
    ## $`1987`
    ## [1] 33.6
    ## 
    ## $`1988`
    ## [1] 35.2
    ## 
    ## $`1989`
    ## [1] 35.8
    ## 
    ## $`1990`
    ## [1] 37.6

y por último juntamos los elementos de nuevo usando la función `do.call`
que nos pide que indiquemos *cómo* hemos de realizar el pegado:

    do.call(rbind, mean_tobacco)

    ##      [,1]
    ## 1985 31.7
    ## 1986 32.8
    ## 1987 33.6
    ## 1988 35.2
    ## 1989 35.8
    ## 1990 37.6
    ## 1991 40.9
    ## 1992 43.9
    ## 1993 47.1
    ## 1994 51.6
    ## 1995 53.7

Esta es la esencia de la estrategia de dividir (usando `split`), aplicar
(usando `lapply`) y combinar (usando `do.call`).

Podemos conseguir lo mismo de otros dos modos usando dos paquetes muy
populares en `R`. Una opción sería usar el paquete `plyr` que ofrece una
interfaz común, clara e intuitiva para operaciones que tienen todas la
misma naturaleza.

El uso es muy similar. El primer argumento indica los datos que queremos
particionar, el segundo la variable que usaremos para particionar
(aunque quizás no es necesario, como veremos enseguida) y el tercero la
operación que queremos realizar. Un par de ejemplos serán de utilidad.
Empezaremos por instalar el paquete `plyr` que no viene por defecto:

    install.packages("plyr"); library(plyr)

    ## Installing package into '/usr/local/lib/R/site-library'
    ## (as 'lib' is unspecified)

y ahora

    group_means <- ddply(tobacco, .(year), function(x) mean(x$tax))
    head(group_means)

    ##   year   V1
    ## 1 1985 31.7
    ## 2 1986 32.8
    ## 3 1987 33.6
    ## 4 1988 35.2
    ## 5 1989 35.8
    ## 6 1990 37.6

El nombre de la función `ddply` nos da mucha información sobre qué es lo
que está ocurriendo. La primera letra `d` nos dice que el input será un
`data.frame`, la segunda letra `d` que el output es otro `data.frame`.
Todas las funciones en `plyr` tienen la misma estructura. Por ejemplo,
podríamos calcular la media por grupos y devolver el resultado como una
lista cambiando la segunda letra de `d` a `l`.

    head(dlply(tobacco, .(year), function(x) mean(x$tax)))

    ## $`1985`
    ## [1] 31.7
    ## 
    ## $`1986`
    ## [1] 32.8
    ## 
    ## $`1987`
    ## [1] 33.6
    ## 
    ## $`1988`
    ## [1] 35.2
    ## 
    ## $`1989`
    ## [1] 35.8
    ## 
    ## $`1990`
    ## [1] 37.6

o podríamos haber hecho los cálculos usando la lista que creamos más
arriba cuando demostrábamos `split`. En este caso, el input es una
lista, por lo que la primera letra de la función será `l` y no
necesitamos especificar la variable de agrupación ya que los datos ya
están agrupados (lo cual debería ayudarnos a entender que `plyr`, igual
que en el proceso manual, usa una lista como pivote):

    ldply(split_tobacco, mean)

    ##     .id   V1
    ## 1  1985 31.7
    ## 2  1986 32.8
    ## 3  1987 33.6
    ## 4  1988 35.2
    ## 5  1989 35.8
    ## 6  1990 37.6
    ## 7  1991 40.9
    ## 8  1992 43.9
    ## 9  1993 47.1
    ## 10 1994 51.6
    ## 11 1995 53.7

El tercer método es similar a `plyr` pero es un conjunto de funciones
que están especializadas en `ddply`, esto es, funciones que toman y
devuelven un `data.frame`. La sintaxis es un poco diferente a lo que
hemos visto hasta ahora pero está convirtiéndose en muy popular, así que
es importante no perder este paquete de vista. Empezaremos por instalar
y cargar el paquete:

    install.packages("dplyr"); library(dplyr)

    ## Installing package into '/usr/local/lib/R/site-library'
    ## (as 'lib' is unspecified)

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:plyr':
    ## 
    ##     arrange, count, desc, failwith, id, mutate, rename, summarise,
    ##     summarize

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

El paquete funciona mediante *verbos* encadenados con operadores (`%>%`)
que concatenan operaciones y tienen una enorme similitud a SQL. Por
ejemplo, lo que queremos hacer es *agrupar* los datos por `year` y
después *resumirlos* para la variable `tax`. Aquí enfatizo *resumir* por
oposición a *mutar* en el sentido de que el resultado es un *resumen* y
no una transformación de la columna: cada grupo de observaciones será
resumido en un único valor: la media.

    tobacco %>%
        group_by(year) %>%
        summarize(ave=mean(tax))

    ## # A tibble: 11 x 2
    ##     year   ave
    ##    <int> <dbl>
    ##  1  1985  31.7
    ##  2  1986  32.8
    ##  3  1987  33.6
    ##  4  1988  35.2
    ##  5  1989  35.8
    ##  6  1990  37.6
    ##  7  1991  40.9
    ##  8  1992  43.9
    ##  9  1993  47.1
    ## 10  1994  51.6
    ## 11  1995  53.7

¿Qué pasaría si cambiasemos el verbo `summarize` por el verbo mutar?

    mtobacco <- tobacco %>%
        group_by(year) %>%
        mutate(ave=mean(tax))
    head(mtobacco)

    ## # A tibble: 6 x 10
    ## # Groups:   year [1]
    ##    state  year   cpi      pop packpc    income   tax avgprs  taxs   ave
    ##   <fctr> <int> <dbl>    <int>  <dbl>     <int> <dbl>  <dbl> <dbl> <dbl>
    ## 1     AL  1985  1.08  3973000    116  46014968  32.5  102.2  33.3  31.7
    ## 2     AR  1985  1.08  2327000    129  26210736  37.0  101.5  37.0  31.7
    ## 3     AZ  1985  1.08  3184000    105  43956936  31.0  108.6  36.2  31.7
    ## 4     CA  1985  1.08 26444000    100 447102816  26.0  107.8  32.1  31.7
    ## 5     CO  1985  1.08  3209000    113  49466672  31.0   94.3  31.0  31.7
    ## 6     CT  1985  1.08  3201000    109  60063368  42.0  128.0  51.5  31.7

En este caso, lo que estamos pidiendo es una *mutación* de la base de
datos original, de tal forma que cada entrada tiene asociada la media de
la variable `tax` para su año correspondiente.

Una cosa a enfatizar es la generalidad de este tipo de aproximación.
Muchas operaciones, no solo el cálculo de medidas resumen, se pueden
concebir como parte de `split-apply-combine`. Pensemos, por ejemplo, en
cómo el consumo de tabaco cambiá en relación a los impuestos para cada
año. Una aproximación, no la mejor, sería hacer una regresión separada
entre consumo e impuestos para cada año. Para ver el efecto, solo
queremos ver los coeficientes:

    lm_models <- dlply(tobacco, ~ year, function(x) lm(packpc ~ log(tax), data=x))
    lm_coefs <- ldply(lm_models, coefficients)
    lm_coefs

    ##    year (Intercept) log(tax)
    ## 1  1985         236    -33.0
    ## 2  1986         244    -36.1
    ## 3  1987         248    -37.8
    ## 4  1988         267    -43.3
    ## 5  1989         276    -47.0
    ## 6  1990         255    -42.0
    ## 7  1991         245    -39.2
    ## 8  1992         252    -40.9
    ## 9  1993         263    -43.6
    ## 10 1994         263    -43.0
    ## 11 1995         290    -49.0

Hay dos cosas a tener en cuenta aquí. En primer lugar que estamos
transformando un `data.frame` en una lista (`dlply`) y después la lista
resultante en otro `data.frame` (`ldply`). Verifica que entiendes por
qué hacemos esto. La otra cosa a tener en cuenta es que hemos generado
una función anónima para calcular cada regresión. El argumento que
necesitamos para especificar qué hacer en cada grupo es una función que
toma un único parámetro, en este caso la base de datos correspodiente a
ese año. Como es una función concreta que solo necesitamos para una
única tarea, no nos molestamos ni siquiera en darle un nombre.

### Fusionar dos bases de datos

Por ahora hemos trabajado con un único `data.frame` que vive en memoria.
Sin embargo, en muchas ocasiones (bien por que los datos vienen en
estructuras diferentes o por que generamos bases separadas en el
transcurso de nuestras operaciones) necesitaremos *fusionar* bases de
datos que contienen una llave común. Pensemos en un ejemplo trivial en
el que queremos tomar los coeficientes que acabamos de estimar y los
queremos juntar de nuevo en nuestra base de datos original.

    merged_tobacco <- merge(tobacco, lm_coefs, by="year")
    head(merged_tobacco)

    ##   year state  cpi      pop packpc    income  tax avgprs taxs (Intercept)
    ## 1 1985    AL 1.08  3973000    116  46014968 32.5  102.2 33.3         236
    ## 2 1985    AR 1.08  2327000    129  26210736 37.0  101.5 37.0         236
    ## 3 1985    AZ 1.08  3184000    105  43956936 31.0  108.6 36.2         236
    ## 4 1985    CA 1.08 26444000    100 447102816 26.0  107.8 32.1         236
    ## 5 1985    CO 1.08  3209000    113  49466672 31.0   94.3 31.0         236
    ## 6 1985    CT 1.08  3201000    109  60063368 42.0  128.0 51.5         236
    ##   log(tax)
    ## 1      -33
    ## 2      -33
    ## 3      -33
    ## 4      -33
    ## 5      -33
    ## 6      -33

La función `merge` toma otros argumentos que nos permiten especificar
diferentes clases de `join`, qué hacer con las unidades que no existen
en las dos bases de datos (¿las incluimos o las dejamos?), o qué hacer
con variables que existen en las dos bases de datos.

### Restructurar un base de datos

Una operación que haremos mucho a lo largo de los dos siguientes días
será restructurar el formato de nuestra base de datos. Por ejemplo,
ahora nuestra base de datos sobre consumo de tabaco está estructurada
por pares estado-año.

    head(tobacco)

    ##   state year  cpi      pop packpc    income  tax avgprs taxs
    ## 1    AL 1985 1.08  3973000    116  46014968 32.5  102.2 33.3
    ## 2    AR 1985 1.08  2327000    129  26210736 37.0  101.5 37.0
    ## 3    AZ 1985 1.08  3184000    105  43956936 31.0  108.6 36.2
    ## 4    CA 1985 1.08 26444000    100 447102816 26.0  107.8 32.1
    ## 5    CO 1985 1.08  3209000    113  49466672 31.0   94.3 31.0
    ## 6    CT 1985 1.08  3201000    109  60063368 42.0  128.0 51.5

Cada fila representa un año en un determinado estado. Sin embargo,
podemos querer cambiar el formato de tal forma que cada fila represente
datos para un determinado año. Por supuesto, y como siempre, existen
modos de hacer esto usando las librerías base en `R`, pero creo que el
paquete `reshape2` nos ofrece una vía más directa e intuitiva. Las
operaciones aquí se dividen en dos funciones: una operación *derrite*
(*melts*) la base de datos de acuerdo con algunas variables que hagan de
índice. La segunda *funde* (*casts*) en la estructura que nosotros
queramos. Por ejemplo, para el problema anterior podemos hacer:

    library(reshape2)
    molten_tobacco <- melt(tobacco[, c("state", "year", "tax")], id=c("state", "year"))
    head(molten_tobacco)

    ##   state year variable value
    ## 1    AL 1985      tax  32.5
    ## 2    AR 1985      tax  37.0
    ## 3    AZ 1985      tax  31.0
    ## 4    CA 1985      tax  26.0
    ## 5    CO 1985      tax  31.0
    ## 6    CT 1985      tax  42.0

    tail(molten_tobacco)

    ##     state year variable value
    ## 523    VA 1995      tax  26.5
    ## 524    VT 1995      tax  44.0
    ## 525    WA 1995      tax  80.5
    ## 526    WI 1995      tax  62.0
    ## 527    WV 1995      tax  41.0
    ## 528    WY 1995      tax  36.0

    head(dcast(molten_tobacco, state ~ variable + year))

    ##   state tax_1985 tax_1986 tax_1987 tax_1988 tax_1989 tax_1990 tax_1991
    ## 1    AL     32.5     32.5     32.5     32.5     32.5     32.5     34.5
    ## 2    AR     37.0     37.0     37.0     37.0     37.0     37.0     39.0
    ## 3    AZ     31.0     31.0     31.0     31.0     31.0     31.0     35.2
    ## 4    CA     26.0     26.0     26.0     26.0     38.5     51.0     53.0
    ## 5    CO     31.0     31.0     36.0     36.0     36.0     36.0     38.0
    ## 6    CT     42.0     42.0     42.0     42.0     45.5     56.0     58.0
    ##   tax_1992 tax_1993 tax_1994 tax_1995
    ## 1     36.5     38.5     40.5     40.5
    ## 2     42.0     49.2     55.5     55.5
    ## 3     38.0     40.0     42.0     65.3
    ## 4     55.0     57.0     60.0     61.0
    ## 5     40.0     42.0     44.0     44.0
    ## 6     63.8     67.0     71.0     74.0

Vayamos paso por paso en lo que ha pasado antes. En primer lugar, hemos
tomado únicamente tres variables de la base de datos y hemos pedido a
`R` que la transforme de tal manera que `state` e `year` permanezcan
como índices en las filas. En este caso, lo único que estamos haciendo
es añadir dos nuevas variables `variable` y `value` que capturan las
variables y valores que no son `id`. Si hacemos `melt` sin seleccionar
variables lo podemos ver con más claridad

    head(melt(tobacco, id=c("state", "year")))

    ##   state year variable value
    ## 1    AL 1985      cpi  1.08
    ## 2    AR 1985      cpi  1.08
    ## 3    AZ 1985      cpi  1.08
    ## 4    CA 1985      cpi  1.08
    ## 5    CO 1985      cpi  1.08
    ## 6    CT 1985      cpi  1.08

    tail(melt(tobacco, id=c("state", "year")))

    ##      state year variable value
    ## 3691    VA 1995     taxs  34.4
    ## 3692    VT 1995     taxs  52.4
    ## 3693    WA 1995     taxs  96.1
    ## 3694    WI 1995     taxs  71.6
    ## 3695    WV 1995     taxs  50.4
    ## 3696    WY 1995     taxs  36.0

Aquí vemos con claridad que todas las variables han sido transformadas
para que aparezcan en una única columna con su nombre y su valor. La
siguiente fase es `cast` que nos permite, usando una fórmula, indicar
qué variables van a permanecer en las filas y cuáles en las columnas. En
este caso, la izquierda del símbolo `~` representa las filas y la
derecha las columnas.

Así, por ejemplo, en el caso anterior lo que queríamos era pivotar la
variable `tax` a las columnas y añadir a esta el valor de año, mientras
que en las filas permanece la variable `state`

    head(dcast(molten_tobacco, state ~ variable + year))

    ##   state tax_1985 tax_1986 tax_1987 tax_1988 tax_1989 tax_1990 tax_1991
    ## 1    AL     32.5     32.5     32.5     32.5     32.5     32.5     34.5
    ## 2    AR     37.0     37.0     37.0     37.0     37.0     37.0     39.0
    ## 3    AZ     31.0     31.0     31.0     31.0     31.0     31.0     35.2
    ## 4    CA     26.0     26.0     26.0     26.0     38.5     51.0     53.0
    ## 5    CO     31.0     31.0     36.0     36.0     36.0     36.0     38.0
    ## 6    CT     42.0     42.0     42.0     42.0     45.5     56.0     58.0
    ##   tax_1992 tax_1993 tax_1994 tax_1995
    ## 1     36.5     38.5     40.5     40.5
    ## 2     42.0     49.2     55.5     55.5
    ## 3     38.0     40.0     42.0     65.3
    ## 4     55.0     57.0     60.0     61.0
    ## 5     40.0     42.0     44.0     44.0
    ## 6     63.8     67.0     71.0     74.0
