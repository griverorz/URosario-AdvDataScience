### El intérprete

Empecemos usando el intérprete para familiarizarnos con algunas
operaciones aritméticas básicas. Todas funcionan del modo en el que uno
esperaría.

    1 + 1
    sqrt(2)
    exp(1)
    2^3

Lo mismo ocurre con las comparaciones:

    1 > 1
    0 == log(1)
    (1 + 1 == 2) | (1 + 1 == 3)
    (1 + 1 == 2) & (1 + 1 == 3)

Podemos asignar valores a un nombre usando el *operador de asignación*
`<-`. `R` también acepta `=` para hacer asignaciones, pero no siempre y
además `<-` es mucho más popular. Por ejemplo, podemos asignar el valor
`1` a la variable `a`, de tal forma que cada vez que llamemos a `a`
obtendremos el valor al que la variable se refiere.

    a <- 1
    a

Tipos de datos
--------------

`R` ofrece varios tipos de datos. Los más relevantes son: *numeric*,
*integer*, *character*, and *logical*.

    1.0 # Numeric
    1 # "Integer"
    "ab" # Character
    TRUE # logical

El `#` indica un comentario, que puede estar o no al principio de una
línea.

`R` también ofrece constantes lógicas para los valores perdidos (`NA`),
"Not a Number" (`NaN`) y valores para infinito positivo y negativo
(`Inf` y `-Inf`):

    NA
    0/0
    1/0

Puedes comprobar el tipo de un objeto usando las funciones `is.` (*"is
`x` an integer?"*):

    is.numeric(1.0)
    is.integer(1)
    is.character("ab")
    is.logical(FALSE)

Un error común que todos comentemos en algún momento es comprobar que un
objeto es `NA` mediante la comparación con `==`.

    NA == NA

    ## [1] NA

Hay buenas razones para este tipo de comportamiento. Encuentro esta
explicación muy acertada:
[StackOverflow](http://stackoverflow.com/questions/25100974/na-matches-na-but-is-not-equal-to-na-why):

> Think of NA as meaning "I don't know what's there". The correct answer
> to 3 &gt; NA is obviously NA because we don't know if the missing
> value is larger than 3 or not. Well, it's the same for NA == NA. They
> are both missing values but the true values could be quite different,
> so the correct answer is "I don't know."

Tenemos funciones especiales para comprobar que un objeto es `NA`:

    is.na(NA)

    ## [1] TRUE

y lo mismo se puede decir de `NaN`.

`is.numeric` es una *función,* y todas las funciones en `R` funcional
del mismo modo. Tienen asociado un nombre seguido de sus argumentos
rodeados por paréntesis: `any_function(x)`. A lo largo de las siguientes
sesiones veremos y aprenderemos mucho más sobre las funciones en `R`.

### Estructuras de datos

La estructuras más comunes (e intuitivas) son `vector`, `matrix`,
`data.frame` y `list`. Veremos más sobre el `data.frame` en la siguiente
sección.

Un `vector` contiene datos *del mismo tipo* (o `NA`). Los vectores se
pueden crear mediante la función `c`, que puede leerse como
"concatenar".

    c(1, 2)
    c(1, 2, NA)
    c(1, "a") # Atención a cómo el primer elemento cambia de tipo

Hay algunas funciones útiles que nos permiten crear vectores con una
estructura particular. Por ejemplo, podemos crear una secuencia usando
la función `seq`:

    c(1, 2, 3, 4)
    seq(1, 4)
    1:4 # Un sinómimo de `seq`

¿Habéis comprobado cómo hemos usado una función con dos argumentos? Es
quizás un buen momento para echar un vistazo a la documentación y ver
más información sobre las función `seq` usando `?seq`

La estructura `matrix` es, como esperaríamos, una matriz: una tabla
rectangular de elementos del mismo tipo.

    A <- matrix(c(1, 2, 3, 4), nrow=2, ncol=2)
    A

    ##      [,1] [,2]
    ## [1,]    1    3
    ## [2,]    2    4

Parad un momento para entender qué hemos hecho. Hemos tomado un objeto
del tipo `vector` y hemos rellenado la matriz en un orden concreto
pasando las dimensiones que queremos usar para transformar el vector.
Quizás resulte poco intuitivo, pero si lo pensáis tiene sentido: una
`matrix` de una sola dimensión no es un `vector`.

Las operaciones en matrices funcionan "más o menos" como uno esperaría:

    B <- matrix(c(4, 3, 2, 1), nrow=2)
    A * B

    ##      [,1] [,2]
    ## [1,]    4    6
    ## [2,]    6    4

    A %*% B

    ##      [,1] [,2]
    ## [1,]   13    5
    ## [2,]   20    8

Comprobad que la primera operación ocurre a nivel de elementos, mientras
que la segunda es la multiplicación matricial habitual. También tenemos
operadores comunes para extraer la diagonar (que es un `vector`) o para
calcular la inversa:

    t(A)
    diag(A)
    solve(A) # pero A^-1...

Una `list` es una colección ordenada de objetos, posiblemente de tipos
diferentes.

    list("first"=c(1, 2, 3), "second"=c("a", "b"), "third"=matrix(NA, ncol=2, nrow=2))

Las listas juegan un papel fundamental en `R` y las usaremos
constantemente.

### Índices

Los índices nos permiten acceder a los elementos de un `vector` o
`matrix` a través de su posicíon en el objeto:

    a <- c(1, 2, 3)
    a[1]
    a[1:2]
    a[-3]
    a[1] <- 0

Una matriz tiene dos dimensiones, así que necesitaremos dos elementos
para indicar celdas en nuestra estructura, pero al margen de eso, los
índices de una matriz se rigen a través de los mismos principios:

    A <- matrix(c(1, 2, 3, 4), nrow=2)
    A[1, 1]
    A[1, ] # Un espacio vacio indica "todos los elementos de esta dimensión"
    A[, 1]
    A[2, 2] <- 0
    A[2, ] <- 0 # Regla de reciclado

Una lista usa una notación similar, pero con dos corchetes en lugar de
uno. Las listas tienen además otras formar de acceder a los elementos.

    my_list <- list("a"=1, "b"=2, "c"=NA)
    my_list[["a"]] # Nombre
    my_list[[1]] # Posición
    my_list$a <- c("Not 1 anymore")

Ved cómo hemos accedido a elementos tanto por nombre como por posición.
