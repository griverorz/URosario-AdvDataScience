Tokenización
------------

En esta sección veremos tareas sencillas de reconocimiento de entidades.
Para ello usaremos tres librerías nuevas, una de las cuales (`openNLP`)
es una interfaz a una librería en Java. Usaremos además modelos
pre-entrenados que no están disponibles en CRAN (depbido debido a la
política del repositorio):

    library(NLP)
    library(openNLP)

    # install.packages("openNLPmodels.en",
    #                  repos="http://datacube.wu.ac.at/",
    #                  type="source")

Vamos a hacer una tarea sencilla de separar un texto en oraciones y en
palabras. En concreto empezaremos con uno de los textos que estudiaremos
en la siguiente sección

    text <- readLines("./dta/senate-releases/Biden/01Mar2006Biden9.txt")

    ## Warning in readLines("./dta/senate-releases/Biden/01Mar2006Biden9.txt"):
    ## incomplete final line found on './dta/senate-releases/Biden/
    ## 01Mar2006Biden9.txt'

y lo transformaremos en un formato más adecuado para trabajar con `NLP`:

    text <- as.String(text)

Las funciones en `openNLP` funcionan siempre del mismo modo. En primer
lugar, inicializamos un *anotador* y después aplicamos ese anotador a un
texto. Por ejemplo, podemos empezar por anotar palabras y oraciones
utilizando las funciones:

    word_ann <- Maxent_Word_Token_Annotator()
    sent_ann <- Maxent_Sent_Token_Annotator()

y a continuación anotamos el texto aplicando estos dos objetos al texto
que queremos analizar:

    annotated_text <- annotate(text, list(sent_ann, word_ann))

Lo que obtenemos es un objeto de clase

    class(annotated_text)

    ## [1] "Annotation" "Span"

que en realidad se parece mucho a un `data.frame` en el que tenemos la
lista de oraciones y palabras identificadas por la posición en la que
empiezan y en la que acaban. Podemos, por ejemplo, recuperar el total de
oraciones:

    head(subset(annotated_text, type=="sentence"))

    ##  id type     start end features
    ##   1 sentence    15 215 constituents=<<integer,29>>
    ##   2 sentence   217 451 constituents=<<integer,40>>
    ##   3 sentence   453 589 constituents=<<integer,23>>
    ##   4 sentence   591 635 constituents=<<integer,10>>
    ##   5 sentence   637 820 constituents=<<integer,30>>
    ##   6 sentence   822 858 constituents=<<integer,8>>

y podemos comprobar el contenido de esta oración

    subset(annotated_text, type=='sentence')[[4]]$features

    ## [[1]]
    ## [[1]]$constituents
    ##  [1] 117 118 119 120 121 122 123 124 125 126

    text[subset(annotated_text, type=='sentence')[[4]]]

    ## The White House has come around to this view.

También podríamos recuperar las palabras que constituyen el texto de
modo similar

    head(subset(annotated_text, type=="word"))

    ##  id type start end features
    ##  25 word    15  20 
    ##  26 word    22  25 
    ##  27 word    27  33 
    ##  28 word    35  42 
    ##  29 word    57  61 
    ##  30 word    63  63

Quizás sea más fácil explorar los resultados utilizando la función
`AnnotatedPlainTextDocument`:

    adoc <- AnnotatedPlainTextDocument(text, annotated_text)
    head(sents(adoc))

    ## [[1]]
    ##  [1] "Rushed"            "deal"              "demands"          
    ##  [4] "scrutiny"          "March"             "1"                
    ##  [7] ","                 "2006"              "This"             
    ## [10] "op-ed"             "originally"        "appeared"         
    ## [13] "in"                "THE"               "NEWS"             
    ## [16] "JOURNAL"           "on"                "March"            
    ## [19] "1"                 ","                 "2006.Rushed"      
    ## [22] "deal"              "demands"           "scrutinyDubai"    
    ## [25] "Ports"             "takeover"          "has"              
    ## [28] "vulnerabilitiesBy" "SEN."             
    ## 
    ## [[2]]
    ##  [1] "JOE"            "BIDENThe"       "legitimate"     "outcry"        
    ##  [5] "over"           "the"            "proposed"       "takeover"      
    ##  [9] "of"             "six"            "American"       "ports"         
    ## [13] "by"             "Dubai"          "Ports"          "World"         
    ## [17] ","              "a"              "company"        "controlled"    
    ## [21] "by"             "the"            "United"         "Arab"          
    ## [25] "Emirates"       ","              "was"            "dismissed"     
    ## [29] "in"             "a"              "News"           "Journal"       
    ## [33] "editorial"      "as"             "unjustified"    "\""            
    ## [37] "chest-thumping" "jingoism"       "."              "\""            
    ## 
    ## [[3]]
    ##  [1] "I"          "disagree"   ".Members"   "of"         "Congress"  
    ##  [6] "from"       "both"       "parties"    "reacted"    "because"   
    ## [11] "this"       "deal"       ","          "approved"   "alarmingly"
    ## [16] "fast"       ","          "raises"     "some"       "real"      
    ## [21] "security"   "concerns"   "."         
    ## 
    ## [[4]]
    ##  [1] "The"    "White"  "House"  "has"    "come"   "around" "to"    
    ##  [8] "this"   "view"   "."     
    ## 
    ## [[5]]
    ##  [1] "It"           "convinced"    "Dubai"        "Ports"       
    ##  [5] "World"        "to"           "submit"       "to"          
    ##  [9] "a"            "full"         "assessment"   "of"          
    ## [13] "the"          "security"     "implications" "of"          
    ## [17] "the"          "proposed"     "buyout.Many"  "people"      
    ## [21] "argue"        "that"         "questioning"  "this"        
    ## [25] "deal"         "is"           "bigotry"      "against"     
    ## [29] "Arabs"        "."           
    ## 
    ## [[6]]
    ## [1] "But"     "not"     "all"     "allies"  "are"     "created" "equal"  
    ## [8] "."

    head(words(adoc))

    ## [1] "Rushed"   "deal"     "demands"  "scrutiny" "March"    "1"

Anotador de entidades y POS
---------------------------

Podemos utilizar el mismo proceso para anotar entidades dentro de un
texto. Por ejemplo, podemos anotar personas, localizaciones,
organizaciones y fechas dentro del documento que ya hemos anotado
anteriormente.

    person_ann <- Maxent_Entity_Annotator(kind="person")
    loc_ann <- Maxent_Entity_Annotator(kind="location")
    org_ann <- Maxent_Entity_Annotator(kind="organization")
    date_ann <- Maxent_Entity_Annotator(kind="date")

y ahora aplicamos estos nuevos anotadores al texto que ya tokenizamos
previamente:

    annotated_text <- annotate(text,
                              list(person_ann, loc_ann, org_ann, date_ann),
                              annotated_text)

podemos ver que, a la lista de tokens, se han añadido las entidades que
el clasificador ha logrado identificar.

    subset(annotated_text, type=="entity")$features

    ## [[1]]
    ## [[1]]$kind
    ## [1] "person"
    ## 
    ## 
    ## [[2]]
    ## [[2]]$kind
    ## [1] "person"
    ## 
    ## 
    ## [[3]]
    ## [[3]]$kind
    ## [1] "person"
    ## 
    ## 
    ## [[4]]
    ## [[4]]$kind
    ## [1] "location"
    ## 
    ## 
    ## [[5]]
    ## [[5]]$kind
    ## [1] "location"
    ## 
    ## 
    ## [[6]]
    ## [[6]]$kind
    ## [1] "location"
    ## 
    ## 
    ## [[7]]
    ## [[7]]$kind
    ## [1] "location"
    ## 
    ## 
    ## [[8]]
    ## [[8]]$kind
    ## [1] "location"
    ## 
    ## 
    ## [[9]]
    ## [[9]]$kind
    ## [1] "location"
    ## 
    ## 
    ## [[10]]
    ## [[10]]$kind
    ## [1] "location"
    ## 
    ## 
    ## [[11]]
    ## [[11]]$kind
    ## [1] "location"
    ## 
    ## 
    ## [[12]]
    ## [[12]]$kind
    ## [1] "location"
    ## 
    ## 
    ## [[13]]
    ## [[13]]$kind
    ## [1] "location"
    ## 
    ## 
    ## [[14]]
    ## [[14]]$kind
    ## [1] "location"
    ## 
    ## 
    ## [[15]]
    ## [[15]]$kind
    ## [1] "organization"
    ## 
    ## 
    ## [[16]]
    ## [[16]]$kind
    ## [1] "organization"
    ## 
    ## 
    ## [[17]]
    ## [[17]]$kind
    ## [1] "organization"
    ## 
    ## 
    ## [[18]]
    ## [[18]]$kind
    ## [1] "organization"
    ## 
    ## 
    ## [[19]]
    ## [[19]]$kind
    ## [1] "organization"
    ## 
    ## 
    ## [[20]]
    ## [[20]]$kind
    ## [1] "organization"
    ## 
    ## 
    ## [[21]]
    ## [[21]]$kind
    ## [1] "organization"
    ## 
    ## 
    ## [[22]]
    ## [[22]]$kind
    ## [1] "organization"
    ## 
    ## 
    ## [[23]]
    ## [[23]]$kind
    ## [1] "organization"
    ## 
    ## 
    ## [[24]]
    ## [[24]]$kind
    ## [1] "organization"
    ## 
    ## 
    ## [[25]]
    ## [[25]]$kind
    ## [1] "organization"
    ## 
    ## 
    ## [[26]]
    ## [[26]]$kind
    ## [1] "organization"
    ## 
    ## 
    ## [[27]]
    ## [[27]]$kind
    ## [1] "organization"
    ## 
    ## 
    ## [[28]]
    ## [[28]]$kind
    ## [1] "date"
    ## 
    ## 
    ## [[29]]
    ## [[29]]$kind
    ## [1] "date"

y ahora podemos ver las entidades que hemos recuperado

    sel <- subset(annotated_text, type=="entity")
    cbind(unlist(sel$features), text[sel])

    ##      [,1]           [,2]                              
    ## kind "person"       "Bush"                            
    ## kind "person"       "U.S."                            
    ## kind "person"       "Joseph Biden"                    
    ## kind "location"     "United States"                   
    ## kind "location"     "United States"                   
    ## kind "location"     "Poland"                          
    ## kind "location"     "Saudi Arabia"                    
    ## kind "location"     "Pakistan"                        
    ## kind "location"     "United Arab"                     
    ## kind "location"     "New York"                        
    ## kind "location"     "Miami"                           
    ## kind "location"     "United States"                   
    ## kind "location"     "Hong Kong"                       
    ## kind "location"     "Delaware"                        
    ## kind "organization" "Congress"                        
    ## kind "organization" "White House"                     
    ## kind "organization" "NATO"                            
    ## kind "organization" "Great Britain"                   
    ## kind "organization" "United Arab Emirates"            
    ## kind "organization" "Great Britain"                   
    ## kind "organization" "Congress"                        
    ## kind "organization" "Coast Guard"                     
    ## kind "organization" "Maritime Transportation Security"
    ## kind "organization" "Coast"                           
    ## kind "organization" "Guard"                           
    ## kind "organization" "Coast Guard"                     
    ## kind "organization" "Customs"                         
    ## kind "date"         "March 1"                         
    ## kind "date"         "March 1"

La anotación de POS sigue la misma lógica. Ahora generaremos un nuevo
objeto en el que inicializamos el anotador de POS y aplicamos este nuevo
anotador a nuestro texto:

    pos_ann <- Maxent_POS_Tag_Annotator()
    pos_annotated_text <- annotate(text, pos_ann, annotated_text)

Ahora lo que tenemos en la columna de `features` es una indiación de la
POS de cada una de las palabras

    head(subset(pos_annotated_text, type=="word"))

    ##  id type start end features
    ##  25 word    15  20 POS=JJ
    ##  26 word    22  25 POS=NN
    ##  27 word    27  33 POS=NNS
    ##  28 word    35  42 POS=NN
    ##  29 word    57  61 POS=NNP
    ##  30 word    63  63 POS=CD

En este caso vemos que, por ejemplo,

    text[subset(pos_annotated_text, type=="word")[[1]]]

    ## Rushed

está etiquetado como `JJ` que se corresponde como adjetivo y

    text[subset(pos_annotated_text, type=="word")[[2]]]

    ## deal

está etiquetado como `NN` que es un sustantivo en singular. La lista
completa de abreviaturas es:

<table>
<tr>
<th>
Símbolo
</th>
<th>
Significado
</th>
</tr>
<tr>
<td>
CC
</td>
<td>
Coordinating conjunction
</td>
</tr>
<tr>
<td>
CD
</td>
<td>
Cardinal number
</td>
</tr>
<tr>
<td>
DT
</td>
<td>
Determiner
</td>
</tr>
<tr>
<td>
EX
</td>
<td>
Existential there
</td>
</tr>
<tr>
<td>
FW
</td>
<td>
Foreign word
</td>
</tr>
<tr>
<td>
IN
</td>
<td>
Preposition or subordinating conjunction
</td>
</tr>
<tr>
<td>
JJ
</td>
<td>
Adjective
</td>
</tr>
<tr>
<td>
JJR
</td>
<td>
Adjective, comparative
</td>
</tr>
<tr>
<td>
JJS
</td>
<td>
Adjective, superlative
</td>
</tr>
<tr>
<td>
LS
</td>
<td>
List item marker
</td>
</tr>
<tr>
<td>
MD
</td>
<td>
Modal
</td>
</tr>
<tr>
<td>
NN
</td>
<td>
Noun, singular or mass
</td>
</tr>
<tr>
<td>
NNS
</td>
<td>
Noun, plural
</td>
</tr>
<tr>
<td>
NNP
</td>
<td>
Proper noun, singular
</td>
</tr>
<tr>
<td>
NNPS
</td>
<td>
Proper noun, plural
</td>
</tr>
<tr>
<td>
PDT
</td>
<td>
Predeterminer
</td>
</tr>
<tr>
<td>
POS
</td>
<td>
Possessive ending
</td>
</tr>
<tr>
<td>
PRP
</td>
<td>
Personal pronoun
</td>
</tr>
<tr>
<td>
PRP$
</td>
<td>
Possessive pronoun
</td>
</tr>
<tr>
<td>
RB
</td>
<td>
Adverb
</td>
</tr>
<tr>
<td>
RBR
</td>
<td>
Adverb, comparative
</td>
</tr>
<tr>
<td>
RBS
</td>
<td>
Adverb, superlative
</td>
</tr>
<tr>
<td>
RP
</td>
<td>
Particle
</td>
</tr>
<tr>
<td>
SYM
</td>
<td>
Symbol
</td>
</tr>
<tr>
<td>
TO
</td>
<td>
to
</td>
</tr>
<tr>
<td>
UH
</td>
<td>
Interjection
</td>
</tr>
<tr>
<td>
VB
</td>
<td>
Verb, base form
</td>
</tr>
<tr>
<td>
VBD
</td>
<td>
Verb, past tense
</td>
</tr>
<tr>
<td>
VBG
</td>
<td>
Verb, gerund or present participle
</td>
</tr>
<tr>
<td>
VBN
</td>
<td>
Verb, past participle
</td>
</tr>
<tr>
<td>
VBP
</td>
<td>
Verb, non­3rd person singular present
</td>
</tr>
<tr>
<td>
VBZ
</td>
<td>
Verb, 3rd person singular present
</td>
</tr>
<tr>
<td>
WDT
</td>
<td>
Wh­determiner
</td>
</tr>
<tr>
<td>
WP
</td>
<td>
Wh­pronoun
</td>
</tr>
<tr>
<td>
WP$
</td>
<td>
Possessive wh­pronoun
</td>
</tr>
<tr>
<td>
WRB
</td>
<td>
Wh­adverb
</td>
</tr>
</table>
Hemos visto que las etiquetaciones son en realidad el resultado de un
modelo estadístico. Podemos pedirle al anotador que nos devuelva las
probabilidades asociadas a la etiqueta escogida con el fin de tener
información acerca de la incertidumbre.

    pos_ann <- Maxent_POS_Tag_Annotator(probs=TRUE)
    pos_annotated_text <- annotate(text, pos_ann, annotated_text)
    head(subset(pos_annotated_text, type=="word"))

    ##  id type start end features
    ##  25 word    15  20 POS=JJ, POS_prob=0.228
    ##  26 word    22  25 POS=NN, POS_prob=0.881
    ##  27 word    27  33 POS=NNS, POS_prob=0.836
    ##  28 word    35  42 POS=NN, POS_prob=0.563
    ##  29 word    57  61 POS=NNP, POS_prob=0.941
    ##  30 word    63  63 POS=CD, POS_prob=0.989

y así comprobamos que la asignación de *rushed* como adjetivo en
realidad tiene una enorme incertidumbre.
