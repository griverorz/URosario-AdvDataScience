_Big data_ para los sectores público y privado (Primera parte)
=================================================

Objetivos
-----

Durante los últimos años hemos sido testigos de la expansión de nuevas
herramientas computacionales que han cambiando el panorama en todas las áreas de
investigación social. Estas herramientas nos permiten analizar nuevos tipos de
datos (como archivos de texto, imágenes, o redes sociales), explotar bases de
datos masivas, o enfrentar problemas de predicción con una gran precisión. Con
ello, nos ofrecen la posibilidad de adentrarnos en temas de investigación que
hasta ahora no eran factibles.

En esta primera mitad del curso _Big Data para los sectores público y privado_
nos centraremos en la captura y el análisis de nuevas formas de información.
Estudiaremos, por ejemplo, como obtener datos de Internet, cómo identificar a
los actores más relevantes en una red social, cómo obtener información de una
gran colección de archivos de texto, cómo sintetizar información cuantitativa y
cómo fusionar bases de datos. La segunda mitad del curso se centrará en modelos
predictivos.

Prerrequisitos y estructura de la clase
---------------------------------

Durante la primera mitad de cada sesión, cubriremos las intuiciones detrás de la
teoría y en la segunda mitad aplicaremos esos conceptos a bases de datos y
problemas reales.

Para poder seguir la clase, es necesario tener cierta exposición al análisis
estadístico. En concreto, para aprovechar correctamente los contenidos de cada
sesión deberás familiarizado con los fundamentos de la probabilidad y la
estadística inferencial. Además, debes tener cierta soltura con modelos de
regresión y clasificación como la regresión logística.

El lenguaje de programación `R` será nuestra principal herramienta de trabajo.
En la primera clase repasaremos los fundamentos del lenguaje. Si no tienes
experiencia previa con programación, ni siquiera con un lenguaje estadístico
como Stata o SAS, probablemente querrás complementar esta clase con algunos
materiales adicionales antes de la segunda sesión. Ponte en contacto conmigo y
te puedo recomendar algunas lecturas.

Evaluación
--------

El curso completo _Big Data para los sectores público y privado_ será evaluado
mediante cuatro pruebas prácticas (dos en cada una de las mitades) y un
ejercicio de investigación. 

Contacto
------

Durante los días del curso estaré disponible para tutorías entre 10:30 y 12:00
en la oficina 3-15 Facultad de Economía. Fuera de esos horarios mantendré una
política de puerta abierta. Si por cualquier motivo esas dos opciones no fuesen
suficientes, ponte en contacto conmigo por correo electrónico. 

Bibliografía
----------
-   James, G., Witten, D, Hastie, T. y Tibshirani, R. (2013): _An Introduction
    to Statistical Learning._ Springer. 
-   Tilton, L. y Arnold, T. (2015): _Humanities Data in `R`._ Springer. 
-   de Bruin, J. (2015): _Probabilistic Record Linkage with the Fellegi
    and Sunter Framework._ MSc Dissertation. Delft University of Technology. 
-   Borgatti, S. y Everett, M. (2013): _Analyzing Social Networks._ SAGE
    Publishing.
-   Grolemund, G. y Wickham, H. (2016): _R for Data Science_. O'Reilly. 

Contenidos
--------

1.  Introducción
    1.  ¿Qué es el _Big Data_?
    2.  Ciencia de datos para la investigación social
        1.  Retos
        2.  Oportunidades
    4.  ¿Qué necesitas para ser un científico de datos?
    5.  Una introducción a `R`

<br /> 

2.  Captura de datos de Internet
    1.  Capturar datos provenientes de una REST API
		2. Estructura de una REST API
        1. Formatos de intercambio de datos
		3. Un ejemplo: Search API de Twitter
    3.  Obtención de datos de páginas web
        1.  Una breve introducción a HTML y CSS
		2.  Un ejemplo: Resultados electorales en Colombia
	2. Captura de datos en streaming
	   1. Un ejemplo: Streaming API de Twitter 

<br /> 

3.  Análisis de redes sociales 
    1.  Caminos
    3.  Componentes
    4.  Matrices de adyacencias
    5.  Descriptivos de una red
	1. Medidas de cohesión
	2. Medidas de transitividad
	3. Medidas de centralidad
	6.  Detección de comunidades
	7.  Contraste de hipótesis en redes sociales
    8.  Un ejemplo: Redes de colaboración en el Congreso de los Diputados

<br /> 

4.  Aprendizaje no-supervisado
    1.  Una visión general del problema
    2.  Análisis de componentes principales
	3.  Análisis de conglomerados
	4.  Escalado multidimensional
    5.  Un ejemplo: Criminalidad en Colombia

<br /> 

5.  Procesamiento de lenguaje natural y análisis de textos
    1.  Procesamiento de lenguaje natural
		1. Tokenización
		2. Normalización
		3. Reconocimiento de entidades
		4. Etiquetadores 
    4.  Análisis de textos
        1.  El modelo de bolsa de palabras
		2.  Matrices de términos y TF-IDF
		4.  Similitud entre documentos
		5.  Modelos de clasificación de documentos
    5. Un ejemplo: Temas en notas de prensa en el Senado de los Estados Unidos

<br />

6.  Fusión de registros
    1.  Contexto del problema
    2.  El marco Fellegi-Sunter
    4.  Métricas en cadenas de texto
	5.  Errores de clasificación 
	6.  Reglas óptimas de clasificación
	7.  Estimación de parámetros 
    6.  Un ejemplo: Fusionar congresistas con cuentas de Twitter
