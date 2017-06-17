El análisis de temas en minería de texto, como hemos visto, es un caso
particular del aprendizaje no-supervisado en el que queremos agrupar
documentos de acuerdo con la frecuencia de términos que contienen.

    library(stringdist)
    library(tm)

    ## Loading required package: NLP

    library(stringi)

Empezaremos leyendo el listado de archivos que contienen las notas de
prensa. Recordemos que cada nota está en un documento de texto separado
bajo una carpeta que lleva el nombre del senador.

    allfiles <- list.files("./dta/senate-releases", full.names=TRUE, recursive=TRUE)
    head(allfiles)

    ## [1] "./dta/senate-releases/Akaka/10Apr2007akaka175.txt"
    ## [2] "./dta/senate-releases/Akaka/10Feb2006akaka132.txt"
    ## [3] "./dta/senate-releases/Akaka/10Jan2006akaka144.txt"
    ## [4] "./dta/senate-releases/Akaka/10Jan2007akaka238.txt"
    ## [5] "./dta/senate-releases/Akaka/10Jul2006akaka46.txt" 
    ## [6] "./dta/senate-releases/Akaka/10Jul2007akaka109.txt"

Usando expresiones regulares, podemos extraer del nombre del archivo
toda la información que necesitamos sobre el autor, la fecha y, además,
tener un identificador de cada documento.

    extraer_metadatos <- function(x) {
        str <- ".*/([a-zA-Z]{1,})/([0-9]{1,2}[a-zA-Z]{3}[0-9]{4}).*([0-9]{1,3}).txt$"
        nombre <- gsub(str, "\\1", x)
        fecha <- gsub(str, "\\2", x)
        narchivo <- gsub(str, "\\3", x)
        return(data.frame("nombre"=nombre,
                          "fecha"=strptime(fecha, "%d%b%Y"),
                          "narchivo"=narchivo))
    }

Tomaremos una muestra aleatoria simple de documentos para los siguientes
análisis.

    allfiles <- sample(allfiles, 1000) ## Sampling!
    metadatos <- extraer_metadatos(allfiles)
    head(metadatos)

    ##      nombre      fecha narchivo
    ## 1   Kennedy 2005-09-08        3
    ## 2   Bennett 2007-09-19        9
    ## 3   Roberts 2007-10-04        3
    ## 4      Reid 2005-05-12        2
    ## 5 Alexander 2007-06-29        7
    ## 6  Mikulski 2006-05-05        0

Ahora podemos leer los documentos en nuestra sesión

    alltexts <- lapply(allfiles, function(x) readLines(x, encoding="ISO-8859"))

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kennedy/08Sep2005Kennedy3.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Bennett/19Sep2007Bennett29.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Roberts/4Oct2007Roberts113.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reid/12May2005Reid12.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Alexander/29Jun2007Alexander127.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Mikulski/5May2006Mikulski330.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reid/18May2005Reid0.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Wyden/19Jun2007Wyden61.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Durbin/25Jul2007Durbin2.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Coleman/21Sep2004Coleman91.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cantwell/9Jul2007Cantwell149.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Murray/6Dec2007Murray2.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Harkin/6Apr2006Harkin875.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Burns/7Jul2005Burns426.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Boxer/13Jul2005Boxer431.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Hatch/9Apr2008Hatch17.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dole/17Dec2007Dole29.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kerry/16Apr2007Kerry375.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Obama/23Sep2005Obama651.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Hutchison/30Sep2005Hutchison217.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Thomas/2Feb2005Thomas282.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kohl/15Dec2006Kohl1.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Salazar/2Aug2007Salazar269.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Schumer/13Jul2006Schumer427.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dayton/11Aug2005Dayton280.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kennedy/13Apr2007Kennedy9.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/9Mar2005Clinton653.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lautenberg/09Feb2005Lautenberg539.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Craig/8May2007Craig159.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lautenberg/17Oct2007Lautenberg72.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dorgan/24Aug2006Dorgan435.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Schumer/22Aug2007Schumer300.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Collins/16Jul2007Collins217.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Salazar/10Mar2006Salazar206.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Collins/24Sep2007Collins284.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Johnson/14May2007Johnson39.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Boxer/7Sep2007Boxer54.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Collins/27Nov2006Collins444.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cornyn/14Feb2008Cornyn69.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Durbin/14Jan2008Durbin6.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Collins/16Mar2005Collins126.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Jeffords/22Nov2005Jeffords118.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Salazar/9Jan2008Salazar106.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Bayh/23Jan2006Bayh2.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Collins/14Jun2005Collins283.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Murray/10Mar2005Murray13.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/28Jun2005Lieberman203.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lautenberg/21Mar2007Lautenberg255.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reed/27Apr2006Reed15.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Murray/9May2007Murray12.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Thune/28Jun2006Thune5.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Stabenow/6Dec2006Stabenow194.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Hatch/10May2006Hatch391.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lugar/6Jan2006Lugar72.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lugar/9Aug2006Lugar428.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kennedy/27Jun2007Kennedy4.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Leahy/15Nov2007Leahy61.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Pryor/25May2005Pryor503.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Thomas/9Mar2006Thomas152.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Salazar/7Feb2008Salazar69.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Rockefeller/18Jan2006Rockefeller237.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Coleman/26Jun2007Coleman234.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Graham/23Aug2005Graham168.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Landrieu/08Mar2007Landrieu252.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dorgan/9Jan2008Dorgan59.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Grassley/25May2006Grassley44.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Stabenow/1May2007Stabenow153.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/30Mar2006Lieberman308.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Carper/09Nov2005Carper9.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dewine/29Jul2004Dewine456.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Frist/13Sep2004Frist329.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Klobuchar/23Jul2007Klobuchar277.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dole/17Jan2007Dole186.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/27Mar2007Domenici47.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Levin/9Jul2007Levin108.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Hagel/4May2007Hagel48.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Ensign/24Oct2005Ensign349.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Pryor/24Aug2007Pryor113.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Conrad/14May2007Conrad2.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kennedy/20Sep2007Kennedy8.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kennedy/23Jan2007Kennedy2.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Hutchison/19Jul2005Hutchison270.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Crapo/28Jul2005Crapo509.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Brownback/9Feb2007Brownback129.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Conrad/18Jun2007Conrad8.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Chambliss/18Oct2006Chambliss221.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dodd/26Feb2007Dodd495.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Coleman/6Jul2004Coleman132.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Murray/18Jan2007Murray2.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Obama/13Jun2008Obama61.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Biden/14Mar2006Biden17.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dayton/2Aug2004Dayton489.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dodd/28Mar2007Dodd454.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Wyden/26Jul2005Wyden198.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reid/30Nov2005Reid313.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Levin/19Jul2005Levin503.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Chambliss/30Jan2008Chambliss37.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/15Oct2005Clinton183.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Santorum/24Sep2004Santorum228.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Feingold/24Sep2004Feingold9.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Burns/5May2004Burns132.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/23May2005Clinton500.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Grassley/14Apr2006Grassley77.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Landrieu/04Mar2005Landrieu155.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Corzine/29Jun2004Corzine200.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Graham/14Mar2006Graham36.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Murray/8Dec2006Murray5.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Sununu/18Oct2007Sununu53.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kyl/28Jun2005Kyl371.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/12Sep2007Clinton375.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Sununu/13Nov2007Sununu30.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/23Sep2004Domenici104.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Stabenow/1Oct2004Stabenow471.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Leahy/07Jun2006Leahy164.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Inouye/08Jun2007Inouye53.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lugar/14Oct2004Lugar308.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Collins/07Apr2006Collins151.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Leahy/05Aug2005Leahy160.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Frist/7Apr2006Frist271.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/18Feb2005Lieberman376.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Akaka/28Jun2005akaka63.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Schumer/04Oct2006Schumer163.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dewine/24May2005Dewine86.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Gregg/8May2007Gregg37.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Mikulski/17May2004Mikulski293.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Voinovich/18Jan2006Voinovich236.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kerry/28Jun2007Kerry277.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Bunning/17Jul2007Bunning66.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dodd/21Sep2006Dodd98.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/1Jun2006Clinton475.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Graham/23Jun2005Graham103.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lincoln/06Dec2006Lincoln6.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Pryor/02Oct2006Pryor300.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Jeffords/15Feb2005Jeffords211.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Leahy/09Feb2006Leahy318.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Obama/23Jun2006Obama525.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Harkin/19Mar2007Harkin419.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kerry/28Jul2006Kerry69.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Roberts/31Oct2007Roberts96.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Burr/27Jan2006Burr215.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lincoln/28Feb2007Lincoln17.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Mikulski/8Dec2006Mikulski139.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Hatch/25Oct2007Hatch107.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Bunning/24Feb2005Bunning73.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Alexander/16Oct2007Alexander54.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reid/23May2007Reid494.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dewine/19Dec2005Dewine311.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/McCain/02Apr2005McCain29.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Craig/7Dec2007Craig65.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Mikulski/9Nov2007Mikulski171.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/28Sep2007Clinton295.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Leahy/06Dec2006Leahy11.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Corker/08Oct2007Corker54.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Sarbanes/15Feb2005Sarbanes94.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dodd/16Nov2005Dodd238.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Snowe/17Dec2007Snowe199.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Corzine/14Feb2005Corzine111.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/McConnell/28Jan2008McConnell68.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Schumer/07Nov2007Schumer82.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/21Jul2004Domenici201.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Voinovich/22Jun2007Voinovich94.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kennedy/11Jan2007Kennedy10.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Collins/09May2007Collins146.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Chafee/11Jan2005Chafee114.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Salazar/10Apr2008Salazar22.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Biden/04Dec2006Biden12.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kerry/03Oct2005Kerry67.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Snowe/8Jun2006Snowe287.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Pryor/07Feb2007Pryor254.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cantwell/9Feb2006Cantwell43.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cantwell/6Apr2006Cantwell489.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Landrieu/29Jun2005Landrieu97.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Coburn/21Mar2007Coburn21.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Harkin/2Mar2007Harkin451.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dorgan/15Mar2005Dorgan310.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Allard/16Nov2005Allard13.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Casey/23Mar2007Casey3.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Snowe/14Sep2004Snowe428.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/16Apr2007Clinton909.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/27Apr2005Clinton566.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Mikulski/13Nov2006Mikulski164.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Specter/25Jul2005Specter46.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Frist/22Jul2004Frist362.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Tester/17Apr2008Tester6.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kennedy/25Sep2007Kennedy13.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/17Mar2005Domenici375.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Harkin/15Mar2005Harkin1589.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Salazar/21Apr2006Salazar173.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Biden/09Jan2006Biden18.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Talent/17Mar2005Talent400.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lugar/31Jan2006Lugar54.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Biden/17Aug2006Biden6.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Snowe/20Nov2006Snowe94.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Johnson/20Sep2004Johnson277.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Corzine/4Mar2005Corzine97.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Ensign/25Jul2005Ensign391.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/McConnell/12Mar2007McConnell375.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Salazar/31Mar2006Salazar187.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Bingaman/11Jul2005Bingaman211.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Wyden/24Apr2008Wyden1.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lautenberg/13Nov2007Lautenberg48.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kerry/08Nov2007Kerry64.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Burr/8Dec2005Burr235.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Martinez/10Dec2007Martinez17.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Rockefeller/31Aug2005Rockefeller266.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Craig/6Mar2008Craig30.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Santorum/24Jan2006Santorum293.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/25May2007Clinton758.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Durbin/12Apr2007Durbin11.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Murray/2Aug2005Murray7.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Barrasso/10Sep2007Barrasso19.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Hatch/29Jun2005Hatch21.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Salazar/30Sep2005Salazar310.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Mikulski/7Sep2004Mikulski242.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dewine/29Sep2005Dewine395.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/4Oct2007Clinton278.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Schumer/19Apr2006Schumer518.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Feingold/22Jun2007Feingold199.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Frist/17Dec2004Frist198.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Santorum/23Nov2004Santorum144.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Harkin/17Jun2005Harkin1386.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cornyn/29Nov2004Cornyn192.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kyl/10May2005Kyl383.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Talent/18Jun2004Talent163.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Boxer/8Aug2007Boxer59.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Shelby/14Jun2007Shelby105.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Shelby/15Jun2005Shelby169.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Sarbanes/15Mar2005Sarbanes90.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Conrad/3Aug2007Conrad10.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Feinstein/24Jul2007Feinstein205.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Mikulski/8Dec2004Mikulski173.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Murray/14May2006Murray11.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Grassley/20Mar2007Grassley381.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Isakson/14Jun2007Isakson125.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reed/20Dec2007Reed3.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Sanders/27Feb2007Sanders256.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Casey/22Oct2007Casey3.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/20Jul2005Clinton370.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Crapo/25May2006Crapo325.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kennedy/17Jun2005Kennedy2.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Johnson/20Feb2008Johnson140.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Mikulski/13May2005Mikulski128.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Isakson/8May2006Isakson296.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kennedy/23Jan2007Kennedy11.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lugar/28Jan2008Lugar51.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Salazar/31Oct2007Salazar200.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Biden/24Oct2007Biden12.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kerry/16Jul2007Kerry248.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Leahy/19Sep2006Leahy64.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Vitter/28Jun2006Vitter277.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kerry/21Nov2005Kerry14.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Corzine/16Nov2004Corzine152.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Mikulski/29Jun2007Mikulski351.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Chambliss/8Dec2006Chambliss211.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/5Feb2007Clinton1092.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Harkin/24Aug2007Harkin184.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Alexander/12Sep2007Alexander95.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kennedy/22Sep2007Kennedy6.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Stabenow/28Mar2006Stabenow261.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Wyden/8Sep2006Wyden129.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lugar/6Mar2008Lugar25.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Ensign/31Oct2005Ensign339.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cornyn/3Oct2007Cornyn215.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Leahy/29Mar2006Leahy251.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Corzine/28Mar2005Corzine81.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Harkin/15Nov2005Harkin1046.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cornyn/5Jun2004Cornyn275.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Stevens/2Nov2005Stevens389.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/3Oct2005Lieberman98.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Brownback/24Oct2007Brownback30.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reid/27Apr2006Reid114.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dayton/9Sep2005Dayton276.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dodd/28Jan2008Dodd115.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Johnson/1Sep2006Johnson231.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Schumer/07Nov2007Schumer84.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Murray/25Mar2005Murray5.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Allen/14Jun2006Allen101.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Ensign/18Jul2006Ensign220.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Hatch/8Sep2006Hatch338.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kyl/19Jan2005Kyl419.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Obama/10Jul2008Obama29.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Hutchison/14Feb2008Hutchison31.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Snowe/13Sep2006Snowe152.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Bingaman/8Aug2005Bingaman201.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Ensign/1Feb2007Ensign133.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cornyn/24Jan2007Cornyn10.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lautenberg/27Jul2007Lautenberg133.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Corzine/16Dec2005Corzine2.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Conrad/15Apr2005Conrad10.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Stevens/26Apr2006Stevens341.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Coleman/25Jul2006Coleman90.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Pryor/02Oct2007Pryor66.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kyl/17May2006Kyl215.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Smith/4Sep2007Smith16.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Barrasso/28Nov2007Barrasso50.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Biden/07Jun2005Biden14.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Snowe/13Jun2005Snowe452.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Alexander/25May2007Alexander156.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Collins/12Nov2005Collins501.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Pryor/08Mar2007Pryor234.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Collins/22Jun2005Collins301.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dodd/4Oct2006Dodd81.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Bingaman/2Nov2007Bingaman123.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/6Aug2007Clinton476.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Sessions/08Mar2006Sessions152.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Leahy/08Sep2005Leahy122.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cardin/01Jun2007Cardin199.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Frist/1Nov2005Frist446.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dayton/6Feb2006Dayton202.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Alexander/7Jun2007Alexander148.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Schumer/01Mar2005Schumer683.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Rockefeller/30Aug2004Rockefeller352.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dole/22Nov2004Dole469.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kennedy/14Feb2006Kennedy6.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Brownback/12Dec2006Brownback2.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kohl/15Mar2006Kohl6.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Hatch/16Jun2004Hatch207.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dayton/31Jan2006Dayton205.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Conrad/6Oct2005Conrad24.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Salazar/3May2005Salazar349.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Talent/20Nov2004Talent469.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dole/27Sep2004Dole478.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Feinstein/15Jul2007Feinstein215.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Coleman/14Sep2007Coleman152.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Webb/14Sep2007Webb56.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/24Feb2005Lieberman370.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Snowe/29Oct2007Snowe247.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/McCain/22Sep2006McCain85.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Allard/28Feb2007Allard0.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Webb/24Jul2007Webb78.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Mikulski/13May2004Mikulski295.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Pryor/08Jul2005Pryor480.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Hutchison/20Jan2005Hutchison356.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Murray/21Jan2005Murray11.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Coleman/1Aug2007Coleman190.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Collins/27Nov2006Collins445.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Casey/09May2007Casey11.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Landrieu/20Mar2007Landrieu238.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Hatch/25Jul2005Hatch6.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cornyn/30May2007Cornyn378.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Bingaman/2Mar2007Bingaman398.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Thomas/26Aug2004Thomas314.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dorgan/25Sep2007Dorgan138.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Shelby/30Nov2006Shelby367.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Mikulski/13Mar2007Mikulski19.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cantwell/11Apr2006Cantwell486.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Biden/14Aug2005Biden3.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Klobuchar/05May2008Klobuchar65.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lautenberg/25Oct2005Lautenberg463.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kennedy/16May2007Kennedy5.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kennedy/16Mar2006Kennedy2.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Talent/21Apr2006Talent148.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Schumer/08Jun2005Schumer481.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Schumer/12Feb2007Schumer663.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/20Jul2006Clinton334.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Roberts/18May2006Roberts366.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/BenNelson/26Jun2006BenNelson68.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reid/12Sep2006Reid329.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Boxer/21Feb2006Boxer285.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/14Dec2007Clinton91.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Shelby/26Jul2005Shelby227.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Grassley/5Mar2008Grassley60.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Pryor/28Apr2005Pryor520.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Johnson/10Aug2005Johnson93.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Roberts/15Nov2005Roberts417.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dole/10Apr2008Dole4.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lautenberg/31Jul2007Lautenberg132.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Schumer/26May2005Schumer499.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/8Jul2005Lieberman195.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Murkowski/24Jun2004Murkowski378.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Leahy/29Mar2007Leahy384.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Johnson/25Jan2006Johnson462.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/3Jul2007Clinton629.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Specter/30Jan2007Specter196.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reid/9May2007Reid25.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kyl/15Sep2004Kyl455.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Coleman/9Nov2006Coleman468.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Talent/20Nov2004Talent477.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Corzine/22Jun2005Corzine52.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Schumer/19Sep2007Schumer207.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Harkin/29Jun2005Harkin1360.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cardin/26Sep2007Cardin96.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Landrieu/20Oct2006Landrieu42.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dorgan/25Jan2008Dorgan49.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/BenNelson/07Nov2007BenNelson26.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Inouye/19Sep2006Inouye15.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Shelby/09Nov2007Shelby239.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Feinstein/19Jun2006Feinstein85.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/6Nov2007Domenici159.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/29Jun2007Clinton633.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kennedy/19Jun2007Kennedy8.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Schumer/16Jul2006Schumer423.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kennedy/10Feb2006Kennedy12.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kerry/21Sep2007Kerry144.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Feinstein/3Jun2004Feinstein156.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Harkin/27Sep2005Harkin1148.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reed/29Jun2006Reed8.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Rockefeller/7Jun2005Rockefeller286.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Snowe/24Sep2004Snowe387.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Pryor/10Jan2006Pryor393.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cantwell/17Jul2006Cantwell393.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Biden/19Dec2007Biden7.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Sanders/1Oct2007Sanders116.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Alexander/12Jan2005Alexander321.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Allen/11Aug2004Allen434.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Warner/5Jan2005Warner82.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dorgan/16Jun2005Dorgan260.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Craig/11Aug2005Craig461.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/21Dec2005Clinton14.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Shelby/17Dec2007Shelby307.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Stabenow/2Feb2006Stabenow285.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kennedy/17May2006Kennedy6.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Roberts/1Feb2005Roberts469.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Collins/11Jun2007Collins187.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Obama/16May2007Obama405.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Feingold/24Aug2007Feingold152.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dewine/15Jun2004Dewine41.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Santorum/18May2004Santorum390.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Frist/30Sep2006Frist50.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reid/30May2006Reid53.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Shelby/28Jun2007Shelby134.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Allard/1Aug2006Allard12.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Voinovich/15Dec2005Voinovich248.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Bennett/03Oct2007Bennett22.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Allard/25Jul2007Allard10.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Klobuchar/24May2007Klobuchar309.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/BenNelson/20Jun2006BenNelson74.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Graham/30Mar2005Graham45.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lugar/1May2006Lugar7.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Murray/7Feb2005Murray5.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Smith/2Feb2006Smith6.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lautenberg/25Sep2007Lautenberg95.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Talent/29Mar2006Talent170.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/15May2007Clinton810.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Harkin/14Jan2005Harkin1719.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Coleman/31Jan2008Coleman43.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Alexander/29Jul2005Alexander167.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/15Sep2005Lieberman118.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Burns/23Nov2004Burns41.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Menendez/19Jul2007Menendez236.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Jeffords/10Nov2005Jeffords126.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Collins/05Oct2007Collins306.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Collins/12Jun2006Collins257.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Johnson/13Sep2007Johnson395.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Mikulski/3Oct2006Mikulski181.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Ensign/29Sep2005Ensign355.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Conrad/21Feb2007Conrad2.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Casey/05Dec2007Casey14.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Allard/16Oct2007Allard11.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dodd/7Sep2005Dodd271.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/McCaskill/13Dec2007McCaskill8.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Frist/4May2005Frist90.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Frist/8Nov2006Frist28.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Mikulski/28Jun2007Mikulski359.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Murray/26Jan2005Murray2.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Vitter/9Sep2005Vitter385.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Talent/1Mar2005Talent412.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dole/7Nov2007Dole54.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Snowe/8Mar2006Snowe499.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Isakson/13Sep2007Isakson89.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Hatch/21Dec2005Hatch445.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Harkin/1Feb2005Harkin1690.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/BenNelson/18Apr2007BenNelson119.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Bingaman/9Jun2006Bingaman63.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/BenNelson/25May2007BenNelson100.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Santorum/6Dec2005Santorum345.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/13Feb2007Domenici120.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kennedy/23May2005Kennedy1.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lincoln/16Mar2006Lincoln6.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dole/22Nov2004Dole472.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Chambliss/14Dec2007Chambliss50.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reid/2May2005Reid21.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Whitehouse/16Nov2007Whitehouse50.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reid/1Feb2008Reid109.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Santorum/12Nov2005Santorum374.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Durbin/30Jan2007Durbin11.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Grassley/13Dec2007Grassley111.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Stabenow/6Feb2006Stabenow283.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Murray/2Aug2005Murray0.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Harkin/16Sep2005Harkin1185.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Tester/23Oct2007Tester147.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Klobuchar/27May2008Klobuchar42.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Leahy/05Feb2007Leahy454.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cantwell/3Apr2008Cantwell17.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kerry/11Mar2005Kerry235.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kerry/17Mar2007Kerry411.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Jeffords/12May2004Jeffords340.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Casey/19Dec2007Casey4.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dodd/9Jun2004Dodd426.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Isakson/10Jan2007Isakson202.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Graham/29Nov2006Graham182.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Murray/17Mar2007Murray6.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Menendez/14Nov2006Menendez428.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Mikulski/20Jul2006Mikulski250.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Chambliss/13Feb2008Chambliss30.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Talent/11May2006Talent129.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Graham/01Aug2007Graham114.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Collins/27Jun2005Collins305.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cornyn/8Jul2004Cornyn259.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Barrasso/11Dec2007Barrasso56.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/23Nov2004Domenici38.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kyl/17Jul2006Kyl184.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Craig/15Jun2007Craig137.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Bayh/06Apr2006Bayh4.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Voinovich/19Oct2007Voinovich56.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Levin/7Mar2005Levin576.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lautenberg/23Mar2005Lautenberg526.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Hutchison/12Apr2007Hutchison379.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Levin/13Apr2005Levin557.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/11May2006Clinton516.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Corker/12Dec2007Corker11.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Snowe/14Dec2007Snowe206.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lugar/13Mar2007Lugar298.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Isakson/18Aug2005Isakson441.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/24May2004Domenici264.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Hutchison/23Jun2005Hutchison278.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/McConnell/21Sep2006McConnell458.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/8Jun2005Domenici309.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Isakson/8Nov2007Isakson64.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/19Dec2005Clinton17.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Tester/12Sep2007Tester197.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kerry/04Apr2007Kerry388.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Schumer/22Sep2005Schumer242.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Menendez/15Feb2007Menendez388.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Johnson/28Sep2005Johnson54.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dewine/8Jul2004Dewine3.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Talent/2Oct2006Talent14.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Coleman/15Jan2008Coleman55.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Salazar/10Aug2006Salazar64.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/25Jan2005Clinton714.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lott/23Jun2004Lott365.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Obama/7Mar2007Obama457.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Burns/28Jan2006Burns319.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Johnson/20Sep2004Johnson276.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Feingold/13Nov2006Feingold314.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Bayh/03Aug2004Bayh17.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/12Apr2007Clinton921.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Bayh/25Jul2005Bayh13.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Frist/7Jun2005Frist73.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lautenberg/17Mar2005Lautenberg528.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kerry/09May2007Kerry348.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Schumer/13Sep2007Schumer220.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Grassley/2Aug2007Grassley228.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/21Sep2004Domenici116.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/9May2006Lieberman253.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Burns/19Jul2006Burns65.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/3May2004Domenici295.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Coleman/2Jun2006Coleman158.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Sessions/30Aug2006Sessions111.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Leahy/22Mar2007Leahy395.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/29Apr2005Domenici336.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dodd/16Oct2007Dodd237.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Levin/29Jul2005Levin489.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lott/11Mar2005Lott306.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Isakson/20May2006Isakson287.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Johnson/18Jun2007Johnson2.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/1Dec2007Clinton145.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Warner/3Jun2004Warner132.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Harkin/20Sep2006Harkin591.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/McCain/17May2005McCain45.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Schumer/26Jun2005Schumer444.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Hagel/5Mar2007Hagel68.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/13May2005Lieberman259.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Mikulski/26Feb2007Mikulski41.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cantwell/28Sep2006Cantwell327.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/McConnell/8Nov2007McConnell140.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Tester/12Sep2007Tester200.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kerry/08Mar2007Kerry424.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Obama/31Aug2005Obama670.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Grassley/24Jul2007Grassley247.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kyl/17May2004Kyl496.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Biden/16Jul2006Biden13.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Boxer/3May2007Boxer106.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Leahy/06Nov2007Leahy81.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Salazar/16May2007Salazar356.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Johnson/20Feb2008Johnson113.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Martinez/18Jan2006Martinez182.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/22Jun2006Clinton431.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Salazar/16Nov2007Salazar172.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Hutchison/25Jan2007Hutchison452.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/1Mar2006Lieberman356.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lautenberg/25May2006Lautenberg378.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Durbin/14Nov2006Durbin7.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reid/28Jun2006Reid463.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dodd/29May2007Dodd383.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Ensign/20Jul2007Ensign48.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Hatch/28Aug2007Hatch154.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/McCain/14Dec2007McCain104.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dewine/17Aug2005Dewine37.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Roberts/10Nov2004Roberts482.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Bingaman/8Feb2005Bingaman288.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Coleman/26Jul2006Coleman87.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Thune/14Nov2007Thune4.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Smith/13Apr2007Smith5.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Collins/06Oct2005Collins454.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reid/26Apr2006Reid117.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Akaka/28Apr2006akaka92.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Coburn/17Mar2005Coburn6.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Burns/7Feb2005Burns6.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Schumer/17Aug2005Schumer350.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/3Mar2006Clinton621.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Voinovich/24Jan2005Voinovich288.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kerry/29Aug2006Kerry55.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Thune/9Mar2005Thune18.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Grassley/2Nov2007Grassley154.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Conrad/7Jun2006Conrad19.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cantwell/2Mar2007Cantwell235.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Snowe/5Nov2007Snowe241.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Collins/11Sep2007Collins257.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Grassley/14Aug2006Grassley478.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Sununu/12Apr2007Sununu158.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Talent/8Apr2006Talent158.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Specter/05Oct2006Specter32.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Murray/11Dec2006Murray2.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Leahy/12Jan2006Leahy358.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Feinstein/6Oct2004Feinstein77.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lincoln/22Mar2007Lincoln14.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/BenNelson/22Jan2007BenNelson162.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Coleman/18Apr2007Coleman305.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/21Sep2006Lieberman75.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lugar/5Apr2005Lugar222.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lincoln/29Jun2005Lincoln11.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/14Aug2007Clinton458.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Leahy/17Feb2007Leahy432.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/3Jul2006Domenici361.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Coburn/28Jun2005Coburn24.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Thomas/22Feb2006Thomas162.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/6Jan2005Lieberman439.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kennedy/11Jul2007Kennedy5.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kennedy/15Dec2005Kennedy5.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lincoln/23Mar2007Lincoln11.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Gregg/20Jul2006Gregg95.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cantwell/19Dec2006Cantwell285.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Graham/24Jan2007Graham11.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Casey/09Nov2007Casey10.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Akaka/28Sep2006akaka19.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reid/24May2007Reid489.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/22Nov2005Clinton75.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Voinovich/16Nov2005Voinovich253.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Coleman/13Mar2007Coleman335.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dewine/4Nov2004Dewine289.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dodd/4Mar2008Dodd70.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Graham/14Sep2005Graham184.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Shelby/06Sep2007Shelby175.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/6May2004Domenici289.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/25Aug2004Domenici145.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Hatch/21Sep2004Hatch146.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Sarbanes/16Jun2004Sarbanes132.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/28Jan2008Domenici103.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/28Apr2006Domenici456.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Landrieu/28Apr2005Landrieu132.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/19Jan2006Domenici112.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Shelby/18Dec2007Shelby327.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lautenberg/19Apr2007Lautenberg231.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Sununu/20Sep2005Sununu39.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/McCain/24Jan2007McCain16.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Durbin/10May2007Durbin0.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Ensign/22Jun2006Ensign232.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Shelby/05May2006Shelby119.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Rockefeller/9May2006Rockefeller197.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/26Jan2006Clinton688.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Ensign/25Oct2005Ensign343.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Harkin/19Dec2007Harkin3.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Bunning/25Jan2006Bunning53.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Allen/22Jun2004Allen478.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Stabenow/16Oct2007Stabenow80.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Allard/17Apr2007Allard15.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Leahy/19Sep2006Leahy65.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Snowe/22Sep2004Snowe393.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Barrasso/19Jul2007Barrasso9.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Mikulski/27Sep2007Mikulski231.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Boxer/24Oct2007Boxer40.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kerry/08Jun2006Kerry112.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dewine/5Jan2005Dewine204.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Johnson/21Aug2007Johnson424.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Coleman/29Mar2007Coleman315.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/30Jun2006Lieberman167.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Stevens/25Feb2005Stevens412.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/10May2007Domenici449.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Hatch/9Jun2006Hatch376.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Schumer/22Feb2007Schumer625.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dewine/28Sep2005Dewine406.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/23Jul2007Domenici328.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Bayh/14Dec2005Bayh15.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dayton/24Jun2004Dayton6.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kerry/19Jun2006Kerry107.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Snowe/18Nov2005Snowe195.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/27Jul2004Domenici187.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cardin/24Oct2007Cardin61.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Thune/9Mar2007Thune22.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dole/23Nov2004Dole464.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Feinstein/5Sep2006Feinstein18.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/7Sep2007Clinton385.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/7Jun2006Domenici393.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/18Jan2006Lieberman408.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Carper/03Aug2007Carper3.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Schumer/20Apr2005Schumer571.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Landrieu/04Apr2006Landrieu169.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Collins/09Mar2005Collins99.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Landrieu/03Nov2005Landrieu11.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kerry/13Apr2005Kerry206.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Isakson/5Jul2005Isakson465.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Snowe/20Feb2007Snowe484.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Burns/27Apr2005Burns464.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/12Jul2007Clinton608.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dole/15Jul2004Dole492.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Sanders/12Sep2007Sanders131.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Specter/30Sep2005Specter23.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kennedy/28Feb2007Kennedy5.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cornyn/21May2004Cornyn284.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Biden/17May2007Biden17.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Akaka/25Apr2007akaka159.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Obama/9Mar2006Obama573.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/27Apr2005Clinton569.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Corzine/15Mar2005Corzine88.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Webb/14Feb2007Webb169.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Hutchison/19Sep2007Hutchison180.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Leahy/03Feb2005Leahy352.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reid/11May2006Reid81.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Mikulski/30Sep2004Mikulski210.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/14Oct2005Lieberman84.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/1Jul2005Clinton412.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dodd/18Mar2008Dodd47.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Leahy/08Mar2006Leahy289.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dewine/24Aug2005Dewine489.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Mikulski/9Nov2005Mikulski492.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Salazar/18Dec2007Salazar124.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Salazar/28Feb2008Salazar53.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kennedy/09Mar2006Kennedy12.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dodd/28Apr2005Dodd309.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Bennett/23Jun2005Bennett217.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Brown/24Sep2007Brown8.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Bayh/28Sep2004Bayh9.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dole/25Oct2007Dole58.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reid/15Nov2005Reid328.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Feingold/7Apr2008Feingold7.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Allard/24Aug2006Allard3.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Rockefeller/6May2004Rockefeller370.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Snowe/1Apr2005Snowe74.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Martinez/10Sep2007Martinez70.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Allard/23Jan2007Allard7.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Jeffords/10Feb2006Jeffords90.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Coburn/30Mar2007Coburn24.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Burns/10Nov2005Burns361.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kerry/02Oct2007Kerry118.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Johnson/9Sep2005Johnson66.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Crapo/13Dec2007Crapo17.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Schumer/07Sep2007Schumer256.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Brownback/3Nov2005Brownback17.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Byrd/23Jan2007Byrd40.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Murkowski/16Jan2008Murkowski8.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/21Sep2005Lieberman108.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/BenNelson/14Apr2005BenNelson128.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Johnson/24Sep2007Johnson369.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Martinez/14Mar2006Martinez151.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dorgan/6Apr2005Dorgan304.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Boxer/4Dec2007Boxer28.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Vitter/28Apr2006Vitter310.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kennedy/09Dec2006Kennedy13.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Schumer/17Aug2005Schumer344.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Santorum/1Aug2006Santorum91.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Crapo/9Nov2005Crapo434.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reed/14Dec2006Reed0.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Hutchison/26Sep2007Hutchison161.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Frist/22Jun2005Frist61.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Bingaman/11Sep2007Bingaman204.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Akaka/4Aug2006akaka35.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Rockefeller/7Dec2007Rockefeller45.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dewine/8Nov2006Dewine30.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/26Jun2006Lieberman177.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Jeffords/16Nov2005Jeffords124.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Murray/14Nov2006Murray4.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Durbin/19Oct2007Durbin23.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Smith/3May2007Smith11.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/1Feb2005Lieberman415.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Snowe/23Jun2006Snowe247.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dole/8Feb2005Dole439.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Pryor/25Sep2007Pryor77.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Collins/05Oct2006Collins413.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Collins/07Sep2006Collins363.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Feinstein/17Oct2005Feinstein353.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Stevens/20Jul2007Stevens140.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Johnson/9Sep2004Johnson288.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Snowe/14Apr2005Snowe51.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/14Jun2006Lieberman204.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/18Jun2007Clinton695.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/16Jun2005Lieberman219.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Feingold/16Feb2006Feingold389.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Pryor/23Sep2005Pryor449.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Mikulski/20Jun2007Mikulski377.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Snowe/20Jul2006Snowe210.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Mikulski/24Aug2006Mikulski220.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Mikulski/7Sep2006Mikulski208.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reed/25Jul2007Reed15.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Snowe/15Jun2006Snowe266.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Jeffords/22Jun2004Jeffords316.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Roberts/17May2005Roberts443.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Menendez/22Oct2007Menendez101.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Demint/28Jan2008Demint23.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Talent/15Dec2004Talent453.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Akaka/3Mar2006akaka121.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Crapo/28Sep2006Crapo245.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Levin/15Dec2005Levin404.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Sununu/06Jun2007Sununu130.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lugar/20Sep2006Lugar403.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Santorum/28Jun2005Santorum475.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dewine/12Sep2006Dewine113.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Brownback/20Jul2006Brownback41.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Corker/24May2007Corker128.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Ensign/27Sep2005Ensign357.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Harkin/12Jul2005Harkin1342.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Frist/7Jul2006Frist149.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reid/31Jul2006Reid393.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Byrd/11Apr2005Byrd210.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Shelby/01Feb2007Shelby29.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Enzi/16Feb2005Enzi35.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Obama/17Nov2005Obama620.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Graham/25Apr2006Graham56.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Feinstein/21Jun2005Feinstein405.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/3Mar2005Clinton662.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Wyden/5Mar2008Wyden17.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Feinstein/17Sep2007Feinstein167.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Coleman/8Jun2007Coleman255.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Levin/8Jun2006Levin324.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/27Jul2007Domenici310.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Craig/12Apr2007Craig173.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dorgan/8Jun2007Dorgan232.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Craig/24Jan2006Craig380.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Crapo/23Jan2007Crapo209.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Grassley/23Jan2007Grassley441.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Burr/6Dec2006Burr115.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Snowe/5Jan2007Snowe56.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Snowe/9Sep2004Snowe438.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Thomas/16Sep2005Thomas236.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kerry/27Sep2007Kerry126.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/5Oct2007Clinton276.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Allard/4May2007Allard19.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Stabenow/21Jun2005Stabenow360.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Stevens/13Feb2007Stevens240.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/29Nov2006Clinton43.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/10May2006Domenici439.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lautenberg/10May2007Lautenberg212.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/16Feb2007Domenici109.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Bingaman/7Jul2006Bingaman55.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/20Mar2007Domenici62.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Frist/29Jun2006Frist159.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Santorum/3Nov2004Santorum178.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cardin/15Nov2007Cardin33.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lincoln/23Mar2007Lincoln10.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Grassley/27Sep2007Grassley191.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Obama/4Oct2007Obama258.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dewine/24Aug2005Dewine3.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/10May2007Clinton823.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Santorum/7Oct2005Santorum409.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/9May2006Lieberman254.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Stabenow/2Dec2004Stabenow433.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Smith/27Aug2007Smith0.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Boxer/27Sep2007Boxer44.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Martinez/14Sep2006Martinez49.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Stevens/13Jun2007Stevens168.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/25Oct2005Domenici191.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reid/9May2006Reid88.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Coleman/29Jul2005Coleman423.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Frist/9Mar2005Frist141.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Coburn/8Jun2007Coburn42.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Obama/10Jan2007Obama472.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Landrieu/02May2007Landrieu199.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dodd/14Feb2008Dodd86.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Jeffords/26Oct2005Jeffords132.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/McConnell/4Sep2007McConnell224.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Boxer/11Dec2007Boxer24.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Sessions/28Jun2007Sessions35.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/9Feb2006Lieberman372.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lugar/3Mar2008Lugar29.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Hagel/15Sep2004Hagel347.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Hatch/28Sep2004Hatch138.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/9Mar2005Lieberman347.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/21Apr2005Lieberman290.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Feinstein/20Jul2004Feinstein126.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Snowe/30Sep2004Snowe369.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Coleman/12Oct2004Coleman75.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reid/8May2006Reid92.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dodd/27Jun2007Dodd351.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lugar/13Dec2007Lugar71.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Collins/25Sep2006Collins396.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Schumer/09Mar2005Schumer665.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Schumer/15Sep2006Schumer234.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cornyn/6Feb2007Cornyn497.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Voinovich/11Sep2006Voinovich159.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Santorum/7Apr2006Santorum215.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Tester/7Nov2007Tester125.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Johnson/27Feb2008Johnson88.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Enzi/22Oct2007Enzi179.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Bennett/21Jul2005Bennett209.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Snowe/7Feb2005Snowe185.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Snowe/14Dec2007Snowe204.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cantwell/8Aug2007Cantwell135.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Martinez/03Jan2006Martinez185.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Clinton/20Jul2007Clinton548.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Hutchison/6Nov2007Hutchison114.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/25May2006Lieberman228.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Pryor/19Dec2007Pryor3.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/McConnell/4Feb2008McConnell62.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dewine/29Sep2005Dewine391.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Boxer/19Dec2007Boxer6.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dorgan/28Jul2005Dorgan226.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Crapo/17Aug2007Crapo99.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Murray/4Apr2006Murray2.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Schumer/10May2005Schumer522.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dorgan/5Dec2007Dorgan89.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Collins/31Jan2005Collins37.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dodd/12Dec2007Dodd162.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/BenNelson/20Oct2005BenNelson27.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/20Apr2006Domenici472.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reid/29May2007Reid483.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dodd/27May2005Dodd302.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Roberts/16Jun2004Roberts23.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lautenberg/13Jan2006Lautenberg436.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Collins/10Feb2006Collins62.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Smith/16Jun2005Smith7.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Alexander/16Jun2005Alexander198.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Stevens/8Dec2006Stevens282.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dodd/12Sep2007Dodd283.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Stevens/3Nov2005Stevens387.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dorgan/14Jul2006Dorgan458.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Mikulski/27Apr2007Mikulski452.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Santorum/14Mar2005Santorum60.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Collins/21Nov2007Collins376.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Biden/04May2007Biden15.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Johnson/6Mar2006Johnson406.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Harkin/31Aug2005Harkin1232.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Shelby/14Dec2007Shelby289.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Tester/7Nov2007Tester129.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Santorum/29Oct2004Santorum184.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/9Nov2007Lieberman31.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Collins/15Mar2006Collins111.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Leahy/15Mar2007Leahy404.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Durbin/12Sep2007Durbin1.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Feinstein/27Sep2006Feinstein494.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Sessions/25Apr2007Sessions61.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Johnson/24May2007Johnson23.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Mikulski/1Feb2008Mikulski85.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Schumer/27Dec2005Schumer4.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Byrd/25Oct2005Byrd161.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Mikulski/30Jun2006Mikulski275.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Alexander/29Mar2007Alexander201.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lieberman/30Aug2006Lieberman111.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Bingaman/15Mar2005Bingaman264.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Graham/13Oct2006Graham170.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Allard/27Jun2007Allard5.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Frist/28Nov2005Frist425.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/21Aug2007Domenici256.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Kerry/20Nov2007Kerry46.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Levin/26Sep2007Levin64.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lugar/2Feb2006Lugar49.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cantwell/1Nov2006Cantwell303.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Enzi/06Dec2007Enzi202.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cantwell/1Mar2006Cantwell26.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Gregg/26Dec2007Gregg248.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Grassley/13Jun2007Grassley294.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cantwell/25May2006Cantwell441.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Warner/18Oct2007Warner26.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Feinstein/22Apr2007Feinstein314.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Enzi/06Sep2007Enzi150.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Allard/28Sep2006Allard13.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Murray/17Dec2007Murray9.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Chafee/1Mar2005Chafee103.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Collins/15Dec2005Collins531.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reid/20Apr2007Reid47.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Lugar/22Mar2007Lugar289.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Roberts/27Jun2007Roberts183.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Dewine/24Sep2004Dewine330.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Whitehouse/19Jun2007Whitehouse96.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Stabenow/19Sep2006Stabenow203.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Schumer/24Oct2005Schumer154.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Schumer/27Nov2006Schumer81.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Mikulski/13Aug2004Mikulski252.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reid/15Jun2007Reid449.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Jeffords/7Dec2006Jeffords0.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Domenici/18Nov2005Domenici159.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Reid/16Jul2007Reid395.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Coleman/6Feb2008Coleman34.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Craig/14Jul2006Craig280.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Harkin/14Jan2005Harkin1713.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Cornyn/4Dec2006Cornyn36.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Collins/26Apr2005Collins189.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Allen/15Sep2006Allen27.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Mikulski/25May2006Mikulski309.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Burr/2Mar2005Burr308.txt'

    ## Warning in readLines(x, encoding = "ISO-8859"): incomplete final line found
    ## on './dta/senate-releases/Levin/28Jul2005Levin494.txt'

    head(alltexts[[1]])

    ## [1] "         FOR IMMEDIATE RELEASE     Statement Included  FOR IMMEDIATE RELEASE        CONTACT  Laura Capps  Melissa Wagoner   202  224 2633    Washington  D C Today  Senator Edward M  Kennedy and Senator Mike Enzi held a hearing of the Senate Help committee with relief organizations working to provide support to Hurricane Katrina victims  Outside witnesses  including the Dr  Diane Roussel  the Superintendent of Jefferson Parish Public Schools in New Orleans  participated to help formulate a bipartisan plan on the immediate and long term needs resulting from Hurricane Katrina and the following relief efforts  Other witnesses  such as Dr  Jennifer Leaning of Harvard School of Public Health  shared their experiences dealing with the aftermath of other disasters     We have witnessed a natural disaster turned into a national catastrophe by a botched and inadequate response  despite the bravery and sacrifice of relief workers  rescue personnel  and the hurricane survivors themselves  Senator Kennedy said  Our promise to those who have survived the hurricane should not simply be to turn back the clock a month or two it should be to fulfill the true promise of the American Dream by committing ourselves to better health  better education and better job opportunities for them  and for all Americans   On Tuesday  as the first step  dozens of representatives from nationwide relief organizations met with Kennedy and Enzi and relayed their experiences and recommendations for meeting the challenge of providing support services to a region severely hit with health care  education  economic and structural devastation  Senator Kennedy has already put forth proposals to ensure that students don t miss a year of school  survivors receive much needed health care  and families receive economic assistance during this time of great tragedy  Some of these ideas became a part of Democratic Caucus agenda and others will become part of the HELP Committee s plan  Below is Senator Kennedy s remarks and the witness list from today s hearings     STATEMENT OF SENATOR EDWARD M  KENNEDY HEARING ON HURRICANE KATRINA SEPTEMBER 8  2005   Thank you  Senator Enzi  for holding today s important hearing on assisting those affected by Hurricane Katrina  and thank you also to our distinguished witnesses for their expertise and thoughtful recommendations    Some images are so searing they are burned into our memories forever  None of us will ever forget the pictures we have seen from the Gulf Coast in recent days    We have seen images of despair among those who were abandoned in a nation of great wealth and of hope reborn in the faces of families reunited after surviving a calamity of Biblical proportions  We have seen great heroism too  not only in the spectacular images of rescues by helicopter  but in the quiet courage of neighbors who helped neighbors survive the howling winds and rising waters     We have seen darker images too   images we thought we d never see in America  The elderly  the disabled  and the sick left to die  Families split apart  And American citizens trapped without food  sanitation or adequate water in makeshift shelters     In short  we have witnessed a natural disaster turned into a national catastrophe by a botched and inadequate response  despite the bravery and sacrifice of relief workers  rescue personnel  and the hurricane survivors themselves     Most of all  these indelible images remind us that we are all part of the American family  When members of that family are in need  in want  and in fear  we all have a duty to make our family whole once more    All of us  I am sure  have been heartened by the thousands of volunteers who have honored their commitment to the American family by giving their time and their skills to healing the injured  repairing schools  counseling the grieving  and aiding the survivors in finding new hope    In my own state of Massachusetts  health professionals  educators  labor unions  businesses  and countless individual citizens have answered the call  Eighteen major hospitals are sending volunteer teams or supplies to the affected area  More than 30 colleges and universities in Massachusetts will enroll students impacted by hurricane Katrina  offering housing and tuition assistance     Congress has a major responsibility to help the survivors of this terrible ordeal rebuild their communities and their lives  Today s hearing is an important part of meeting that responsibility  The distinguished individuals seated around this table today  and the organizations they represent  have rolled up their sleeves to help those most in need along the Gulf Coast  They have the vision to see what must be done  and the experience to know how to get it done    As we speak  thousands of Americans displaced from their homes are at risk of epidemics   yet only three working hospitals remain in Southeast Louisiana  Thousands more face the silent battles of coping with bereavement and catastrophe  We must restore shattered hospitals  assure access to health care including mental health care  and build communities so that hurricane survivors can live with dignity and hope in homes of their own    135 000 students in Louisiana alone have been displaced from their schools  Hundreds of schools in Mississippi have been damaged or destroyed  Student s lives have been disrupted and their semester interrupted  Fortunately  superintendents and principals across the country have reached out to students displaced by this disaster to welcome them into their classrooms  It s our turn in Congress to reach out and provide the resources needed for schools to take these students in  while also helping to rebuild educational institutions devastated by Katrina    We cannot afford to neglect the impact of this disaster on our nation s youngest children  Many of the thousands of children and families in the Gulf region most seriously impacted by this storm were already among the most neglected and vulnerable in our nation  devastated by the impact of poverty  It s time for Congress to act to help those young children and their families cope with the effects of trauma  and build a stronger foundation for their future     Up to one million Americans will be left jobless in this tragic storm  The unemployment rate in the Gulf Cost region is expected to increase to 25 percent or higher  Experts estimate that many of the affected workers will be unemployed for nine months or more     These are staggering figures  And they have national implications  Standard  Poors says that the likelihood of another recession has doubled and is now more than 25 percent    These families have lost absolutely everything  and they need a source of income while they try to get back on their feet and begin looking for new employment  This process will undoubtedly take time  Many of these people have more basic needs   such as finding shelter or finding lost family members   that must be met before they can focus on finding work     In addition  while communities across the country have generously opened their doors and their hearts to Katrina victims  the local economies in these areas do not necessarily have the capacity to accommodate the influx of workers that have arrived    Families  needs are immediate and significant  Employers and state governments in hurricane ravaged states cannot bear the burden of compensating the huge numbers of workers that are now jobless through their state unemployment compensation systems  We need a comprehensive federal response that makes disaster unemployment assistance available to every worker left jobless by this tragedy  and this federal assistance must provide a meaningful benefit that will meet the basic needs of unemployed workers and their families as they begin their long road to recovery    We must also take steps to see that we are better prepared for future calamities  whether from floods  earthquakes or terrorist attacks  In the days and weeks to come  we will have much to learn that will be helpful in this task    But an essential part of building for the future is a clear eyed assessment of the mistakes made in the response to Hurricane Katrina  If we fail to recognize and admit mistakes  they are sure to be repeated    But our task for today is to learn from our distinguished panelists how best to protect the health of those affected by the hurricane  and see that they can rebuild their lives     What should be our measure of success    Some would think it enough to return the survivors to the lives they knew before the flooding   but we should aim higher  For many of the survivors  the life they knew before the storm was one of ill health  inadequate education  and opportunity denied  The nation had failed them long before Katrina hit    Our promise to those who have survived the hurricane should not simply be to turn back the clock a month or two it should be to fulfill the true promise of the American Dream by committing ourselves to better health  better education and better job opportunities for them  and for all Americans    HURRICANE KATRINA ROUNDTABLE PARTICIPANTS   1       Michael Casserly  Great City Schools    2       Dr  Leonard Merrell  Superintendent of Katy Independent School District   3       Dr  Diane Roussel  Superintendent of Jefferson Parish School District   4       Alabama Education Department Task Force Dr  Eddie Johnson  Deputy Superintendent Feagin Johnson  Assistant Superintendent Craig Pouncy  Assistant Superintendent Maggie Rivers  Director Federal Programs Perry Taylor  School Architect Perry Fulton  Child Nutrition   5       Dr  Jennifer Leaning  Professor of the Practice of International Health   Harvard School of Public Health   6       Lisa Cox  Assistant Director for Federal Affairs  National Association of Community Health Centers    7       Charlie Ware  Chairman  Wyoming Workforce Development Council   8       Mark Shriver  Vice President and Managing Director  Save the Children   9        Kenneth  Weigand   Vice President for Human Resources  Walgreen s    10   Joseph E  Savoi  Louisiana Commissioner of Higher Education   11   Kathleen Smith  President  Education Finance Council   12    Major Marilyn White  National Consultant on Adult Ministries  Salvation Army   13   Maurice Emsellem  Public Policy Director  National Employment Law Project  Laura Capps  Melissa Wagoner  202  224 2633 "

Antes de poder trabajar con estos documentos, tenemos que procesarlos.
Los pasos son bastante habituales en practicamente todas los analisis de
minería de textos. Empezaremos por asegurarnos de que cada documento
está contenido en un vector

    alltexts <- lapply(alltexts, function(x) paste(x, collapse="\n"))
    substring(alltexts[[1]], 1, 100)

    ## [1] "         FOR IMMEDIATE RELEASE     Statement Included  FOR IMMEDIATE RELEASE        CONTACT  Laura C"

Aunque los textos están en inglés, es posible que algunos términos, por
ejemplo, nombres propios, contengan acentos. La mejor solución para
nosotros es transliterar los documentos a ASCII.

    alltexts <- lapply(alltexts, function(x) stri_trans_general(x, "latin-ascii"))

También convertiremos todas las palabras a minúscula:

    alltexts <- lapply(alltexts, tolower)

y eliminaremos puntuación, dígitos, espacios, y símbolos de dolar.

    alltexts <- lapply(alltexts, function(x) stri_replace_all_regex(x, "[[:punct:]]", " "))
    alltexts <- lapply(alltexts, function(x) stri_replace_all_regex(x, "[[:digit:]]", " "))
    alltexts <- lapply(alltexts, function(x) stri_replace_all_regex(x, "[[:space:]]", " "))
    alltexts <- lapply(alltexts, function(x) stri_replace_all_regex(x, "\\$", ""))
    substring(alltexts[[1]], 1, 100)

    ## [1] "         for immediate release     statement included  for immediate release        contact  laura c"

Para las operaciones de limpieza que nos quedan, es conveniente echar
mano del paquete `tm` que implementa versiones eficientes de funciones
como las que hemos usado pero para trabajar sobre documentos de texto.

    docs <- Corpus(VectorSource(alltexts))

Empezaremos por quitar palabras vacías. `tm` contiene una lista que
podemos usar para una primera pasada.

    docs <- tm_map(docs, removeWords, stopwords("english"))

En muchas ocasiones, y dependiendo del tema de los documentos, querremos
definir nuestra propia lista de palabras a eliminar. Esto lo podemos
usar cambiando el nargumento que pasamos a `removeWords` con un vector
de palabras:

    otras_stopwords <- c("can", "say", "one", "way", "use", "also", "howev", "tell",
                         "will", "much", "need", "take", "tend", "even", "like",
                         "particular", "rather", "said", "get", "well", "make",
                         "ask", "come", "end", "first", "two", "help", "often",
                         "may", "might", "see", "someth", "thing", "point", "post",
                         "look", "right", "now", "think", "‘ve ", "‘re ", "anoth",
                         "put", "set", "new", "good", "want", "sure", "kind",
                         "larg", "yes, ", "day", "etc", "quit", "sinc", "attempt",
                         "lack", "seen", "awar", "littl", "ever", "moreov",
                         "though", "found", "abl", "enough", "far", "earli", "away",
                         "achiev", "draw", "last", "never", "brief", "bit", "entir",
                         "brief", "great", "lot")
    docs <- tm_map(docs, removeWords, otras_stopwords)

Por último, eliminaremos los espacios en blanco sobrantes. Aunque es
sencillo escribir una expresión regular, podemos utilizar en su lugar
`tm` otra vez:

    docs <- tm_map(docs, stripWhitespace)
    docs[[21]]$content

    ## [1] "sens johnson thomas bill assists local law enforcement costs clean methamphetamine sites sens tim johnson d sd craig thomas r wy today introduced legislation states localities pay costs cleaning methamphetamine meth labs bipartisan legislation mandate portion forfeiture fund administered department treasury made available specifically methamphetamine lab clean currently money forfeiture fund used expenses resulting drug seizures forfeiture contract services compensation informers unfortunately rural communities become preferred place methamphetamine production trafficking communities afford enormous cost result clean meth lab bill communities clean meth sites johnson bill drafted consultation local law enforcement officials know hand difficult clean thank commitment repairing communities johnson introduced legislation th congress welcomed bipartisan support senator thomas small business owners landlords bear cost cleaning meth sites thomas s fair rural communities business owners shoulder costs alone using funds seizures forfeitures defray costs clean sites eliminate major financial obstacle communities cost taxpayer thomas wyoming s senior senator leader many issues affect rural communities johnson thomas legislation provide payment designated state local tribal law enforcement entity environment health entity cleaning areas formerly used methamphetamine lab event lab located private property payment fund exceed costs utilized property owners knowledge existence operation lab prior law enforcement action upon acquiring knowledge law enforcement agency notified within hours according estimates every pound meth produced pounds toxic wastes produced clean costs meth lab site range significant cost local law enforcement entities absorb member united states senate member senate appropriations committee johnson sought increase federal funding law enforcement drug treatment drug prevention initiatives including increased funding assist local law enforcement high intensity drug trafficking area hidta initiative contact cameron hardy "

Lo que nos interesa es cómo los términos se agrupan en documentos.
Variaciones de los términos a través de sufijos son en este caso un
problema. Por ejemplo, en textos referidos a educación, no queremos que
nuestro modelo intente capturar diferencias entre *educación*,
*educativa*, *educado*, ... Nuestro intento de clasificar documentos
funcionará mejor si reducimos todas estas palabras a una raíz común,
como *educ*. Este proceso se denomina *stemming* y `tm` implementa
varios algoritmos que funcionan bien en inglés.

    docs <- tm_map(docs, stemDocument)
    docs[[21]]$content

    ## [1] "sen johnson thoma bill assist local law enforc cost clean methamphetamin site sen tim johnson d sd craig thoma r wy today introduc legisl state local pay cost clean methamphetamin meth lab bipartisan legisl mandat portion forfeitur fund administ depart treasuri made avail specif methamphetamin lab clean current money forfeitur fund use expens result drug seizur forfeitur contract servic compens inform unfortun rural communiti becom prefer place methamphetamin product traffick communiti afford enorm cost result clean meth lab bill communiti clean meth site johnson bill draft consult local law enforc offici know hand difficult clean thank commit repair communiti johnson introduc legisl th congress welcom bipartisan support senat thoma small busi owner landlord bear cost clean meth site thoma s fair rural communiti busi owner shoulder cost alon use fund seizur forfeitur defray cost clean site elimin major financi obstacl communiti cost taxpay thoma wyom s senior senat leader mani issu affect rural communiti johnson thoma legisl provid payment design state local tribal law enforc entiti environ health entiti clean area former use methamphetamin lab event lab locat privat properti payment fund exceed cost util properti owner knowledg exist oper lab prior law enforc action upon acquir knowledg law enforc agenc notifi within hour accord estim everi pound meth produc pound toxic wast produc clean cost meth lab site rang signific cost local law enforc entiti absorb member unit state senat member senat appropri committe johnson sought increas feder fund law enforc drug treatment drug prevent initi includ increas fund assist local law enforc high intens drug traffick area hidta initi contact cameron hardi"

Esta colección de documentos podemos, ahora sí, usarla para nuestros
análisis. Pero antes de eso, tenemos que convertirla a una matriz de
términos.

    dtm <- DocumentTermMatrix(docs,
                              control=list(wordLengths=c(2, Inf),
                                           sparse=TRUE))
    inspect(dtm[1:5, 1:5])

    ## <<DocumentTermMatrix (documents: 5, terms: 5)>>
    ## Non-/sparse entries: 6/19
    ## Sparsity           : 76%
    ## Maximal term length: 8
    ## Weighting          : term frequency (tf)
    ## Sample             :
    ##     Terms
    ## Docs abandon absolut access accommod across
    ##    1       1       1      1        1      2
    ##    2       0       0      0        0      0
    ##    3       0       0      1        0      0
    ##    4       0       0      0        0      0
    ##    5       0       0      0        0      0

En esta matriz hemos eliminado palabras muy cortas, de menos de dos
caracteres. Además, también queremos eliminar palabras muy poco
frecuentes, que aparezcan en menos del 90% de los documentos. Con eso
conseguiremos que la matriz no sea tan *sparse*.

    dtm <- removeSparseTerms(dtm , 0.9)
    findFreqTerms(dtm, 1000) ## palabras que aparecen 1000 veces

    ##  [1] "million" "nation"  "presid"  "program" "provid"  "senat"   "state"  
    ##  [8] "today"   "work"    "year"    "bill"    "secur"   "sen"     "fund"

Con el fin de poder referirnos a los documentos que originan cada
término, pondremos los nombres de los archivos como nombres de las filas
de la base de datos.

    rownames(dtm) <- gsub(".*/([0-9]*.*).txt$", "\\1", allfiles)
    saveRDS(dtm, "./dta/dtm.RDS")
