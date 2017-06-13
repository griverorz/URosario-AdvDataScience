El paquete `httr`
-----------------

Antes de empezar con un ejemplo real, veamos la lógica de los dos
paquetes que usaremos a lo largo de esta primera parte:

    library(httr) ## Para interactuar con API
    library(jsonlite) ## Para interactuar con JSON

    ## Loading required package: methods

El paquete `httr` nos permite interacciones con API y, entre otras
functions, nos provee de `GET` para crear peticiones a una página. Por
ejemplo:

    r <- GET("http://httpbin.org/get")
    r

    ## Response [http://httpbin.org/get]
    ##   Date: 2017-06-12 20:25
    ##   Status: 200
    ##   Content-Type: application/json
    ##   Size: 327 B
    ## {
    ##   "args": {}, 
    ##   "headers": {
    ##     "Accept": "application/json, text/xml, application/xml, */*", 
    ##     "Accept-Encoding": "gzip, deflate", 
    ##     "Connection": "close", 
    ##     "Host": "httpbin.org", 
    ##     "User-Agent": "libcurl/7.51.0 r-curl/2.4 httr/1.2.1"
    ##   }, 
    ##   "origin": "190.242.113.150", 
    ## ...

Podemos inspeccionar la estructura del objeto que acabamos de importar:

    str(r)

    ## List of 10
    ##  $ url        : chr "http://httpbin.org/get"
    ##  $ status_code: int 200
    ##  $ headers    :List of 10
    ##   ..$ connection                      : chr "keep-alive"
    ##   ..$ server                          : chr "meinheld/0.6.1"
    ##   ..$ date                            : chr "Mon, 12 Jun 2017 20:25:14 GMT"
    ##   ..$ content-type                    : chr "application/json"
    ##   ..$ access-control-allow-origin     : chr "*"
    ##   ..$ access-control-allow-credentials: chr "true"
    ##   ..$ x-powered-by                    : chr "Flask"
    ##   ..$ x-processed-time                : chr "0.00120496749878"
    ##   ..$ content-length                  : chr "327"
    ##   ..$ via                             : chr "1.1 vegur"
    ##   ..- attr(*, "class")= chr [1:2] "insensitive" "list"
    ##  $ all_headers:List of 1
    ##   ..$ :List of 3
    ##   .. ..$ status : int 200
    ##   .. ..$ version: chr "HTTP/1.1"
    ##   .. ..$ headers:List of 10
    ##   .. .. ..$ connection                      : chr "keep-alive"
    ##   .. .. ..$ server                          : chr "meinheld/0.6.1"
    ##   .. .. ..$ date                            : chr "Mon, 12 Jun 2017 20:25:14 GMT"
    ##   .. .. ..$ content-type                    : chr "application/json"
    ##   .. .. ..$ access-control-allow-origin     : chr "*"
    ##   .. .. ..$ access-control-allow-credentials: chr "true"
    ##   .. .. ..$ x-powered-by                    : chr "Flask"
    ##   .. .. ..$ x-processed-time                : chr "0.00120496749878"
    ##   .. .. ..$ content-length                  : chr "327"
    ##   .. .. ..$ via                             : chr "1.1 vegur"
    ##   .. .. ..- attr(*, "class")= chr [1:2] "insensitive" "list"
    ##  $ cookies    :'data.frame': 0 obs. of  7 variables:
    ##   ..$ domain    : logi(0) 
    ##   ..$ flag      : logi(0) 
    ##   ..$ path      : logi(0) 
    ##   ..$ secure    : logi(0) 
    ##   ..$ expiration:Classes 'POSIXct', 'POSIXt'  num(0) 
    ##   ..$ name      : logi(0) 
    ##   ..$ value     : logi(0) 
    ##  $ content    : raw [1:327] 7b 0a 20 20 ...
    ##  $ date       : POSIXct[1:1], format: "2017-06-12 20:25:14"
    ##  $ times      : Named num [1:6] 0 0.124 0.195 0.195 0.271 ...
    ##   ..- attr(*, "names")= chr [1:6] "redirect" "namelookup" "connect" "pretransfer" ...
    ##  $ request    :List of 7
    ##   ..$ method    : chr "GET"
    ##   ..$ url       : chr "http://httpbin.org/get"
    ##   ..$ headers   : Named chr "application/json, text/xml, application/xml, */*"
    ##   .. ..- attr(*, "names")= chr "Accept"
    ##   ..$ fields    : NULL
    ##   ..$ options   :List of 2
    ##   .. ..$ useragent: chr "libcurl/7.51.0 r-curl/2.4 httr/1.2.1"
    ##   .. ..$ httpget  : logi TRUE
    ##   ..$ auth_token: NULL
    ##   ..$ output    : list()
    ##   .. ..- attr(*, "class")= chr [1:2] "write_memory" "write_function"
    ##   ..- attr(*, "class")= chr "request"
    ##  $ handle     :Class 'curl_handle' <externalptr> 
    ##  - attr(*, "class")= chr "response"

y podemos ver que incluye información acerca del *header*, el *request*
que hemos hecho, el *status code* que ha sido devuelto y, por supuesto,
el *content* que la página devuelve. El paquete ofrece formas de acceder
a esta información:

    status_code(r)

    ## [1] 200

    headers(r)

    ## $connection
    ## [1] "keep-alive"
    ## 
    ## $server
    ## [1] "meinheld/0.6.1"
    ## 
    ## $date
    ## [1] "Mon, 12 Jun 2017 20:25:14 GMT"
    ## 
    ## $`content-type`
    ## [1] "application/json"
    ## 
    ## $`access-control-allow-origin`
    ## [1] "*"
    ## 
    ## $`access-control-allow-credentials`
    ## [1] "true"
    ## 
    ## $`x-powered-by`
    ## [1] "Flask"
    ## 
    ## $`x-processed-time`
    ## [1] "0.00120496749878"
    ## 
    ## $`content-length`
    ## [1] "327"
    ## 
    ## $via
    ## [1] "1.1 vegur"
    ## 
    ## attr(,"class")
    ## [1] "insensitive" "list"

    http_status(r)

    ## $category
    ## [1] "Success"
    ## 
    ## $reason
    ## [1] "OK"
    ## 
    ## $message
    ## [1] "Success: (200) OK"

Quizás la parte más interesante para nosotros ahora mismo sea el
contenido de la petición:

    str(content(r))

    ## List of 4
    ##  $ args   : Named list()
    ##  $ headers:List of 5
    ##   ..$ Accept         : chr "application/json, text/xml, application/xml, */*"
    ##   ..$ Accept-Encoding: chr "gzip, deflate"
    ##   ..$ Connection     : chr "close"
    ##   ..$ Host           : chr "httpbin.org"
    ##   ..$ User-Agent     : chr "libcurl/7.51.0 r-curl/2.4 httr/1.2.1"
    ##  $ origin : chr "190.242.113.150"
    ##  $ url    : chr "http://httpbin.org/get"

Podemos extraerla de muchas formas, pero la función `content` nos ofrece
la posibilidad de retornarla como texto:

    content(r, "text")

    ## No encoding supplied: defaulting to UTF-8.

    ## [1] "{\n  \"args\": {}, \n  \"headers\": {\n    \"Accept\": \"application/json, text/xml, application/xml, */*\", \n    \"Accept-Encoding\": \"gzip, deflate\", \n    \"Connection\": \"close\", \n    \"Host\": \"httpbin.org\", \n    \"User-Agent\": \"libcurl/7.51.0 r-curl/2.4 httr/1.2.1\"\n  }, \n  \"origin\": \"190.242.113.150\", \n  \"url\": \"http://httpbin.org/get\"\n}\n"

La cadena de texto que acabamos de recuperar está en formato JSON.
Podemos convertirla manualmente a un objeto nativo de `R`

    nr <- fromJSON(content(r, "text"))

    ## No encoding supplied: defaulting to UTF-8.

    nr

    ## $args
    ## named list()
    ## 
    ## $headers
    ## $headers$Accept
    ## [1] "application/json, text/xml, application/xml, */*"
    ## 
    ## $headers$`Accept-Encoding`
    ## [1] "gzip, deflate"
    ## 
    ## $headers$Connection
    ## [1] "close"
    ## 
    ## $headers$Host
    ## [1] "httpbin.org"
    ## 
    ## $headers$`User-Agent`
    ## [1] "libcurl/7.51.0 r-curl/2.4 httr/1.2.1"
    ## 
    ## 
    ## $origin
    ## [1] "190.242.113.150"
    ## 
    ## $url
    ## [1] "http://httpbin.org/get"

    nr$headers

    ## $Accept
    ## [1] "application/json, text/xml, application/xml, */*"
    ## 
    ## $`Accept-Encoding`
    ## [1] "gzip, deflate"
    ## 
    ## $Connection
    ## [1] "close"
    ## 
    ## $Host
    ## [1] "httpbin.org"
    ## 
    ## $`User-Agent`
    ## [1] "libcurl/7.51.0 r-curl/2.4 httr/1.2.1"

aunque el paquete `httr` permite devolver el contenido directamente en
una lista

    content(r, "parsed")

    ## $args
    ## named list()
    ## 
    ## $headers
    ## $headers$Accept
    ## [1] "application/json, text/xml, application/xml, */*"
    ## 
    ## $headers$`Accept-Encoding`
    ## [1] "gzip, deflate"
    ## 
    ## $headers$Connection
    ## [1] "close"
    ## 
    ## $headers$Host
    ## [1] "httpbin.org"
    ## 
    ## $headers$`User-Agent`
    ## [1] "libcurl/7.51.0 r-curl/2.4 httr/1.2.1"
    ## 
    ## 
    ## $origin
    ## [1] "190.242.113.150"
    ## 
    ## $url
    ## [1] "http://httpbin.org/get"

Datos Abiertos de Colombia
--------------------------

El portal de Datos Abiertos de Colombia ofrece acceso a una gran
cantidad de datos de diferentes tipos y orígenes. Algunos de ellos, son
ofrecidos como una web API. Tenemos información general sobre el portal
y una introducción a cómo usar los datos y conseguir la llave de acceso
en el [área de
desarrolladores](https://herramientas.datos.gov.co/es/content/desarrollar-usando-los-datos).

Como ejemplo de uso, examinaremos los datos de hurtos de celulares
durante el año 2015. Información general sobre la base de datos y qué
significa cada campo se puede encontrar en la [página de
documentación](https://dev.socrata.com/foundry/www.datos.gov.co/7qbg-3nz6).

Empezaremos por fijar el *endpoint* que da acceso a los datos, que
tienen el código `mtn5-6956`

    URL <- "https://www.datos.gov.co/resource/mtn5-6956.json"

Tal y como se explica en la documentación, el acceso a los datos está
regulado mediante un *token* que debe ser introducido en el *header* de
la petición.

Una consideración de seguridad importante: nunca almacenéis o uséis esta
token directamente en el código. En mi caso, he guardado el *token* en
un archivo de texto plano que puedo leer con la función `readLines`

    creds <- readLines("./credentials-datos-abiertos.txt")
    r <- GET(URL, 
             add_headers("X-App-Token"=creds))

Antes de continuar veamos un poco sobre la respuesta. Los *status codes*
de la web API se pueden consultar en [la
documentación](https://dev.socrata.com/docs/response-codes.html)

    status_code(r)

    ## [1] 200

Comprobemos también el tipo de información que es devuelta por la API.
Es importante fijarse en los campos `x-soda2-fields` y `x-soda-types`,
así como en el `encoding` de la respuesta.

    headers(r)

    ## $server
    ## [1] "nginx"
    ## 
    ## $date
    ## [1] "Mon, 12 Jun 2017 20:25:15 GMT"
    ## 
    ## $`content-type`
    ## [1] "application/json; charset=utf-8"
    ## 
    ## $`transfer-encoding`
    ## [1] "chunked"
    ## 
    ## $connection
    ## [1] "keep-alive"
    ## 
    ## $`x-socrata-requestid`
    ## [1] "898mlur7i94pvs8qpeg7nyho3"
    ## 
    ## $`access-control-allow-origin`
    ## [1] "*"
    ## 
    ## $etag
    ## [1] "W/\"2bb564a1d7662b5b63f7e2570e05a6f3\""
    ## 
    ## $`last-modified`
    ## [1] "Wed, 07 Jun 2017 15:44:36 UTC"
    ## 
    ## $`x-soda2-warning`
    ## [1] "X-SODA2-Fields, X-SODA2-Types, and X-SODA2-Legacy-Types are deprecated"
    ## 
    ## $`x-soda2-fields`
    ## [1] "[\"m_vil_agresor\",\"barrio\",\"m_vil_victima\",\"municipio\",\"hora\",\"d_a\",\"estado_civil\",\"profesi_n\",\"edad\",\"linea\",\"arma_empleada\",\"clase\",\"fecha\",\"zona\",\"escolaridad\",\"marca\",\"pa_s_de_nacimiento\",\"c_digo_dane\",\"clase_de_sitio\",\"departamento\",\"clase_de_empleado\",\"cantidad\",\"sexo\"]"
    ## 
    ## $`x-soda2-types`
    ## [1] "[\"text\",\"text\",\"text\",\"text\",\"text\",\"text\",\"text\",\"text\",\"number\",\"text\",\"text\",\"text\",\"calendar_date\",\"text\",\"text\",\"text\",\"text\",\"html\",\"text\",\"text\",\"text\",\"number\",\"text\"]"
    ## 
    ## $`x-soda2-legacy-types`
    ## [1] "true"
    ## 
    ## $age
    ## [1] "2067"
    ## 
    ## $`x-socrata-region`
    ## [1] "aws-us-east-1-fedramp-prod"
    ## 
    ## $`content-encoding`
    ## [1] "gzip"
    ## 
    ## attr(,"class")
    ## [1] "insensitive" "list"

Con esto podemos hacernos una idea de la información que está
disponible.

Veamos ahora el contenido

    datos <- content(r, "text")

Como veíamos antes, el contendo es devuelto en un texto en formato JSON
que podemos transformar en una lista usando la función `fromJSON` del
paquete `jsonlite`. La función además nos ofrece la posibilidad de
simplificar en resultado a un `data.frame` en el caso de que sea
posible:

    jsonlist <- fromJSON(datos, simplifyDataFrame=FALSE)
    jsondf <- fromJSON(datos, simplifyDataFrame=TRUE)

Comprobemos que los resultados son correctos, o al menos consistentes:

    length(jsonlist)

    ## [1] 1000

    dim(jsondf)

    ## [1] 1000   23

El número de casos está determinado por los parámetros por defecto de la
búsqueda. Claramente es algo que debemos poder modificar. Para ello,
podemos ir a la
[documentación](https://dev.socrata.com/consumers/getting-started.html)
y comprobar que lo que necesitamos es añadir a la dirección los
parámetros `$limit` y `$offset`:

    URL2 <- paste0(URL, "?$limit=100&$offset=100")
    r <- GET(URL2,         
             add_headers("X-App-Token"=creds))

y ahora podemos comprobar la nueva longitud del resultado

    length(content(r, "parse"))

    ## [1] 100

Pasar parámetros a la URL es la forma manual de ejecutar búsquedas
parametrizadas, pero eso nos limita mucho. Por fortuna, `GET` admite un
argumento que nos permite añadir parámetros a la búsqueda de una forma
más natural:

    r <- GET(URL,
             query=list("$limit"=25),
             add_headers("X-App-Token"=creds))
    length(content(r, "parse"))

    ## [1] 25

Por supuesto, podemos pasar más parámetros a la búsqueda para ir a
observaciones más concretas, como por ejemplo:

    r <- GET(URL,
             query=list("d_a"="LUNES",
                        "municipio"="CARTAGENA (CT)",
                        "$limit"=1000),
             add_headers("X-App-Token"=creds))
    head(content(r, "parse"))

    ## [[1]]
    ## [[1]]$m_vil_agresor
    ## [1] "A PIE"
    ## 
    ## [[1]]$barrio
    ## [1] "LA BOQUILLA"
    ## 
    ## [[1]]$m_vil_victima
    ## [1] "A PIE"
    ## 
    ## [[1]]$municipio
    ## [1] "CARTAGENA (CT)"
    ## 
    ## [[1]]$hora
    ## [1] "6:00:00"
    ## 
    ## [[1]]$d_a
    ## [1] "Lunes"
    ## 
    ## [[1]]$estado_civil
    ## [1] "UNION LIBRE"
    ## 
    ## [[1]]$profesi_n
    ## [1] "POLICIA"
    ## 
    ## [[1]]$edad
    ## [1] "27"
    ## 
    ## [[1]]$linea
    ## [1] "LINEA STANDARD"
    ## 
    ## [[1]]$arma_empleada
    ## [1] "SIN EMPLEO DE ARMAS"
    ## 
    ## [[1]]$clase
    ## [1] "CELULAR"
    ## 
    ## [[1]]$fecha
    ## [1] "2017-09-01T00:00:00"
    ## 
    ## [[1]]$zona
    ## [1] "URBANA"
    ## 
    ## [[1]]$escolaridad
    ## [1] "TECNICO"
    ## 
    ## [[1]]$marca
    ## [1] "NO REPORTADO"
    ## 
    ## [[1]]$pa_s_de_nacimiento
    ## [1] "COLOMBIA"
    ## 
    ## [[1]]$c_digo_dane
    ## [1] "13001000"
    ## 
    ## [[1]]$clase_de_sitio
    ## [1] "VIAS PUBLICAS"
    ## 
    ## [[1]]$departamento
    ## [1] "BOLÍVAR"
    ## 
    ## [[1]]$clase_de_empleado
    ## [1] "EMPLEADO POLICIAL"
    ## 
    ## [[1]]$cantidad
    ## [1] "2"
    ## 
    ## [[1]]$sexo
    ## [1] "MASCULINO"
    ## 
    ## 
    ## [[2]]
    ## [[2]]$m_vil_agresor
    ## [1] "PASAJERO MOTOCICLETA"
    ## 
    ## [[2]]$barrio
    ## [1] "EL BIFFI"
    ## 
    ## [[2]]$m_vil_victima
    ## [1] "A PIE"
    ## 
    ## [[2]]$municipio
    ## [1] "CARTAGENA (CT)"
    ## 
    ## [[2]]$hora
    ## [1] "13:45:00"
    ## 
    ## [[2]]$d_a
    ## [1] "Lunes"
    ## 
    ## [[2]]$estado_civil
    ## [1] "SOLTERO"
    ## 
    ## [[2]]$profesi_n
    ## [1] "ECONOMISTA"
    ## 
    ## [[2]]$edad
    ## [1] "34"
    ## 
    ## [[2]]$linea
    ## [1] "LINEA STANDARD"
    ## 
    ## [[2]]$arma_empleada
    ## [1] "ARMA DE FUEGO"
    ## 
    ## [[2]]$clase
    ## [1] "CELULAR"
    ## 
    ## [[2]]$fecha
    ## [1] "2017-09-01T00:00:00"
    ## 
    ## [[2]]$zona
    ## [1] "URBANA"
    ## 
    ## [[2]]$escolaridad
    ## [1] "SUPERIOR"
    ## 
    ## [[2]]$marca
    ## [1] "NO REPORTADO"
    ## 
    ## [[2]]$pa_s_de_nacimiento
    ## [1] "COLOMBIA"
    ## 
    ## [[2]]$c_digo_dane
    ## [1] "13001000"
    ## 
    ## [[2]]$clase_de_sitio
    ## [1] "VIAS PUBLICAS"
    ## 
    ## [[2]]$departamento
    ## [1] "BOLÍVAR"
    ## 
    ## [[2]]$clase_de_empleado
    ## [1] "EMPLEADO PARTICULAR"
    ## 
    ## [[2]]$cantidad
    ## [1] "1"
    ## 
    ## [[2]]$sexo
    ## [1] "FEMENINO"
    ## 
    ## 
    ## [[3]]
    ## [[3]]$m_vil_agresor
    ## [1] "A PIE"
    ## 
    ## [[3]]$barrio
    ## [1] "SAN FERNANDO"
    ## 
    ## [[3]]$m_vil_victima
    ## [1] "A PIE"
    ## 
    ## [[3]]$municipio
    ## [1] "CARTAGENA (CT)"
    ## 
    ## [[3]]$hora
    ## [1] "23:50:00"
    ## 
    ## [[3]]$d_a
    ## [1] "Lunes"
    ## 
    ## [[3]]$estado_civil
    ## [1] "SOLTERO"
    ## 
    ## [[3]]$profesi_n
    ## [1] "-"
    ## 
    ## [[3]]$edad
    ## [1] "24"
    ## 
    ## [[3]]$linea
    ## [1] "MOTO G"
    ## 
    ## [[3]]$arma_empleada
    ## [1] "ARMA BLANCA / CORTOPUNZANTE"
    ## 
    ## [[3]]$clase
    ## [1] "CELULAR"
    ## 
    ## [[3]]$fecha
    ## [1] "2017-09-01T00:00:00"
    ## 
    ## [[3]]$zona
    ## [1] "URBANA"
    ## 
    ## [[3]]$escolaridad
    ## [1] "SECUNDARIA"
    ## 
    ## [[3]]$marca
    ## [1] "MOTOROLA"
    ## 
    ## [[3]]$pa_s_de_nacimiento
    ## [1] "COLOMBIA"
    ## 
    ## [[3]]$c_digo_dane
    ## [1] "13001000"
    ## 
    ## [[3]]$clase_de_sitio
    ## [1] "VIAS PUBLICAS"
    ## 
    ## [[3]]$departamento
    ## [1] "BOLÍVAR"
    ## 
    ## [[3]]$clase_de_empleado
    ## [1] "EMPLEADO PARTICULAR"
    ## 
    ## [[3]]$cantidad
    ## [1] "1"
    ## 
    ## [[3]]$sexo
    ## [1] "FEMENINO"
    ## 
    ## 
    ## [[4]]
    ## [[4]]$m_vil_agresor
    ## [1] "PASAJERO BUS"
    ## 
    ## [[4]]$barrio
    ## [1] "CENTRO"
    ## 
    ## [[4]]$m_vil_victima
    ## [1] "PASAJERO BUS"
    ## 
    ## [[4]]$municipio
    ## [1] "CARTAGENA (CT)"
    ## 
    ## [[4]]$hora
    ## [1] "14:30:00"
    ## 
    ## [[4]]$d_a
    ## [1] "Lunes"
    ## 
    ## [[4]]$estado_civil
    ## [1] "SOLTERO"
    ## 
    ## [[4]]$profesi_n
    ## [1] "-"
    ## 
    ## [[4]]$edad
    ## [1] "23"
    ## 
    ## [[4]]$linea
    ## [1] "GALAXY S5"
    ## 
    ## [[4]]$arma_empleada
    ## [1] "SIN EMPLEO DE ARMAS"
    ## 
    ## [[4]]$clase
    ## [1] "CELULAR"
    ## 
    ## [[4]]$zona
    ## [1] "URBANA"
    ## 
    ## [[4]]$escolaridad
    ## [1] "SECUNDARIA"
    ## 
    ## [[4]]$marca
    ## [1] "SAMSUNG"
    ## 
    ## [[4]]$pa_s_de_nacimiento
    ## [1] "COLOMBIA"
    ## 
    ## [[4]]$c_digo_dane
    ## [1] "13001000"
    ## 
    ## [[4]]$clase_de_sitio
    ## [1] "BUS MEGABUS"
    ## 
    ## [[4]]$departamento
    ## [1] "BOLÍVAR"
    ## 
    ## [[4]]$clase_de_empleado
    ## [1] "EMPLEADO PARTICULAR"
    ## 
    ## [[4]]$cantidad
    ## [1] "1"
    ## 
    ## [[4]]$sexo
    ## [1] "FEMENINO"
    ## 
    ## 
    ## [[5]]
    ## [[5]]$m_vil_agresor
    ## [1] "A PIE"
    ## 
    ## [[5]]$barrio
    ## [1] "LOS CARACOLES"
    ## 
    ## [[5]]$m_vil_victima
    ## [1] "A PIE"
    ## 
    ## [[5]]$municipio
    ## [1] "CARTAGENA (CT)"
    ## 
    ## [[5]]$hora
    ## [1] "2:30:00"
    ## 
    ## [[5]]$d_a
    ## [1] "Lunes"
    ## 
    ## [[5]]$estado_civil
    ## [1] "SOLTERO"
    ## 
    ## [[5]]$profesi_n
    ## [1] "-"
    ## 
    ## [[5]]$edad
    ## [1] "20"
    ## 
    ## [[5]]$linea
    ## [1] "LINEA STANDARD"
    ## 
    ## [[5]]$arma_empleada
    ## [1] "ARMA BLANCA / CORTOPUNZANTE"
    ## 
    ## [[5]]$clase
    ## [1] "CELULAR"
    ## 
    ## [[5]]$zona
    ## [1] "RURAL"
    ## 
    ## [[5]]$escolaridad
    ## [1] "SUPERIOR"
    ## 
    ## [[5]]$marca
    ## [1] "NO REPORTADO"
    ## 
    ## [[5]]$pa_s_de_nacimiento
    ## [1] "COLOMBIA"
    ## 
    ## [[5]]$c_digo_dane
    ## [1] "13001000"
    ## 
    ## [[5]]$clase_de_sitio
    ## [1] "PLAYA"
    ## 
    ## [[5]]$departamento
    ## [1] "BOLÍVAR"
    ## 
    ## [[5]]$clase_de_empleado
    ## [1] "ESTUDIANTE"
    ## 
    ## [[5]]$cantidad
    ## [1] "1"
    ## 
    ## [[5]]$sexo
    ## [1] "MASCULINO"
    ## 
    ## 
    ## [[6]]
    ## [[6]]$m_vil_agresor
    ## [1] "PASAJERO MOTOCICLETA"
    ## 
    ## [[6]]$barrio
    ## [1] "BOSTON"
    ## 
    ## [[6]]$m_vil_victima
    ## [1] "A PIE"
    ## 
    ## [[6]]$municipio
    ## [1] "CARTAGENA (CT)"
    ## 
    ## [[6]]$hora
    ## [1] "22:10:00"
    ## 
    ## [[6]]$d_a
    ## [1] "Lunes"
    ## 
    ## [[6]]$estado_civil
    ## [1] "UNION LIBRE"
    ## 
    ## [[6]]$profesi_n
    ## [1] "-"
    ## 
    ## [[6]]$edad
    ## [1] "28"
    ## 
    ## [[6]]$linea
    ## [1] "MOTO G"
    ## 
    ## [[6]]$arma_empleada
    ## [1] "SIN EMPLEO DE ARMAS"
    ## 
    ## [[6]]$clase
    ## [1] "CELULAR"
    ## 
    ## [[6]]$zona
    ## [1] "URBANA"
    ## 
    ## [[6]]$escolaridad
    ## [1] "SECUNDARIA"
    ## 
    ## [[6]]$marca
    ## [1] "MOTOROLA"
    ## 
    ## [[6]]$pa_s_de_nacimiento
    ## [1] "COLOMBIA"
    ## 
    ## [[6]]$c_digo_dane
    ## [1] "13001000"
    ## 
    ## [[6]]$clase_de_sitio
    ## [1] "CASAS DE HABITACION"
    ## 
    ## [[6]]$departamento
    ## [1] "BOLÍVAR"
    ## 
    ## [[6]]$clase_de_empleado
    ## [1] "AMA DE CASA"
    ## 
    ## [[6]]$cantidad
    ## [1] "1"
    ## 
    ## [[6]]$sexo
    ## [1] "FEMENINO"

Un ejercicio más práctico
-------------------------

Imaginemos que queremos recuperar todos los datos de la base de datos de
hurtos de celulares. Para eso tenemos que pasar páginas en nuestro
código y también guardar los resultados intermedios. Como a priori no
sabemos cuántas páginas hay, tenemos que verificar en cada llamada que
hemos recibido datos.

Para hacer nuestra vida más fácil, crearemos una función que nos permite
crear la lista con `limit` y `offset` que se corresponde con cada página

    pasar_pagina <- function(i, block=1000) {
        return(list("$limit"=block,
                    "$offset"=block*(i - 1))) 
    }
    pasar_pagina(1)

    ## $`$limit`
    ## [1] 1000
    ## 
    ## $`$offset`
    ## [1] 0

    pasar_pagina(3)

    ## $`$limit`
    ## [1] 1000
    ## 
    ## $`$offset`
    ## [1] 2000

Ahora lo que haremos será escribir un `while` que cree la llamada
correspondiente a esa página y que, si el resultado es correcto, acumule
los resultados en una lista (`res` en este caso). Cuando encontremos que
la llamada devuelve un objeto vacío, pararemos la ejecución.

    status <- TRUE
    i <- 1
    res <- list()
    while(status) {
        print(sprintf("Recogiendo datos de pagina %s", i))
        r <- GET(URL,
                 query=pasar_pagina(i),
                 add_headers("X-App-Token"=creds))
        status <- status_code(r) == 200
        if (status) {
            print("...Guardando datos")
            res[[i]] <- fromJSON(content(r, "text"))
        }
        if (i >= 10) { ## Para no eternizarnos
            status <- FALSE
        }
        Sys.sleep(5)
        i <- i + 1
    }

    ## [1] "Recogiendo datos de pagina 1"
    ## [1] "...Guardando datos"
    ## [1] "Recogiendo datos de pagina 2"
    ## [1] "...Guardando datos"
    ## [1] "Recogiendo datos de pagina 3"
    ## [1] "...Guardando datos"
    ## [1] "Recogiendo datos de pagina 4"
    ## [1] "...Guardando datos"
    ## [1] "Recogiendo datos de pagina 5"
    ## [1] "...Guardando datos"
    ## [1] "Recogiendo datos de pagina 6"
    ## [1] "...Guardando datos"
    ## [1] "Recogiendo datos de pagina 7"
    ## [1] "...Guardando datos"
    ## [1] "Recogiendo datos de pagina 8"
    ## [1] "...Guardando datos"
    ## [1] "Recogiendo datos de pagina 9"
    ## [1] "...Guardando datos"
    ## [1] "Recogiendo datos de pagina 10"
    ## [1] "...Guardando datos"

Un par de cosas importantes a notar. En primer lugar, que hemos creado
el objeto `res` con antelación sin especificar qué tamaño tiene. Solo
hemos dicho que es una lista. A medida que vamos pidiendo páginas,
hacemos crecer la lista al asignar nuevos elementos `res[[i]] <-`. En
segundo lugar, imprimimos en pantalla información acerca de dónde está
operando ahora nuestro `while`. Esto no servirá de ayuda para investigar
si hubiese problemas. En tercer lugar, hemos puesto una llamada a
`Sys.sleep(5)` entre cada iteración. Esto consigue que la ejecución se
detenga durante 5 segundos. Es buena práctica y, de hecho, puede ser
necesario para evitar los límites que el servidor puede haber
establecido (veremos esto con más detalle en la próxima sección). Por
último, hemos actualizado el valor de `status` dinámicamente durante el
código para contrastar si realmente estamos ante la última página de
datos. Cuando `status` pasa a ser `FALSE`, la ejecución se para.

Una vez finaliza la ejecución, tendremos *una lista de bases de datos*.

    str(res)

    ## List of 10
    ##  $ :'data.frame':    1000 obs. of  23 variables:
    ##   ..$ m_vil_agresor     : chr [1:1000] "A PIE" "A PIE" "PASAJERO MOTOCICLETA" "PASAJERO MOTOCICLETA" ...
    ##   ..$ barrio            : chr [1:1000] "JARDIN" "VDA. SAN FELIX" "SANTA RITA" "VDA. SAN FELIX" ...
    ##   ..$ m_vil_victima     : chr [1:1000] "A PIE" "A PIE" "A PIE" "CONDUCTOR VEHICULO" ...
    ##   ..$ municipio         : chr [1:1000] "LETICIA (CT)" "BELLO" "BELLO" "BELLO" ...
    ##   ..$ hora              : chr [1:1000] "8:00:00" "0:00:00" "21:45:00" "10:35:00" ...
    ##   ..$ d_a               : chr [1:1000] "Domingo" "Domingo" "Domingo" "Domingo" ...
    ##   ..$ estado_civil      : chr [1:1000] "UNION LIBRE" "SOLTERO" "SOLTERO" "CASADO" ...
    ##   ..$ profesi_n         : chr [1:1000] "-" "-" "-" "-" ...
    ##   ..$ edad              : chr [1:1000] "29" "33" "22" "42" ...
    ##   ..$ linea             : chr [1:1000] "J700" "LINEA STANDARD" "GALAXY J" "5S" ...
    ##   ..$ arma_empleada     : chr [1:1000] "CONTUNDENTES" "ARMA DE FUEGO" "ARMA DE FUEGO" "ARMA BLANCA / CORTOPUNZANTE" ...
    ##   ..$ clase             : chr [1:1000] "CELULAR" "CELULAR" "CELULAR" "CELULAR" ...
    ##   ..$ fecha             : chr [1:1000] "2017-01-01T00:00:00" "2017-01-01T00:00:00" "2017-01-01T00:00:00" "2017-01-01T00:00:00" ...
    ##   ..$ zona              : chr [1:1000] "URBANA" "RURAL" "URBANA" "RURAL" ...
    ##   ..$ escolaridad       : chr [1:1000] "TECNICO" "SECUNDARIA" "SECUNDARIA" "SECUNDARIA" ...
    ##   ..$ marca             : chr [1:1000] "SAMSUNG" "LENOVO" "SAMSUNG" "IPHONE" ...
    ##   ..$ pa_s_de_nacimiento: chr [1:1000] "COLOMBIA" "COLOMBIA" "COLOMBIA" "COLOMBIA" ...
    ##   ..$ c_digo_dane       : chr [1:1000] "91001000" "05088000" "05088000" "05088000" ...
    ##   ..$ clase_de_sitio    : chr [1:1000] "CASAS DE HABITACION" "VIAS PUBLICAS" "VIAS PUBLICAS" "VIAS PUBLICAS" ...
    ##   ..$ departamento      : chr [1:1000] "AMAZONAS" "ANTIOQUIA" "ANTIOQUIA" "ANTIOQUIA" ...
    ##   ..$ clase_de_empleado : chr [1:1000] "INDEPENDIENTE" "EMPLEADO PARTICULAR" "INDEPENDIENTE" "INDEPENDIENTE" ...
    ##   ..$ cantidad          : chr [1:1000] "1" "1" "1" "1" ...
    ##   ..$ sexo              : chr [1:1000] "FEMENINO" "MASCULINO" "MASCULINO" "MASCULINO" ...
    ##  $ :'data.frame':    1000 obs. of  23 variables:
    ##   ..$ m_vil_agresor     : chr [1:1000] "A PIE" "A PIE" "A PIE" "A PIE" ...
    ##   ..$ barrio            : chr [1:1000] "BALCONES DE LA FRONTERA" "CHAMBU" "LOS CHILCOS" "LA RIVERA" ...
    ##   ..$ m_vil_victima     : chr [1:1000] "A PIE" "A PIE" "A PIE" "A PIE" ...
    ##   ..$ municipio         : chr [1:1000] "IPIALES" "IPIALES" "IPIALES" "PASTO (CT)" ...
    ##   ..$ hora              : chr [1:1000] "20:30:00" "20:30:00" "9:30:00" "19:00:00" ...
    ##   ..$ d_a               : chr [1:1000] "Viernes" "Viernes" "Viernes" "Viernes" ...
    ##   ..$ estado_civil      : chr [1:1000] "UNION LIBRE" "UNION LIBRE" "UNION LIBRE" "CASADO" ...
    ##   ..$ profesi_n         : chr [1:1000] "-" "-" "-" "ABOGADO" ...
    ##   ..$ edad              : chr [1:1000] "33" "36" "59" "43" ...
    ##   ..$ linea             : chr [1:1000] "MOTO G" "GALAXY J" "J200" "6" ...
    ##   ..$ arma_empleada     : chr [1:1000] "ARMA BLANCA / CORTOPUNZANTE" "ARMA BLANCA / CORTOPUNZANTE" "SIN EMPLEO DE ARMAS" "ARMA BLANCA / CORTOPUNZANTE" ...
    ##   ..$ clase             : chr [1:1000] "CELULAR" "CELULAR" "CELULAR" "CELULAR" ...
    ##   ..$ fecha             : chr [1:1000] "2017-06-01T00:00:00" "2017-06-01T00:00:00" "2017-06-01T00:00:00" "2017-06-01T00:00:00" ...
    ##   ..$ zona              : chr [1:1000] "URBANA" "URBANA" "URBANA" "URBANA" ...
    ##   ..$ escolaridad       : chr [1:1000] "SUPERIOR" "SECUNDARIA" "PRIMARIA" "SUPERIOR" ...
    ##   ..$ marca             : chr [1:1000] "MOTOROLA" "SAMSUNG" "SAMSUNG" "IPHONE" ...
    ##   ..$ pa_s_de_nacimiento: chr [1:1000] "COLOMBIA" "COLOMBIA" "COLOMBIA" "COLOMBIA" ...
    ##   ..$ c_digo_dane       : chr [1:1000] "52356000" "52356000" "52356000" "52001000" ...
    ##   ..$ clase_de_sitio    : chr [1:1000] "FRENTE CONJUNTO - VIA PUBLICA" "VIAS PUBLICAS" "VIAS PUBLICAS" "VIAS PUBLICAS" ...
    ##   ..$ departamento      : chr [1:1000] "NARIÑO" "NARIÑO" "NARIÑO" "NARIÑO" ...
    ##   ..$ clase_de_empleado : chr [1:1000] "ESTUDIANTE" "EMPLEADO PARTICULAR" "INDEPENDIENTE" "POLITICO" ...
    ##   ..$ cantidad          : chr [1:1000] "1" "1" "1" "1" ...
    ##   ..$ sexo              : chr [1:1000] "MASCULINO" "FEMENINO" "MASCULINO" "MASCULINO" ...
    ##  $ :'data.frame':    1000 obs. of  23 variables:
    ##   ..$ m_vil_agresor     : chr [1:1000] "CONDUCTOR MOTOCICLETA" "A PIE" "PASAJERO BUS" "PASAJERO BUS" ...
    ##   ..$ barrio            : chr [1:1000] "EL EJIDO E-16" "TEUSAQUILLO E-13" "EL SOCORRO E-18" "7 DE AGOSTO E-12" ...
    ##   ..$ m_vil_victima     : chr [1:1000] "A PIE" "A PIE" "PASAJERO BUS" "PASAJERO BUS" ...
    ##   ..$ municipio         : chr [1:1000] "BOGOTÁ D.C. (CT)" "BOGOTÁ D.C. (CT)" "BOGOTÁ D.C. (CT)" "BOGOTÁ D.C. (CT)" ...
    ##   ..$ hora              : chr [1:1000] "15:30:00" "17:30:00" "13:00:00" "13:30:00" ...
    ##   ..$ d_a               : chr [1:1000] "Jueves" "Jueves" "Jueves" "Jueves" ...
    ##   ..$ estado_civil      : chr [1:1000] "SOLTERO" "SOLTERO" "SEPARADO" "CASADO" ...
    ##   ..$ profesi_n         : chr [1:1000] "-" "-" "-" "ADMINISTRADOR PUBLICO" ...
    ##   ..$ edad              : chr [1:1000] "42" "22" "25" "53" ...
    ##   ..$ linea             : chr [1:1000] "LINEA STANDARD" "LINEA STANDARD" "LINEA STANDARD" "LINEA STANDARD" ...
    ##   ..$ arma_empleada     : chr [1:1000] "SIN EMPLEO DE ARMAS" "SIN EMPLEO DE ARMAS" "SIN EMPLEO DE ARMAS" "ARMA BLANCA / CORTOPUNZANTE" ...
    ##   ..$ clase             : chr [1:1000] "CELULAR" "CELULAR" "CELULAR" "CELULAR" ...
    ##   ..$ fecha             : chr [1:1000] "2017-12-01T00:00:00" "2017-12-01T00:00:00" "2017-12-01T00:00:00" "2017-12-01T00:00:00" ...
    ##   ..$ zona              : chr [1:1000] "URBANA" "URBANA" "URBANA" "URBANA" ...
    ##   ..$ escolaridad       : chr [1:1000] "SECUNDARIA" "SECUNDARIA" "SECUNDARIA" "SUPERIOR" ...
    ##   ..$ marca             : chr [1:1000] "HUAWEI" "SONY" "HUAWEI" "NO REPORTADO" ...
    ##   ..$ pa_s_de_nacimiento: chr [1:1000] "COLOMBIA" "COLOMBIA" "COLOMBIA" "COLOMBIA" ...
    ##   ..$ c_digo_dane       : chr [1:1000] "11001000" "11001000" "11001000" "11001000" ...
    ##   ..$ clase_de_sitio    : chr [1:1000] "VIAS PUBLICAS" "VIAS PUBLICAS" "INTERIOR VEHICULO SITP" "TRANSPORTE PÚBLICO" ...
    ##   ..$ departamento      : chr [1:1000] "CUNDINAMARCA" "CUNDINAMARCA" "CUNDINAMARCA" "CUNDINAMARCA" ...
    ##   ..$ clase_de_empleado : chr [1:1000] "EMPLEADO PARTICULAR" "EMPLEADO PARTICULAR" "EMPLEADO PARTICULAR" "EMPLEADO PUBLICO" ...
    ##   ..$ cantidad          : chr [1:1000] "1" "1" "1" "1" ...
    ##   ..$ sexo              : chr [1:1000] "MASCULINO" "MASCULINO" "FEMENINO" "FEMENINO" ...
    ##  $ :'data.frame':    1000 obs. of  22 variables:
    ##   ..$ m_vil_agresor     : chr [1:1000] "A PIE" "PASAJERO MOTOCICLETA" "PASAJERO MOTOCICLETA" "PASAJERO MOTOCICLETA" ...
    ##   ..$ barrio            : chr [1:1000] "CENTRO" "ARRIBA" "ABAJO" "SAN FRANCISCO" ...
    ##   ..$ m_vil_victima     : chr [1:1000] "A PIE" "A PIE" "A PIE" "A PIE" ...
    ##   ..$ municipio         : chr [1:1000] "ZIPAQUIRÁ" "RIOHACHA (CT)" "RIOHACHA (CT)" "RIOHACHA (CT)" ...
    ##   ..$ hora              : chr [1:1000] "13:40:00" "14:00:00" "19:30:00" "17:00:00" ...
    ##   ..$ d_a               : chr [1:1000] "Martes" "Martes" "Martes" "Martes" ...
    ##   ..$ estado_civil      : chr [1:1000] "SOLTERO" "UNION LIBRE" "CASADO" "CASADO" ...
    ##   ..$ profesi_n         : chr [1:1000] "ENFERMERA JEFE" "PSICOLOGO" "-" "LICENCIADO" ...
    ##   ..$ edad              : chr [1:1000] "20" "36" "26" "48" ...
    ##   ..$ linea             : chr [1:1000] "G7007" "LINEA STANDARD" "ASCEND P6 S" "ASCEND G630" ...
    ##   ..$ arma_empleada     : chr [1:1000] "SIN EMPLEO DE ARMAS" "CONTUNDENTES" "ARMA DE FUEGO" "SIN EMPLEO DE ARMAS" ...
    ##   ..$ clase             : chr [1:1000] "CELULAR" "CELULAR" "CELULAR" "CELULAR" ...
    ##   ..$ zona              : chr [1:1000] "URBANA" "URBANA" "URBANA" "URBANA" ...
    ##   ..$ escolaridad       : chr [1:1000] "SUPERIOR" "SUPERIOR" "SECUNDARIA" "SUPERIOR" ...
    ##   ..$ marca             : chr [1:1000] "HUAWEI" "HUAWEI" "HUAWEI" "HUAWEI" ...
    ##   ..$ pa_s_de_nacimiento: chr [1:1000] "COLOMBIA" "COLOMBIA" "COLOMBIA" "COLOMBIA" ...
    ##   ..$ c_digo_dane       : chr [1:1000] "25899000" "44001000" "44001000" "44001000" ...
    ##   ..$ clase_de_sitio    : chr [1:1000] "VIAS PUBLICAS" "CASAS DE HABITACION" "VIAS PUBLICAS" "VIAS PUBLICAS" ...
    ##   ..$ departamento      : chr [1:1000] "CUNDINAMARCA" "GUAJIRA" "GUAJIRA" "GUAJIRA" ...
    ##   ..$ clase_de_empleado : chr [1:1000] "EMPLEADO PARTICULAR" "EMPLEADO PARTICULAR" "COMERCIANTE" "EMPLEADO PUBLICO" ...
    ##   ..$ cantidad          : chr [1:1000] "1" "1" "1" "1" ...
    ##   ..$ sexo              : chr [1:1000] "FEMENINO" "FEMENINO" "FEMENINO" "FEMENINO" ...
    ##  $ :'data.frame':    1000 obs. of  22 variables:
    ##   ..$ m_vil_agresor     : chr [1:1000] "A PIE" "A PIE" "A PIE" "A PIE" ...
    ##   ..$ barrio            : chr [1:1000] "13 DE JUNIO" "BARRIO VILLA REPUBLICANA" "EL CENTRO" "BARRIO SANTA LUCIA" ...
    ##   ..$ m_vil_victima     : chr [1:1000] "A PIE" "A PIE" "A PIE" "A PIE" ...
    ##   ..$ municipio         : chr [1:1000] "CARTAGENA (CT)" "CHIQUINQUIRÁ" "GUATEQUE" "TUNJA (CT)" ...
    ##   ..$ hora              : chr [1:1000] "0:30:00" "7:45:00" "1:00:00" "20:00:00" ...
    ##   ..$ d_a               : chr [1:1000] "Domingo" "Domingo" "Domingo" "Domingo" ...
    ##   ..$ estado_civil      : chr [1:1000] "UNION LIBRE" "UNION LIBRE" "CASADO" "SOLTERO" ...
    ##   ..$ profesi_n         : chr [1:1000] "POLICIA" "-" "-" "-" ...
    ##   ..$ edad              : chr [1:1000] "33" "28" "41" "16" ...
    ##   ..$ linea             : chr [1:1000] "GALAXY J" "LINEA STANDARD" "LINEA STANDARD" "A1" ...
    ##   ..$ arma_empleada     : chr [1:1000] "ARMA BLANCA / CORTOPUNZANTE" "SIN EMPLEO DE ARMAS" "ARMA DE FUEGO" "ARMA BLANCA / CORTOPUNZANTE" ...
    ##   ..$ clase             : chr [1:1000] "CELULAR" "CELULAR" "CELULAR" "CELULAR" ...
    ##   ..$ zona              : chr [1:1000] "URBANA" "URBANA" "URBANA" "URBANA" ...
    ##   ..$ escolaridad       : chr [1:1000] "TECNICO" "PRIMARIA" "SUPERIOR" "SECUNDARIA" ...
    ##   ..$ marca             : chr [1:1000] "SAMSUNG" "HUAWEI" "NO REPORTADO" "AZUMI" ...
    ##   ..$ pa_s_de_nacimiento: chr [1:1000] "COLOMBIA" "COLOMBIA" "COLOMBIA" "COLOMBIA" ...
    ##   ..$ c_digo_dane       : chr [1:1000] "13001000" "15176000" "15322000" "15001000" ...
    ##   ..$ clase_de_sitio    : chr [1:1000] "VIAS PUBLICAS" "CASAS DE HABITACION" "VIAS PUBLICAS" "VIAS PUBLICAS" ...
    ##   ..$ departamento      : chr [1:1000] "BOLÍVAR" "BOYACÁ" "BOYACÁ" "BOYACÁ" ...
    ##   ..$ clase_de_empleado : chr [1:1000] "EMPLEADO POLICIAL" "AMA DE CASA" "INDEPENDIENTE" "ESTUDIANTE" ...
    ##   ..$ cantidad          : chr [1:1000] "1" "1" "1" "1" ...
    ##   ..$ sexo              : chr [1:1000] "MASCULINO" "FEMENINO" "FEMENINO" "MASCULINO" ...
    ##  $ :'data.frame':    1000 obs. of  23 variables:
    ##   ..$ m_vil_agresor     : chr [1:1000] "A PIE" "A PIE" "A PIE" "A PIE" ...
    ##   ..$ barrio            : chr [1:1000] "BELEN C-16" "VILLANUEVA C-10" "LA CANDELARIA C-10" "LA CANDELARIA C-10" ...
    ##   ..$ m_vil_victima     : chr [1:1000] "A PIE" "A PIE" "A PIE" "A PIE" ...
    ##   ..$ municipio         : chr [1:1000] "MEDELLÍN (CT)" "MEDELLÍN (CT)" "MEDELLÍN (CT)" "MEDELLÍN (CT)" ...
    ##   ..$ hora              : chr [1:1000] "13:40:00" "4:30:00" "14:50:00" "6:20:00" ...
    ##   ..$ d_a               : chr [1:1000] "Viernes" "Viernes" "Viernes" "Viernes" ...
    ##   ..$ estado_civil      : chr [1:1000] "UNION LIBRE" "DIVORCIADO" "SOLTERO" "UNION LIBRE" ...
    ##   ..$ profesi_n         : chr [1:1000] "-" "-" "-" "-" ...
    ##   ..$ edad              : chr [1:1000] "40" "39" "19" "34" ...
    ##   ..$ linea             : chr [1:1000] "GALAXY J" "KB770" "MOTO G" "GALAXY J" ...
    ##   ..$ arma_empleada     : chr [1:1000] "ARMA DE FUEGO" "ARMA BLANCA / CORTOPUNZANTE" "SIN EMPLEO DE ARMAS" "CONTUNDENTES" ...
    ##   ..$ clase             : chr [1:1000] "CELULAR" "CELULAR" "CELULAR" "CELULAR" ...
    ##   ..$ zona              : chr [1:1000] "URBANA" "URBANA" "URBANA" "URBANA" ...
    ##   ..$ escolaridad       : chr [1:1000] "SECUNDARIA" "SECUNDARIA" "SECUNDARIA" "TECNICO" ...
    ##   ..$ marca             : chr [1:1000] "SAMSUNG" "LG" "MOTOROLA" "SAMSUNG" ...
    ##   ..$ pa_s_de_nacimiento: chr [1:1000] "COLOMBIA" "COLOMBIA" "COLOMBIA" "COLOMBIA" ...
    ##   ..$ c_digo_dane       : chr [1:1000] "05001000" "05001000" "05001000" "05001000" ...
    ##   ..$ clase_de_sitio    : chr [1:1000] "VIAS PUBLICAS" "VIAS PUBLICAS" "VIAS PUBLICAS" "VIAS PUBLICAS" ...
    ##   ..$ departamento      : chr [1:1000] "ANTIOQUIA" "ANTIOQUIA" "ANTIOQUIA" "ANTIOQUIA" ...
    ##   ..$ clase_de_empleado : chr [1:1000] "EMPLEADO PARTICULAR" "EMPLEADO PARTICULAR" "DESEMPLEADO" "EMPLEADO PARTICULAR" ...
    ##   ..$ cantidad          : chr [1:1000] "1" "1" "1" "1" ...
    ##   ..$ sexo              : chr [1:1000] "MASCULINO" "MASCULINO" "MASCULINO" "MASCULINO" ...
    ##   ..$ fecha             : chr [1:1000] NA NA NA NA ...
    ##  $ :'data.frame':    1000 obs. of  23 variables:
    ##   ..$ m_vil_agresor     : chr [1:1000] "CONDUCTOR VEHICULO" "PASAJERO MOTOCICLETA" "PASAJERO MOTOCICLETA" "PASAJERO MOTOCICLETA" ...
    ##   ..$ barrio            : chr [1:1000] "BELLA ARENA" "EL LIMON" "EVARISTO SOURDIS" "EVARISTO SOURDIS" ...
    ##   ..$ m_vil_victima     : chr [1:1000] "PASAJERO VEHICULO" "A PIE" "A PIE" "A PIE" ...
    ##   ..$ municipio         : chr [1:1000] "BARRANQUILLA (CT)" "BARRANQUILLA (CT)" "SABANALARGA" "SABANALARGA" ...
    ##   ..$ hora              : chr [1:1000] "14:00:00" "6:00:00" "18:30:00" "18:30:00" ...
    ##   ..$ d_a               : chr [1:1000] "Miércoles" "Miércoles" "Miércoles" "Miércoles" ...
    ##   ..$ estado_civil      : chr [1:1000] "UNION LIBRE" "UNION LIBRE" "SOLTERO" "SOLTERO" ...
    ##   ..$ profesi_n         : chr [1:1000] "-" "-" "ADMINISTRADOR DE EMPRESAS" "ADMINISTRADOR DE EMPRESAS" ...
    ##   ..$ edad              : chr [1:1000] "46" "49" "39" "39" ...
    ##   ..$ linea             : chr [1:1000] "LINEA STANDARD" "LINEA STANDARD" "GALAXY J" "LINEA STANDARD" ...
    ##   ..$ arma_empleada     : chr [1:1000] "ARMA BLANCA / CORTOPUNZANTE" "ARMA DE FUEGO" "SIN EMPLEO DE ARMAS" "SIN EMPLEO DE ARMAS" ...
    ##   ..$ clase             : chr [1:1000] "CELULAR" "CELULAR" "CELULAR" "CELULAR" ...
    ##   ..$ fecha             : chr [1:1000] "2017-01-02T00:00:00" "2017-01-02T00:00:00" "2017-01-02T00:00:00" "2017-01-02T00:00:00" ...
    ##   ..$ zona              : chr [1:1000] "URBANA" "URBANA" "URBANA" "URBANA" ...
    ##   ..$ escolaridad       : chr [1:1000] "SECUNDARIA" "SECUNDARIA" "SUPERIOR" "SUPERIOR" ...
    ##   ..$ marca             : chr [1:1000] "SAMSUNG" "SAMSUNG" "SAMSUNG" "MOTOROLA" ...
    ##   ..$ pa_s_de_nacimiento: chr [1:1000] "COLOMBIA" "COLOMBIA" "COLOMBIA" "COLOMBIA" ...
    ##   ..$ c_digo_dane       : chr [1:1000] "08001000" "08001000" "08638000" "08638000" ...
    ##   ..$ clase_de_sitio    : chr [1:1000] "INTERIOR VEHICULO PARTICULAR" "VIAS PUBLICAS" "FRENTE A RESIDENCIAS - VIA PUBLICA" "FRENTE A RESIDENCIAS - VIA PUBLICA" ...
    ##   ..$ departamento      : chr [1:1000] "ATLÁNTICO" "ATLÁNTICO" "ATLÁNTICO" "ATLÁNTICO" ...
    ##   ..$ clase_de_empleado : chr [1:1000] "EMPLEADO PARTICULAR" "EMPLEADO PARTICULAR" "EMPLEADO PARTICULAR" "EMPLEADO PARTICULAR" ...
    ##   ..$ cantidad          : chr [1:1000] "1" "1" "1" "1" ...
    ##   ..$ sexo              : chr [1:1000] "MASCULINO" "MASCULINO" "FEMENINO" "FEMENINO" ...
    ##  $ :'data.frame':    1000 obs. of  23 variables:
    ##   ..$ m_vil_agresor     : chr [1:1000] "A PIE" "A PIE" "A PIE" "PASAJERO MOTOCICLETA" ...
    ##   ..$ barrio            : chr [1:1000] "LA MADERA" "GRAN AVENIDA" "ACEVEDO" "EL REMANZO" ...
    ##   ..$ m_vil_victima     : chr [1:1000] "A PIE" "A PIE" "A PIE" "A PIE" ...
    ##   ..$ municipio         : chr [1:1000] "BELLO" "BELLO" "BELLO" "COPACABANA" ...
    ##   ..$ hora              : chr [1:1000] "21:30:00" "6:20:00" "7:25:00" "5:50:00" ...
    ##   ..$ d_a               : chr [1:1000] "Lunes" "Lunes" "Lunes" "Lunes" ...
    ##   ..$ estado_civil      : chr [1:1000] "UNION LIBRE" "SOLTERO" "CASADO" "UNION LIBRE" ...
    ##   ..$ profesi_n         : chr [1:1000] "-" "-" "-" "-" ...
    ##   ..$ edad              : chr [1:1000] "28" "14" "39" "39" ...
    ##   ..$ linea             : chr [1:1000] "LINEA STANDARD" "C900 OPTIMUS 7Q" "LINEA STANDARD" "LINEA STANDARD" ...
    ##   ..$ arma_empleada     : chr [1:1000] "ARMA BLANCA / CORTOPUNZANTE" "ARMA BLANCA / CORTOPUNZANTE" "CONTUNDENTES" "ARMA DE FUEGO" ...
    ##   ..$ clase             : chr [1:1000] "CELULAR" "CELULAR" "CELULAR" "CELULAR" ...
    ##   ..$ fecha             : chr [1:1000] "2017-06-02T00:00:00" "2017-06-02T00:00:00" "2017-06-02T00:00:00" "2017-06-02T00:00:00" ...
    ##   ..$ zona              : chr [1:1000] "URBANA" "URBANA" "URBANA" "URBANA" ...
    ##   ..$ escolaridad       : chr [1:1000] "TECNICO" "SECUNDARIA" "SECUNDARIA" "SECUNDARIA" ...
    ##   ..$ marca             : chr [1:1000] "HUAWEI" "LG" "SAMSUNG" "LENOVO" ...
    ##   ..$ pa_s_de_nacimiento: chr [1:1000] "COLOMBIA" "COLOMBIA" "COLOMBIA" "COLOMBIA" ...
    ##   ..$ c_digo_dane       : chr [1:1000] "05088000" "05088000" "05088000" "05212000" ...
    ##   ..$ clase_de_sitio    : chr [1:1000] "VIAS PUBLICAS" "VIAS PUBLICAS" "VIAS PUBLICAS" "FRENTE A RESIDENCIAS - VIA PUBLICA" ...
    ##   ..$ departamento      : chr [1:1000] "ANTIOQUIA" "ANTIOQUIA" "ANTIOQUIA" "ANTIOQUIA" ...
    ##   ..$ clase_de_empleado : chr [1:1000] "EMPLEADO PARTICULAR" "EMPLEADO PARTICULAR" "EMPLEADO PARTICULAR" "EMPLEADO PARTICULAR" ...
    ##   ..$ cantidad          : chr [1:1000] "1" "1" "1" "1" ...
    ##   ..$ sexo              : chr [1:1000] "MASCULINO" "MASCULINO" "MASCULINO" "MASCULINO" ...
    ##  $ :'data.frame':    1000 obs. of  23 variables:
    ##   ..$ m_vil_agresor     : chr [1:1000] "PASAJERO MOTOCICLETA" "A PIE" "PASAJERO MOTOCICLETA" "PASAJERO MOTOCICLETA" ...
    ##   ..$ barrio            : chr [1:1000] "ARKANIZA" "SANTA HELENA" "LA CEIBA (SALADO)" "LA CEIBA (SALADO)" ...
    ##   ..$ m_vil_victima     : chr [1:1000] "A PIE" "CONDUCTOR VEHICULO" "A PIE" "A PIE" ...
    ##   ..$ municipio         : chr [1:1000] "IBAGUÉ (CT)" "IBAGUÉ (CT)" "IBAGUÉ (CT)" "IBAGUÉ (CT)" ...
    ##   ..$ hora              : chr [1:1000] "19:30:00" "11:45:00" "21:40:00" "21:40:00" ...
    ##   ..$ d_a               : chr [1:1000] "Viernes" "Viernes" "Viernes" "Viernes" ...
    ##   ..$ estado_civil      : chr [1:1000] "CASADO" "UNION LIBRE" "SOLTERO" "SOLTERO" ...
    ##   ..$ profesi_n         : chr [1:1000] "-" "ADMINISTRADOR DE EMPRESAS" "-" "-" ...
    ##   ..$ edad              : chr [1:1000] "39" "45" "21" "22" ...
    ##   ..$ linea             : chr [1:1000] "LINEA STANDARD" "GALAXY J" "U8800" "GALAXY A" ...
    ##   ..$ arma_empleada     : chr [1:1000] "ARMA DE FUEGO" "CONTUNDENTES" "ARMA DE FUEGO" "ARMA DE FUEGO" ...
    ##   ..$ clase             : chr [1:1000] "CELULAR" "CELULAR" "CELULAR" "CELULAR" ...
    ##   ..$ fecha             : chr [1:1000] "2017-10-02T00:00:00" "2017-10-02T00:00:00" "2017-10-02T00:00:00" "2017-10-02T00:00:00" ...
    ##   ..$ zona              : chr [1:1000] "URBANA" "URBANA" "URBANA" "URBANA" ...
    ##   ..$ escolaridad       : chr [1:1000] "SECUNDARIA" "SUPERIOR" "SECUNDARIA" "SECUNDARIA" ...
    ##   ..$ marca             : chr [1:1000] "HUAWEI" "SAMSUNG" "HUAWEI" "SAMSUNG" ...
    ##   ..$ pa_s_de_nacimiento: chr [1:1000] "COLOMBIA" "COLOMBIA" "COLOMBIA" "COLOMBIA" ...
    ##   ..$ c_digo_dane       : chr [1:1000] "73001000" "73001000" "73001000" "73001000" ...
    ##   ..$ clase_de_sitio    : chr [1:1000] "VIAS PUBLICAS" "VIAS PUBLICAS" "VIAS PUBLICAS" "VIAS PUBLICAS" ...
    ##   ..$ departamento      : chr [1:1000] "TOLIMA" "TOLIMA" "TOLIMA" "TOLIMA" ...
    ##   ..$ clase_de_empleado : chr [1:1000] "EMPLEADO PARTICULAR" "INDEPENDIENTE" "ESTUDIANTE" "ESTUDIANTE" ...
    ##   ..$ cantidad          : chr [1:1000] "1" "1" "1" "1" ...
    ##   ..$ sexo              : chr [1:1000] "FEMENINO" "FEMENINO" "FEMENINO" "MASCULINO" ...
    ##  $ :'data.frame':    1000 obs. of  22 variables:
    ##   ..$ m_vil_agresor     : chr [1:1000] "CONDUCTOR MOTOCICLETA" "A PIE" "A PIE" "A PIE" ...
    ##   ..$ barrio            : chr [1:1000] "LOS CAMBULOS E19" "SANTA ANITA E17" "LOS CAMBULOS E19" "UNICENTRO CALI E17" ...
    ##   ..$ m_vil_victima     : chr [1:1000] "CONDUCTOR VEHICULO" "CONDUCTOR MOTOCICLETA" "A PIE" "A PIE" ...
    ##   ..$ municipio         : chr [1:1000] "CALI (CT)" "CALI (CT)" "CALI (CT)" "CALI (CT)" ...
    ##   ..$ hora              : chr [1:1000] "21:15:00" "21:20:00" "16:40:00" "9:20:00" ...
    ##   ..$ d_a               : chr [1:1000] "Miércoles" "Miércoles" "Miércoles" "Miércoles" ...
    ##   ..$ estado_civil      : chr [1:1000] "UNION LIBRE" "SOLTERO" "SOLTERO" "SOLTERO" ...
    ##   ..$ profesi_n         : chr [1:1000] "-" "-" "-" "-" ...
    ##   ..$ edad              : chr [1:1000] "34" "28" "24" "23" ...
    ##   ..$ linea             : chr [1:1000] "GALAXY A" "LINEA STANDARD" "IPAD MINI" "GALAXY J" ...
    ##   ..$ arma_empleada     : chr [1:1000] "ARMA DE FUEGO" "SIN EMPLEO DE ARMAS" "SIN EMPLEO DE ARMAS" "ESCOPOLAMINA" ...
    ##   ..$ clase             : chr [1:1000] "CELULAR" "CELULAR" "CELULAR" "CELULAR" ...
    ##   ..$ zona              : chr [1:1000] "URBANA" "URBANA" "URBANA" "URBANA" ...
    ##   ..$ escolaridad       : chr [1:1000] "SECUNDARIA" "SECUNDARIA" "SECUNDARIA" "SECUNDARIA" ...
    ##   ..$ marca             : chr [1:1000] "SAMSUNG" "SAMSUNG" "APPLE" "SAMSUNG" ...
    ##   ..$ pa_s_de_nacimiento: chr [1:1000] "COLOMBIA" "COLOMBIA" "COLOMBIA" "COLOMBIA" ...
    ##   ..$ c_digo_dane       : chr [1:1000] "76001000" "76001000" "76001000" "76001000" ...
    ##   ..$ clase_de_sitio    : chr [1:1000] "FRENTE A RESIDENCIAS - VIA PUBLICA" "VIAS PUBLICAS" "VIAS PUBLICAS" "CENTRO COMERCIAL" ...
    ##   ..$ departamento      : chr [1:1000] "VALLE" "VALLE" "VALLE" "VALLE" ...
    ##   ..$ clase_de_empleado : chr [1:1000] "INDEPENDIENTE" "EMPLEADO PARTICULAR" "EMPLEADO PARTICULAR" "EMPLEADO PARTICULAR" ...
    ##   ..$ cantidad          : chr [1:1000] "1" "1" "1" "1" ...
    ##   ..$ sexo              : chr [1:1000] "FEMENINO" "FEMENINO" "FEMENINO" "FEMENINO" ...

Pero, para poder hacer análisis, queremos una base de datos única. Para
eso tenemos varias estrategias. La que encuentro más sencilla es usar
`do.call` para ir añadiendo recursivamente cada elemento de la lista a
la anterior.

    res <- do.call(plyr::rbind.fill, res) ## Algunas paginas tienen mas campos
    head(res)

    ##           m_vil_agresor         barrio      m_vil_victima    municipio
    ## 1                 A PIE         JARDIN              A PIE LETICIA (CT)
    ## 2                 A PIE VDA. SAN FELIX              A PIE        BELLO
    ## 3  PASAJERO MOTOCICLETA     SANTA RITA              A PIE        BELLO
    ## 4  PASAJERO MOTOCICLETA VDA. SAN FELIX CONDUCTOR VEHICULO        BELLO
    ## 5 CONDUCTOR MOTOCICLETA    LA ARBOLEDA              A PIE     CAUCASIA
    ## 6  PASAJERO MOTOCICLETA         ALCALA              A PIE     ENVIGADO
    ##       hora     d_a estado_civil profesi_n edad          linea
    ## 1  8:00:00 Domingo  UNION LIBRE         -   29           J700
    ## 2  0:00:00 Domingo      SOLTERO         -   33 LINEA STANDARD
    ## 3 21:45:00 Domingo      SOLTERO         -   22       GALAXY J
    ## 4 10:35:00 Domingo       CASADO         -   42             5S
    ## 5 16:00:00 Domingo      SOLTERO         -   51    A257 MAGNET
    ## 6 12:30:00 Domingo  UNION LIBRE         -   26          XT532
    ##                 arma_empleada   clase               fecha   zona
    ## 1                CONTUNDENTES CELULAR 2017-01-01T00:00:00 URBANA
    ## 2               ARMA DE FUEGO CELULAR 2017-01-01T00:00:00  RURAL
    ## 3               ARMA DE FUEGO CELULAR 2017-01-01T00:00:00 URBANA
    ## 4 ARMA BLANCA / CORTOPUNZANTE CELULAR 2017-01-01T00:00:00  RURAL
    ## 5               ARMA DE FUEGO CELULAR 2017-01-01T00:00:00 URBANA
    ## 6 ARMA BLANCA / CORTOPUNZANTE CELULAR 2017-01-01T00:00:00 URBANA
    ##   escolaridad    marca pa_s_de_nacimiento c_digo_dane      clase_de_sitio
    ## 1     TECNICO  SAMSUNG           COLOMBIA    91001000 CASAS DE HABITACION
    ## 2  SECUNDARIA   LENOVO           COLOMBIA    05088000       VIAS PUBLICAS
    ## 3  SECUNDARIA  SAMSUNG           COLOMBIA    05088000       VIAS PUBLICAS
    ## 4  SECUNDARIA   IPHONE           COLOMBIA    05088000       VIAS PUBLICAS
    ## 5  SECUNDARIA  SAMSUNG           COLOMBIA    05154000       VIAS PUBLICAS
    ## 6  SECUNDARIA MOTOROLA           COLOMBIA    05266000       VIAS PUBLICAS
    ##   departamento   clase_de_empleado cantidad      sexo
    ## 1     AMAZONAS       INDEPENDIENTE        1  FEMENINO
    ## 2    ANTIOQUIA EMPLEADO PARTICULAR        1 MASCULINO
    ## 3    ANTIOQUIA       INDEPENDIENTE        1 MASCULINO
    ## 4    ANTIOQUIA       INDEPENDIENTE        1 MASCULINO
    ## 5    ANTIOQUIA         COMERCIANTE        1  FEMENINO
    ## 6    ANTIOQUIA       INDEPENDIENTE        1 MASCULINO

Ahora tenemos ya una base de datos que podemos usar para responder
preguntas de investigación. Por ejemplo, podemos ver la distribución de
hurtos por departamento:

    table(res$departamento)

    ## 
    ##           AMAZONAS          ANTIOQUIA             ARAUCA 
    ##                 10               1599                 29 
    ##          ATLÁNTICO            BOLÍVAR             BOYACÁ 
    ##                354                 96                142 
    ##             CALDAS            CAQUETÁ           CASANARE 
    ##                241                105                 97 
    ##              CAUCA              CESAR              CHOCÓ 
    ##                206                222                 33 
    ##            CÓRDOBA       CUNDINAMARCA            GUAJIRA 
    ##                166               1740                139 
    ##           GUAVIARE              HUILA          MAGDALENA 
    ##                 10                277                211 
    ##               META             NARIÑO NORTE DE SANTANDER 
    ##                400                461                212 
    ##           PUTUMAYO            QUINDÍO          RISARALDA 
    ##                 28                229                317 
    ##         SAN ANDRÉS          SANTANDER              SUCRE 
    ##                 33                729                182 
    ##             TOLIMA              VALLE             VAUPÉS 
    ##                460               1264                  1 
    ##            VICHADA 
    ##                  7

o, más interesante, el arma usada durante el hurto:

    prop.table(table(res$arma_empleada, res$departamento), 2)

    ##                              
    ##                               AMAZONAS ANTIOQUIA   ARAUCA ATLÁNTICO
    ##   ARMA BLANCA / CORTOPUNZANTE 0.200000  0.297686 0.241379  0.257062
    ##   ARMA DE FUEGO               0.200000  0.269543 0.034483  0.426554
    ##   CONTUNDENTES                0.100000  0.066291 0.137931  0.042373
    ##   ESCOPOLAMINA                0.000000  0.016260 0.000000  0.005650
    ##   JERINGA                     0.000000  0.000000 0.000000  0.000000
    ##   LLAVE MAESTRA               0.000000  0.001876 0.000000  0.002825
    ##   PALANCAS                    0.000000  0.001876 0.000000  0.002825
    ##   PERRO                       0.000000  0.000625 0.000000  0.000000
    ##   SIN EMPLEO DE ARMAS         0.500000  0.345841 0.586207  0.262712
    ##                              
    ##                                BOLÍVAR   BOYACÁ   CALDAS  CAQUETÁ CASANARE
    ##   ARMA BLANCA / CORTOPUNZANTE 0.281250 0.204225 0.253112 0.238095 0.247423
    ##   ARMA DE FUEGO               0.302083 0.049296 0.070539 0.190476 0.134021
    ##   CONTUNDENTES                0.041667 0.176056 0.149378 0.057143 0.185567
    ##   ESCOPOLAMINA                0.000000 0.007042 0.029046 0.000000 0.000000
    ##   JERINGA                     0.000000 0.000000 0.000000 0.000000 0.010309
    ##   LLAVE MAESTRA               0.010417 0.000000 0.020747 0.000000 0.000000
    ##   PALANCAS                    0.000000 0.000000 0.000000 0.019048 0.010309
    ##   PERRO                       0.010417 0.000000 0.000000 0.000000 0.000000
    ##   SIN EMPLEO DE ARMAS         0.354167 0.563380 0.477178 0.495238 0.412371
    ##                              
    ##                                  CAUCA    CESAR    CHOCÓ  CÓRDOBA
    ##   ARMA BLANCA / CORTOPUNZANTE 0.305825 0.144144 0.333333 0.271084
    ##   ARMA DE FUEGO               0.199029 0.567568 0.151515 0.343373
    ##   CONTUNDENTES                0.038835 0.045045 0.060606 0.084337
    ##   ESCOPOLAMINA                0.004854 0.022523 0.000000 0.000000
    ##   JERINGA                     0.000000 0.000000 0.000000 0.000000
    ##   LLAVE MAESTRA               0.004854 0.018018 0.000000 0.000000
    ##   PALANCAS                    0.000000 0.000000 0.000000 0.000000
    ##   PERRO                       0.000000 0.000000 0.000000 0.000000
    ##   SIN EMPLEO DE ARMAS         0.446602 0.202703 0.454545 0.301205
    ##                              
    ##                               CUNDINAMARCA  GUAJIRA GUAVIARE    HUILA
    ##   ARMA BLANCA / CORTOPUNZANTE     0.402874 0.179856 0.100000 0.299639
    ##   ARMA DE FUEGO                   0.074138 0.561151 0.000000 0.205776
    ##   CONTUNDENTES                    0.062069 0.143885 0.000000 0.111913
    ##   ESCOPOLAMINA                    0.002299 0.000000 0.100000 0.010830
    ##   JERINGA                         0.000000 0.000000 0.000000 0.000000
    ##   LLAVE MAESTRA                   0.000000 0.007194 0.000000 0.014440
    ##   PALANCAS                        0.000575 0.014388 0.000000 0.000000
    ##   PERRO                           0.000575 0.000000 0.000000 0.000000
    ##   SIN EMPLEO DE ARMAS             0.457471 0.093525 0.800000 0.357401
    ##                              
    ##                               MAGDALENA     META   NARIÑO
    ##   ARMA BLANCA / CORTOPUNZANTE  0.251185 0.320000 0.420824
    ##   ARMA DE FUEGO                0.331754 0.170000 0.071584
    ##   CONTUNDENTES                 0.090047 0.180000 0.206074
    ##   ESCOPOLAMINA                 0.004739 0.002500 0.006508
    ##   JERINGA                      0.000000 0.000000 0.000000
    ##   LLAVE MAESTRA                0.000000 0.007500 0.000000
    ##   PALANCAS                     0.004739 0.000000 0.000000
    ##   PERRO                        0.000000 0.000000 0.000000
    ##   SIN EMPLEO DE ARMAS          0.317536 0.320000 0.295011
    ##                              
    ##                               NORTE DE SANTANDER PUTUMAYO  QUINDÍO
    ##   ARMA BLANCA / CORTOPUNZANTE           0.283019 0.035714 0.323144
    ##   ARMA DE FUEGO                         0.287736 0.178571 0.152838
    ##   CONTUNDENTES                          0.089623 0.142857 0.148472
    ##   ESCOPOLAMINA                          0.000000 0.000000 0.008734
    ##   JERINGA                               0.000000 0.000000 0.000000
    ##   LLAVE MAESTRA                         0.004717 0.000000 0.008734
    ##   PALANCAS                              0.000000 0.000000 0.013100
    ##   PERRO                                 0.000000 0.000000 0.000000
    ##   SIN EMPLEO DE ARMAS                   0.334906 0.642857 0.344978
    ##                              
    ##                               RISARALDA SAN ANDRÉS SANTANDER    SUCRE
    ##   ARMA BLANCA / CORTOPUNZANTE  0.353312   0.060606  0.436214 0.285714
    ##   ARMA DE FUEGO                0.261830   0.212121  0.165981 0.335165
    ##   CONTUNDENTES                 0.047319   0.000000  0.057613 0.087912
    ##   ESCOPOLAMINA                 0.012618   0.000000  0.002743 0.016484
    ##   JERINGA                      0.000000   0.000000  0.000000 0.000000
    ##   LLAVE MAESTRA                0.006309   0.000000  0.001372 0.000000
    ##   PALANCAS                     0.000000   0.030303  0.000000 0.005495
    ##   PERRO                        0.000000   0.000000  0.000000 0.000000
    ##   SIN EMPLEO DE ARMAS          0.318612   0.696970  0.336077 0.269231
    ##                              
    ##                                 TOLIMA    VALLE   VAUPÉS  VICHADA
    ##   ARMA BLANCA / CORTOPUNZANTE 0.328261 0.200158 0.000000 0.857143
    ##   ARMA DE FUEGO               0.167391 0.454114 0.000000 0.142857
    ##   CONTUNDENTES                0.071739 0.065665 0.000000 0.000000
    ##   ESCOPOLAMINA                0.013043 0.007911 0.000000 0.000000
    ##   JERINGA                     0.000000 0.000000 0.000000 0.000000
    ##   LLAVE MAESTRA               0.013043 0.002373 0.000000 0.000000
    ##   PALANCAS                    0.000000 0.001582 0.000000 0.000000
    ##   PERRO                       0.000000 0.000000 0.000000 0.000000
    ##   SIN EMPLEO DE ARMAS         0.406522 0.268196 1.000000 0.000000
