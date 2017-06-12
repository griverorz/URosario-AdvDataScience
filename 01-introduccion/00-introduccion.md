¿Por qué `R`?
-------------

-   Es libre (*"free" as in "freedom" not as in "beer"*) y extensible.
    Además es gratuito. Además, tú (y la comunidad) puedes inspeccionar
    el código de cada función.
-   Es muy popular, lo que hace que haya muchas colecciones de funciones
    escritas para todo tipo de tareas especializadas.
-   Sus herramientas de visualización son muy famosas y usadas en todo
    tipo de areas profesionales.
-   Tiene una documentación excelente y muchos foros online de ayuda.
-   Una comunidad de usuarios muy activa y dispuesta a ayudar.

¿Qué es `R`?
------------

Es un lenguaje de programación y un entorno estadístico:

-   Es intepretado: Permite ejecuciones directas de código.
-   Tiene tipado dinámico: Es más fácil y mucho menos repetitivo que
    lenguajes convencionales.
-   Es multiparadigma: Orientado a escribir funciones.

La extensión de los archivos de `R` es `.R`.

RStudio
-------

R puede descargarse del Comprehensive R Archive Network,
[CRAN](https//cran.r-project.org). Para nuevos usuarios, RStudio es más
sencillo que trabajar con el intérprete directamente. RStudio es una IDE
muy popular, aunque por supuesto existen otras alternativas:

-   `emacs` a través de [ESS](http://ess.r-project.org/) (que es lo que
    yo usaré).
-   `vim` con
    [Vim-R-Plugin](http://www.vim.org/scripts/script.php?script_id=2628).
-   Sublime Text.

Algunos recursos útiles
-----------------------

Hay una enorme colección de materiales disponibles tanto en Internet
como en papel para aprender a usar `R`. El [Journal of Statistical
Software](http://www.jstatsoft.org/index) y la serie [Use
R!](http://www.springer.com/series/6991) de Springer publican
habitualmente introducciones a diferentes aplicaciones de `R`.

Una buena fuente para nuevos usuarios es [Learning
R](http://shop.oreilly.com/product/0636920028352.do).

La documentación oficial en CRAN (The Comprehensive R Archive Network)
es excelente, pero va mucho más allá de lo que intentaremos cubrir en
esta clase.

Una primera mirada a RStudio
----------------------------

RStudio tiene cuatro ventanas principales.

-   Console (el intérprete de `R`).
-   Code, en donde escribiremos el código.
-   History/Environment
-   Plots/Packages/Help

Conseguir ayuda
---------------

La documentación de `R` tiene una merecida reputación de ser excelented.
Puede accederse a ella desde el intérprete. Por ejemplo, si quisiésemos
obtener información sobre qué hace la función `lm`, o qué parámetros
toma, o incluso ver algunos ejemplos de uso, entraríamos en la consola:

    ?lm

La comunidad de usuarios de `R` es muy útil y activa. Si en algún
momento os quedáis atascados en algún problema, la mejor solución es
preguntar en [StackOverflow](http://stackoverflow.com/), una comunidad
de programadores, usando la etiqueta `#r`.

Por razones obvias, los usuarios se suelen referir al lenguaje como
"Rstats" para facilitar la búsqueda en Internet (probad `#RStats`).
