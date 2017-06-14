Hemos visto antes cómo capturar datos de Twitter a traves de su REST
API. Otra opción es capturar datos *en tiempo real* con su Streaming
API. Capturar datos en streaming requiere un instrumental especial para
poder mantener una conexión abierta y capturar datos a medida que llegan
a la plataforma. La libraría `streamR` simplifica esta tarea.

    library(ROAuth)
    library(streamR)

    ## Loading required package: RCurl

    ## Loading required package: bitops

    ## Loading required package: rjson

El sistemde autorización es un poco diferente al que vimos en el caso de
la REST API. En primer lugar, definimos los valores que deberemos pasar
a la función de autorización del paquete `ROauth`:

    creds <- readLines("./credentials-twitter.txt")
    requestURL <- "https://api.twitter.com/oauth/request_token"
    accessURL <- "https://api.twitter.com/oauth/access_token"
    authURL <- "https://api.twitter.com/oauth/authorize"
    consumerKey <- creds[1]
    consumerSecret <- creds[2]

y ahora creamos un objeto que contenga las credenciales

    auth <- OAuthFactory$new(consumerKey=consumerKey,
                             consumerSecret=consumerSecret,
                             requestURL=requestURL,
                             accessURL=accessURL,
                             authURL=authURL)
    auth$handshake(cainfo=system.file("CurlSSL",
                                      "cacert.pem",
                                      package="RCurl"))

Con esto, podemos hacer llamadas al stream de datos. Es importante
recordar que lo que estamos haciendo es mantener una conexión abierta.
La función `filterStream` nos da varias opciones para definir cuándo
cerrar la conexión.

Podemos decidir cuánto tiempo estaremos conectados

    filterStream(file="tweets_trump.json",
                 track="trump",
                 timeout=60,
                 oauth=auth)

La función guarda un archivo a disco y ahora podemos leerlo de vuelta:

    tw <- readLines("tweets_trump.json")
    tw[[1]]

Como vemos, se trata de objetos JSON iguales que los he hemos usado
cuando aprendíamos a interaccionar con una REST API. Podemos ahora
convertir estas cadenas a una `list` igual que antes:

    fromJSON(tw[[1]])

y podríamos convertir todos los datos que hemos recogido

    jsontw <- lapply(tw, fromJSON)

Sin embargo, el paquete provee de una función para simplificar esta
tarea

    head(parseTweets("tweets_trump.json"))

También podemos seleccionar cuántos tweets queremos en total:

    filterStream(file.name="tweets_trump.json",
                 track="trump",
                 tweets=10,
                 oauth=auth)

o incluso definir un área geográfica para tuits geolocalizados

    filterStream(file.name="",
                 language="es",
                 locations=c(-74,40,-73,41),
                 timeout=60,
                 oauth=auth)

Otra opción que nos ofrece `streamR` es una conexión a una muestra
aleatoria de todos los tweets que se producen.

    sampleStream("tweetsSample.json", timeout=60, oauth=auth, verbose=FALSE)
    tweets.df <- parseTweets("tweetsSample.json", verbose=FALSE)
    head(tweets.df$friends_count)
