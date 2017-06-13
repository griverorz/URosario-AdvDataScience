Una de las mejores cosas de `R` es que programar es muy sencillo. Y eso
es bueno, porque la manipulación de datos, el análisis, o la
visualización se convierten en tareas más sencillas cuando uno puede
escribir pequeñas funciones. Además, dado que `R` es de código abierto,
puedes inspeccionar lo que hace cada una de las funciones, con lo que es
importante saber un poco de programación con el objetivo de poder
entender bién qué hace cada cosa.

### Definir funciones

Podemos pensar en una función como una forma de empaquetar operaciones
en una expresión reusable. Por ejemplo, consideramos un caso sencillo en
el que quisiésemos calcular la media de una variable. En lugar de
calcular `sum` sobre `lenght` en cada situación, podríamos simplemente
empaquetar esas dos operaciones y darles un nombre:

    my_mean <- function(x) sum(x)/length(x)
    my_mean(c(0, 1))

    ## [1] 0.5

Como vemos, para definir una función, usamos la palabra clave `function`
seguida de paréntesis y (opcionalmente), los argumentos que toma la
función. En el cuerpo, incluimos la expresión que la función ejecutará.
A esta instrucción le podemos asignar un nombre: en este caso,
`my_mean`.

Veamos cómo podemos ahora ver los contenidos que acabamos de definir.

    my_mean

    ## function(x) sum(x)/length(x)

La expresión que hemos escrito es válida, pero podemos ser más
explícitos si introducimos las instrucciones entre llaves e indicamos
que la expresión es lo que queremos que la función retorne.

    my_mean <- function(x) {
      return(sum(x)/length(x))
    }

Veamos ahora algunas estructuras de control:

### Iteraciones

`for` nos permite iterar y repetir operaciones a lo largo de una
secuencia. Como podéis imaginar, eso implica ser explícitos sobre dos
cosas: la secuencia y la variable que tomará valores sobre esa
secuencia.

    for (i in 1:3) print(i)

    ## [1] 1
    ## [1] 2
    ## [1] 3

Veamos un ejemplo menos trivial:

    myvector <- c(1, 2, 3, 4)
    out <- 0
    for (i in 1:length(myvector)) {
      out <- myvector[i] + out
    }
    out

    ## [1] 10

Aquí el vector `out` mantendrá el resultado de acumular progresivamente
los resultados de aplicar transformaciones sobre `myvector` `myvector`.
Tened en cuenta como `i` toma, en cada iteración, un valor a lo largo de
las posiciones de `myvector` que vamos usando para atravesar el objeto
paso a paso.

Podemos empaquetar estas operaciones en una función:

    my_sum <- function(x) {
      out <- 0
      for (i in 1:length(x)) {
        out <- x[i] + out
      }
      return(out)
    }
    my_sum(c(1, 2, 3, 4))

    ## [1] 10

Otra forma de construir iteraciones es con `while`. Evaluará una
expresión hasta que la condición que ésta defina no sea `TRUE`.

    i <- 0
    while (i < 3) {
      print(i)
      i <- i + 1
    }

    ## [1] 0
    ## [1] 1
    ## [1] 2

Como podéis ver, la condición `i < 3` pasa a ser `FALSE` en la tercera
iteración, con lo que `3` no se imprime en pantalla.

Hay casos en los que el uso de `which` es natural, como por ejemplo
cuando no sabemos cuántas iteraciones tenemos que hacer. Por ejemplo, en
el caso de algoritmos que queremos que corran hasta que hayan
convergido. Tomemos por ejemplo el cálculo del método de Newton-Raphson
para minimizar la función *x*<sup>2</sup>.

    tol <- 0.01
    x_last <- 10; diff <- 1
    while (abs(diff) > tol) {
        x <- x_last - (x_last^2)/(2*x_last)
        diff <- (x - x_last)
        x_last <- x
        print(x)
    }

    ## [1] 5
    ## [1] 2.5
    ## [1] 1.25
    ## [1] 0.625
    ## [1] 0.312
    ## [1] 0.156
    ## [1] 0.0781
    ## [1] 0.0391
    ## [1] 0.0195
    ## [1] 0.00977

Es obvio decirlo, pero es importante tener cuidado con `while` para no
acabar produciendo iteraciones infinitas.

### Conditionales

Como podéis imaginar, los condicionales ejecutan una operación
dependiendo del valor (`TRUE` o `FALSE`) de una expresión. En `R` usan
la siguiente sintaxis:

    if (x > 0) {
      print("It's positive") 
    } else if (x == 0) {
      print("It's neither positive nor negative")
    } else {
      print("It's negative")
    }

que podemos empaquetar en una función:

    my_sign <- function(x) {
      if (x > 0) {
        out <- "It's positive!"
      } else if (x == 0) {
        out <- "It's neither positive nor negative!"
      } else {
        out <- "It's negative!"
      }
      return(out)
    }

y podemos usar esta expresión para ilustrar qué ocurriría si no somos
cuidados en los detalles de la programación:

    my_sign("A cow")

    ## [1] "It's positive!"

¿Cómo gestionar este tipo de errores? Elevando un error:

    my_sign <- function(x) {
      if (!is.numeric(x)) {
        stop("Input is not a number")
      }
      
      if (x > 0) {
        out <- "It's positive!"
      } else if (x == 0) {
        out <- "It's neither positive nor negative!"
      } else {
        out <- "It's negative!"
      }
      return(out)
    }

Dos cosas que debemos resaltar aquí. Primero, que el operador `!` es el
operador negación (`TRUE == !FALSE`). Segundo, que `stop` interrumpe la
evaluación y produce un error, con lo que la función no llega al segundo
condicional.
