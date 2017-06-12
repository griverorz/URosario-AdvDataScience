Del *prompt* al *script*
------------------------

Hasta ahora hemos hecho todo a través del intérprete. De hecho, en la
práctica habitual haremos muchas cosas a través del intérprete, pero es
una buena idea mantener nuestro código en algún sitio más permanente. Es
en este caso en el que algo como RStudio se convierte en una buena idea
ya que lo que queremos es algo que nos permita editar archivos de texto
con herramientas que hagan sencillo trabajar con el código, pero que al
mismo tiempo permita enviar instrucciones al intérprete.

Análisis básico de datos
------------------------

Empecemos leyendo datos de Internet:

    affairs <- read.csv("http://koaning.io/theme/data/affairs.csv")

La base de datos contiene información sobre los *affairs* de 601
políticos y alguna información sociodemográfica. Una descripción de las
variables (y algunos resultados curiosos) se pueden encontrar en Fair,
R. (1977) "A note on the computation of the tobit estimator",
Econometrica, 45, 1723-1727.

Empecemos echándole un vistazo a los datos. Por ejemplo, podemos
imprimir en pantalla las primeras 6 líneas del archivo usando la función
`head`:

    head(affairs)

    ##      sex age    ym child religious education occupation rate nbaffairs
    ## 1   male  37 10.00    no         3        18          7    4         0
    ## 2 female  27  4.00    no         4        14          6    4         0
    ## 3 female  32 15.00   yes         1        12          1    4         0
    ## 4   male  57 15.00   yes         5        18          6    5         0
    ## 5   male  22  0.75    no         2        17          6    3         0
    ## 6 female  32  1.50    no         2        17          5    5         0

También podemos echarle un vistazo a algunos descriptivos usando la
función `summary` aplicada a variables:

    summary(affairs$nbaffairs)

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##    0.00    0.00    0.00    1.46    0.00   12.00

y

    summary(affairs$child)

    ##  no yes 
    ## 171 430

Es importante notar que `summary` hace cosas diferentes dependiendo el
input que reciba. En el primer caso, `summary` recibe una variable
numérica y produce la media y los cuartiles. En el segundo caso,
`summary` recibe un factor (una variable categórica) y produce una tabla
de frecuencias. Este es un patrón de comportamiento que veremos muy a
menudo en `R`.

Podemos ser más específicos y pedir una tabla de frecuencias con la
función:

    table(affairs$child)

    ## 
    ##  no yes 
    ## 171 430

Para transformar la tabla a proporciones podemos tomar diferentes rutas.
Una de ellas sería hacerlo manualmente, dividiendo las frecuencias por
el total de casos, tomando la longitud de la columna:

    table(affairs$child)/length(affairs$child)

    ## 
    ##    no   yes 
    ## 0.285 0.715

No hemos creado ninguna variable, sino que hemos realizado las
operaciones necesarias en una única llamada. La segunda opción es
"componer" dos funciones:

    prop.table(table(affairs$child))

    ## 
    ##    no   yes 
    ## 0.285 0.715

En ese caso, el resultado de `table` pasa a la función `prop.table`, la
cual transforma frecuencias a proporciones.

Hypothesis testing
------------------

Podemos empezar ahora a analizar los datos. Por ejemplo, imaginemos que
estamos interesados en la diferencia en la media de aventuras que tienen
los políticos dependiendo de si tienen hijos o no. La media muestral
para cada grupo puede calcularse como:

    mean(affairs$nbaffairs[affairs$child == "no"])
    mean(affairs$nbaffairs[affairs$child == "yes"])

Un contraste de *t* puede hacerse de varias formas. La forma más natural
es pasando variables a la función `t.test`. Por ejemplo, si quisiésemos
contrastar una variable contra la hipótesis nula habitual, usaríamos:

    t.test(affairs$nbaffairs)

    ## 
    ##  One Sample t-test
    ## 
    ## data:  affairs$nbaffairs
    ## t = 10, df = 600, p-value <2e-16
    ## alternative hypothesis: true mean is not equal to 0
    ## 95 percent confidence interval:
    ##  1.19 1.72
    ## sample estimates:
    ## mean of x 
    ##      1.46

También podríamos contrastar la igualdad de medias pasando *dos*
vectores a la función:

    t.test(affairs$nbaffairs[affairs$child == "no"], affairs$nbaffairs[affairs$child == "yes"])

    ## 
    ##  Welch Two Sample t-test
    ## 
    ## data:  affairs$nbaffairs[affairs$child == "no"] and affairs$nbaffairs[affairs$child == "yes"]
    ## t = -3, df = 400, p-value = 0.005
    ## alternative hypothesis: true difference in means is not equal to 0
    ## 95 percent confidence interval:
    ##  -1.286 -0.234
    ## sample estimates:
    ## mean of x mean of y 
    ##     0.912     1.672

Lo que es importante a tener en cuenta es que el segundo vector es un
argumento opcional y que la función hace algo diferente cuando el
segundo argumento existe. Echemos un vistazo a la documentación de
`t.test`:

    ?t.test

Vemos que existen dos métodos separados para interactuar con `t.test`:
el que acabamos de usar, pasando los argumentos `x` y tal vez `y`, y
otro que usa una `formula`. Las fórmulas son muy imporatntes en `R` y
las usaréis a menudo en la segunda mitad del curso:

    my_test <- t.test(nbaffairs ~ child, data=affairs)
    my_test

    ## 
    ##  Welch Two Sample t-test
    ## 
    ## data:  nbaffairs by child
    ## t = -3, df = 400, p-value = 0.005
    ## alternative hypothesis: true difference in means is not equal to 0
    ## 95 percent confidence interval:
    ##  -1.286 -0.234
    ## sample estimates:
    ##  mean in group no mean in group yes 
    ##             0.912             1.672

La parte izquierda de la ecuación es la variable que queremos contrastar
para cada uno de los valores indicados en el lado derecho. El argumento
`data` indica dónde viven esas variables: son columnas del `data.frame`
`affairs`. La interfaz de fórmula cobra más sentido si pensamos el
contraste como si fuese un modelo lineal. En ese caso caso, el lado
izquierdo de la fórmula, separado por el símbolo `~`, se corresponde con
el lado izquierdo de la ecuación. La parte derecha es una variable
ficticia (un factor) que divide la muestra en dos grupos.

Podemos inspeccionar los contenidos de `my_test` usando la función
`str`:

    str(my_test)

    ## List of 9
    ##  $ statistic  : Named num -2.84
    ##   ..- attr(*, "names")= chr "t"
    ##  $ parameter  : Named num 397
    ##   ..- attr(*, "names")= chr "df"
    ##  $ p.value    : num 0.00473
    ##  $ conf.int   : atomic [1:2] -1.286 -0.234
    ##   ..- attr(*, "conf.level")= num 0.95
    ##  $ estimate   : Named num [1:2] 0.912 1.672
    ##   ..- attr(*, "names")= chr [1:2] "mean in group no" "mean in group yes"
    ##  $ null.value : Named num 0
    ##   ..- attr(*, "names")= chr "difference in means"
    ##  $ alternative: chr "two.sided"
    ##  $ method     : chr "Welch Two Sample t-test"
    ##  $ data.name  : chr "nbaffairs by child"
    ##  - attr(*, "class")= chr "htest"

Tened en cuenta que `my_test` es una lista que contiene toda la
información relativa al contraste que hemos estimado. Esta información
está accesible a través de una lista:

    my_test$statistic
    my_test$conf.int
    my_test$estimate

Quizás sea ahora un buen momento para volver a la documentación y
comparar el resultado del contraste con lo que dice la sección "Value"
en la ayuda.
