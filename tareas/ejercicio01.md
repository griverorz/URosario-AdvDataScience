### Ejercicio 1

Las respuestas a este ejercicio deben enviarse por correo electrónico (a la
dirección proveída durante la clase) y como archivo adjunto antes de las 20:00h
(hora de Bogotá) del viernes 16 de junio. No se aceptarán envíos después de esa
hora _bajo ninguna condición_. Utiliza "Ejercicio 1" como sujeto del mensaje y
asegúrate de que estás en copia en el correo ya que será la única documentación
aceptable para demostrar errores en el envío. Envía únicamente el código que
hayas usado en un archivo `.R`.


1. Escribe una función que tome como argumento un entero n>1 y genere la
   secuencia de los cuadrados de los enteros 1, ..., n^2. Es decir, si el
   input es `4`, la función debe retornar el vector `c(1, 4, 9, 16)`.
   
2. Modifica el código anterior para que cuando el input de la función sea un
   número par (divisible de manera entera entre 2) la función devuelva, en lugar
   de una secuencia de cuadrados, la secuencia de 1 a n/2. Es decir, si el
   input es 3, la función debe retornar el vector `c(1, 4, 9)` pero si el input
   es 4, debe retornar `c(1, 2)`. Para determinar si un valor es par puedes
   ayudarte del operador módulo `%%` que devuelve el resto de dividir el número
   de la izquierda entre el número de la derecha. Ejemplos, `2 %% 2` es igual a
   0, `3 %% 2` es igual a 1, `4 %% 2` es igual a 0, ...

3. Obtén la lista de miembros del Congreso en Estados Unidos que aparece en la
   sección _Legislators_ de MapLight
   ([enlace](http://classic.maplight.org/us-congress/legislator)). **Pregunta
   optativa:** Obtén para cada legislador, la lista de las 10 principales
   _organizaciones_ que han hecho donaciones a su campaña (por ejemplo, los datos
   de Andrew Lamar
   Alexander están disponibles en
   este [enlace](http://classic.maplight.org/us-congress/legislator/530-andrew-lamar-alexander)).

4. Obtén 10 usuarios de Twitter que hayan mencionado "Colombia" en un tweet
   reciente. Presenta los datos en un `data.frame` en el que la primera columna
   sea el nombre del usuario (_screen name_), la segunda columna sea cuántos
   seguidores tiene (_followers_) y la tercera columna sea la fecha en la que el
   usuario creó su cuenta (_created_).
