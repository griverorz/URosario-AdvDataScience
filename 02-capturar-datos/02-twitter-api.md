Twitter, así como muchas otras páginas, ofrece acceso a sus datos a
través de su API. Si miramos la documentación, veremos cómo acceder a
los *endpoints* directamente después de registrarnos. Sin embargo,
Twitter es ilustrativo de API alrededor de las cuales los
desarrolladores ofrecen paquetes que gestionan inteligentemente las
conexiones y los datos, pensando en los usos más frecuentes. Dicho de
otro modo, aunque sea posible acceder a la Twitter de API a través de
estructuras como las que hemos visto antes, es más práctico usar
paquetes como, por ejemplo `twitteR`.

    library(twitteR)

Antes de nada, tenemos que completar el registro de una nueva
aplicación. Para eso, nos dirigimos a la [página de
desarrolladores](https://dev.twitter.com/) asociada a nuestra cuenta y
obtenemos las credenciales. Como siempre, guardaremos las credenciales
en un archivo separado de nuestro código

    creds <- readLines("credentials-twitter.txt")
    consumer_key <- creds[1]
    consumer_secret <- creds[2]
    access_token <- creds[3]
    access_secret <- creds[4]

Ahora podemos conectar nuestra sesión a nuestra cuenta de Twitter:

    setup_twitter_oauth(consumer_key,
                        consumer_secret,
                        access_token,
                        access_secret)

    ## [1] "Using direct authentication"

Con eso, podemos acceder a varios tipos de funciones. Por ejemplo,
podemos acceder a un endpoint de la Search API que recupera tweets que
contienen los términos que pasamos en el argumento.

    tweets <- searchTwitter("travel ban", n=20)
    tweets[[1]]

    ## [1] "hankamobley: RT @SenFeinstein: This is now the sixth time that a federal court has found Trump’s travel ban is unconstitutional. I hope this puts an end…"

Aquí es donde vemos el valor de usar un paquete como `twitteR`. Si por
ejemplo pedimos más información sobre el objeto, vemos que en realidad
contiene más que simplemente el tweet que hemos recuperado. Al
inspeccionar el objeto vemos que en realidad contiene los datos
relativos al tweet, como cuándo fue escrito, por quién, a qué hora, ...

    str(tweets[[1]])

    ## Reference class 'status' [package "twitteR"] with 17 fields
    ##  $ text         : chr "RT @SenFeinstein: This is now the sixth time that a federal court has found Trump’s travel ban is unconstitutional. I hope this"| __truncated__
    ##  $ favorited    : logi FALSE
    ##  $ favoriteCount: num 0
    ##  $ replyToSN    : chr(0) 
    ##  $ created      : POSIXct[1:1], format: "2017-06-12 20:32:14"
    ##  $ truncated    : logi FALSE
    ##  $ replyToSID   : chr(0) 
    ##  $ id           : chr "874363738647691264"
    ##  $ replyToUID   : chr(0) 
    ##  $ statusSource : chr "<a href=\"http://twitter.com/download/android\" rel=\"nofollow\">Twitter for Android</a>"
    ##  $ screenName   : chr "hankamobley"
    ##  $ retweetCount : num 397
    ##  $ isRetweet    : logi TRUE
    ##  $ retweeted    : logi FALSE
    ##  $ longitude    : chr(0) 
    ##  $ latitude     : chr(0) 
    ##  $ urls         :'data.frame':   0 obs. of  4 variables:
    ##   ..$ url         : chr(0) 
    ##   ..$ expanded_url: chr(0) 
    ##   ..$ dispaly_url : chr(0) 
    ##   ..$ indices     : num(0) 
    ##  and 53 methods, of which 39 are  possibly relevant:
    ##    getCreated, getFavoriteCount, getFavorited, getId, getIsRetweet,
    ##    getLatitude, getLongitude, getReplyToSID, getReplyToSN, getReplyToUID,
    ##    getRetweetCount, getRetweeted, getRetweeters, getRetweets,
    ##    getScreenName, getStatusSource, getText, getTruncated, getUrls,
    ##    initialize, setCreated, setFavoriteCount, setFavorited, setId,
    ##    setIsRetweet, setLatitude, setLongitude, setReplyToSID, setReplyToSN,
    ##    setReplyToUID, setRetweetCount, setRetweeted, setScreenName,
    ##    setStatusSource, setText, setTruncated, setUrls, toDataFrame,
    ##    toDataFrame#twitterObj

No solo eso, también nos ofrece un grupo de funciones para interactuar
con esa información sin tener que buscar a través de la lista de datos.
Por ejemplo, nos permite acceder al número de gente que ha hecho
*favorite* a este tweet, la identidad de los usuarios que lo han
retuiteado, o a través de qué plataforma se ha emitido el tweet. Es
importante tener en cuenta la peculiar forma de acceder a esta
información, como método aplicado a cada objeto de la lista:

    tweets[[1]]$getFavoriteCount()

    ## [1] 0

    tweets[[1]]$statusSource

    ## [1] "<a href=\"http://twitter.com/download/android\" rel=\"nofollow\">Twitter for Android</a>"

También tenemos funciones especializadas en capturar información acerca
de usuarios:

    myself <- getUser("griverorz")
    myself

    ## [1] "griverorz"

    str(myself)

    ## Reference class 'user' [package "twitteR"] with 18 fields
    ##  $ description      : chr "Social scientist | Statistician | Jack of all trades"
    ##  $ statusesCount    : num 10945
    ##  $ followersCount   : num 2309
    ##  $ favoritesCount   : num 5297
    ##  $ friendsCount     : num 431
    ##  $ url              : chr "https://t.co/owUdgQOsNc"
    ##  $ name             : chr "Gonzalo Rivero"
    ##  $ created          : POSIXct[1:1], format: "2010-06-26 13:53:12"
    ##  $ protected        : logi FALSE
    ##  $ verified         : logi FALSE
    ##  $ screenName       : chr "griverorz"
    ##  $ location         : chr "Washington DC"
    ##  $ lang             : chr "en"
    ##  $ id               : chr "159849348"
    ##  $ lastStatus       :Reference class 'status' [package "twitteR"] with 17 fields
    ##   ..$ text         : chr "RT @BigDataCO_UR: Gonzalo Rivero de @westat en @BigDataCO_UR sobre trabajo con encuestas, modelo de investigación y retos actua"| __truncated__
    ##   ..$ favorited    : logi FALSE
    ##   ..$ favoriteCount: num 0
    ##   ..$ replyToSN    : chr(0) 
    ##   ..$ created      : POSIXct[1:1], format: "2017-06-09 18:49:09"
    ##   ..$ truncated    : logi FALSE
    ##   ..$ replyToSID   : chr(0) 
    ##   ..$ id           : chr "873250631108132865"
    ##   ..$ replyToUID   : chr(0) 
    ##   ..$ statusSource : chr "<a href=\"http://twitter.com/download/iphone\" rel=\"nofollow\">Twitter for iPhone</a>"
    ##   ..$ screenName   : chr "Unknown"
    ##   ..$ retweetCount : num 3
    ##   ..$ isRetweet    : logi TRUE
    ##   ..$ retweeted    : logi TRUE
    ##   ..$ longitude    : chr(0) 
    ##   ..$ latitude     : chr(0) 
    ##   ..$ urls         :'data.frame':    0 obs. of  4 variables:
    ##   .. ..$ url         : chr(0) 
    ##   .. ..$ expanded_url: chr(0) 
    ##   .. ..$ dispaly_url : chr(0) 
    ##   .. ..$ indices     : num(0) 
    ##   ..and 53 methods, of which 39 are  possibly relevant:
    ##   ..  getCreated, getFavoriteCount, getFavorited, getId, getIsRetweet,
    ##   ..  getLatitude, getLongitude, getReplyToSID, getReplyToSN,
    ##   ..  getReplyToUID, getRetweetCount, getRetweeted, getRetweeters,
    ##   ..  getRetweets, getScreenName, getStatusSource, getText, getTruncated,
    ##   ..  getUrls, initialize, setCreated, setFavoriteCount, setFavorited,
    ##   ..  setId, setIsRetweet, setLatitude, setLongitude, setReplyToSID,
    ##   ..  setReplyToSN, setReplyToUID, setRetweetCount, setRetweeted,
    ##   ..  setScreenName, setStatusSource, setText, setTruncated, setUrls,
    ##   ..  toDataFrame, toDataFrame#twitterObj
    ##  $ listedCount      : num 196
    ##  $ followRequestSent: logi FALSE
    ##  $ profileImageUrl  : chr "http://pbs.twimg.com/profile_images/872125951806779393/NkcasGkc_normal.jpg"
    ##  and 59 methods, of which 45 are  possibly relevant:
    ##    getCreated, getDescription, getFavorites, getFavoritesCount,
    ##    getFavouritesCount, getFollowerIDs, getFollowers, getFollowersCount,
    ##    getFollowRequestSent, getFriendIDs, getFriends, getFriendsCount, getId,
    ##    getLang, getLastStatus, getListedCount, getLocation, getName,
    ##    getProfileImageUrl, getProtected, getScreenName, getStatusesCount,
    ##    getUrl, getVerified, initialize, setCreated, setDescription,
    ##    setFavoritesCount, setFollowersCount, setFollowRequestSent,
    ##    setFriendsCount, setId, setLang, setLastStatus, setListedCount,
    ##    setLocation, setName, setProfileImageUrl, setProtected, setScreenName,
    ##    setStatusesCount, setUrl, setVerified, toDataFrame,
    ##    toDataFrame#twitterObj

que nos ofrecen también la posibilidad de acceder a información
adicional sobre ese tipo de objeto

    myself$getDescription()

    ## [1] "Social scientist | Statistician | Jack of all trades"

Lo más interesante es ver cómo es posible "encadenar" este tipo de
funciones. Por ejemplo, podemos recuperar quiénes son mis seguidores en
Twitter

    followers <- myself$getFollowerIDs()
    head(followers)

    ## [1] "265975277"          "284241195"          "2254779672"        
    ## [4] "779313898860208128" "108708222"          "2775054035"

y ver que el nuevo objeto `followers` nos permite acceder a información
de cada uno de ellos como usuario

    getUser(followers[1234])

    ## [1] "ygnazyo"

lo cual nos da la posibilidad de reconstruir la red de mis seguidores y
sus seguidores. En la próxima sesión veremos las herramientas necesarias
para llevar a cabo este tipo de análisis.

Finalmente, `twitteR` también nos da acceso a los *timelines* a través
de `userTimeline` con la posibilidad de controlar cuántos tweets
recuperar, si incluir retweets, o *replies*.

    mytm <- userTimeline("griverorz")
    length(mytm)

    ## [1] 10

    mytm[[1]]

    ## [1] "griverorz: @p_beramendi @jfalbertos @marioberamendi Noraboa!"

Y también nos permite interactuar con la lista de trending topics, para
lo cual tenemos que seleccionar la geografía en la que estamos
interesados

    head(availableTrendLocations())

    ##        name country woeid
    ## 1 Worldwide             1
    ## 2  Winnipeg  Canada  2972
    ## 3    Ottawa  Canada  3369
    ## 4    Quebec  Canada  3444
    ## 5  Montreal  Canada  3534
    ## 6   Toronto  Canada  4118

    head(getTrends(368148)) ## Bogotá

    ##                          name
    ## 1                   #VibraTyM
    ## 2                      Millos
    ## 3 #VacacionesRockRadioacktiva
    ## 4                    Medellín
    ## 5                    Santa Fe
    ## 6              Santiago Uribe
    ##                                                         url
    ## 1                   http://twitter.com/search?q=%23VibraTyM
    ## 2                        http://twitter.com/search?q=Millos
    ## 3 http://twitter.com/search?q=%23VacacionesRockRadioacktiva
    ## 4                 http://twitter.com/search?q=Medell%C3%ADn
    ## 5                http://twitter.com/search?q=%22Santa+Fe%22
    ## 6          http://twitter.com/search?q=%22Santiago+Uribe%22
    ##                           query  woeid
    ## 1                   %23VibraTyM 368148
    ## 2                        Millos 368148
    ## 3 %23VacacionesRockRadioacktiva 368148
    ## 4                 Medell%C3%ADn 368148
    ## 5                %22Santa+Fe%22 368148
    ## 6          %22Santiago+Uribe%22 368148
