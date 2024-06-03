# Predicción de enlaces

Expando aquí la cuestión de los modelos de predicción de enlaces empleando Wikidata. Más fructífero de lo que cabría esperar. [Ambientemos.](https://www.youtube.com/watch?v=3cKy5Qk0QMM)

## Propuesta

Bien, la idea consta de dos fases: primero, realizaremos una *predicción semi-inductiva* sobre un subconjunto de Wikidata como WikiKG90Mv2 (que está preparado para estas labores)ón; esto es, consideremos que las entidades obtenidas en NER son nodos no vistos
 del grafo de conocimiento de Wikidata, y -asumiendo tripletas de la forma (?head ?relation ?tail) - obtengamos una predicción de _?tail_ siendo _?head_ un nodo no visto y _?tail_ un nodo visto (de ahí lo de semi-inductiva).

 ![../images/Prediccion.svg](https://raw.githubusercontent.com/ExarcaFidalgo/crystarium/master/images/Prediccion.svg)

 Tras esta primera fase, tendremos una información -en principio, básica- acerca de las entidades reconocidas. Tomando como premisa que estas relaciones son ciertas, podemos realizar una *predicción completamente inductiva* para obtener enlaces entre nodos no vistos (o séase, entre nuestras entidades extraídas del texto)

 ![../images/Prediccion2.svg](https://raw.githubusercontent.com/ExarcaFidalgo/crystarium/master/images/Prediccion2.svg)

 De acuerdo, tenemos un listado finito de _?head_ y _?tail_ es responsabilidad del modelo de predicción. La única incógnita pendiente es la siguiente: ¿qué _?relation_ predecimos? Porque en Wikidata hay 11930, lo cual es ligeramente excesivo.

 Solución: nuestro hechizo definitivo, Shex. Puesto que Wikidata define una serie de [Entity Schemas](https://www.wikidata.org/wiki/Wikidata:Database_reports/EntitySchema_directory) mediante Shex que definen las posibles propiedades de una serie de clases,
 podemos emplearlos para acotar las posibilidades y ahorrar fuerzas al espíritu máquina.

 ## Ejemplo

 Supongamos que de un texto histórico extraemos las siguientes entidades: (Alffonso Fernándiz, PER) (Fernando Suáriz, PER) (Borondés, LOC).

 El primer paso sería realizar una predicción semi-inductiva sobre Wikidata para cada una de estas entidades. Puesto que las dos primeras entidades se han identificado como personas, tiraríamos de [E10](https://www.wikidata.org/wiki/EntitySchema:E10)
Realizamos pues una serie de predicciones tal que:
* _(Alffonso Fernándiz, instance of, ?) => Human (-0.020665482)_
* _(Alffonso Fernándiz, gender, ?) => male (-0.00043228792)_
* _(Alffonso Fernándiz, given name, ?) => Alfonso (-0.21522462)_
* _(Alffonso Fernándiz, family name, ?) => Fernándiz (-0.05276735)_
* _(Alffonso Fernándiz, country of citizenship, ?) => Spain (-0.6640309)_
* _(Alffonso Fernándiz, native language, ?) => Spanish (-0.025430549)_
* _(Alffonso Fernándiz, father, ?) => Alfonso Fernándiz (-3.218818)_

Nótese que algunas de estas relaciones obtienen valores abismales, como _country_ o _father_; realiza predicciones, pero se consideran altamente improbables. En el caso de las relaciones interpersonales, que nuestro hombre del cual sólo tenemos un nombre no esté relacionado con una persona aleatoria de Wikidata es esperable. A menos que se trate de Genghis Khan.

De todos modos, en esta primera fase hemos obtenido información nada desdeñable. Alfonso Fernández es un hombre, en el sentido universal y particular de la palabra, se llama Alfonso y se apellida Fernández, y habla español.

Que Alfonso Fernández tenga por nombre Alfonso y por apellido Fernández parece trivial, pero creedme, tiene más valor del que parece. Porque con esta información, llegó el momento de realizar una predicción completamente inductiva.

Y, amigos míos, si examinamos la información de [Fernández](https://www.wikidata.org/wiki/Q164892), veréis que es un patronímico basado en Fernando (o Fernán). ¿Que quiere decir esto? Que programáticamente, a partir de la cadena de texto original, podemos determinar que el padre de Alfonso Fernández, o bien se apellida Fernández, o se llama Fernando. ¡Maravilloso!

Y, casualmente, en nuestro subconjunto de entidades extraídas, tenemos un _(Fernando Suáriz, given name, ?) => Fernando_. De modo que en esta segunda fase, podemos aventurar, _(Alffonso Fernándiz, father, ?) => Fernando Suáriz_.

## Problemas

Todo periplo del héroe tiene sus retos y tentaciones, naturalmente. 

1. Wikidata emplea un modelo de identificación única que permite referir de manera singular a cualquier entidad/propiedad del conocimiento humano. El modelo KGT5 que estoy empleando, por algún motivo, opta por sacrificar esta excelencia en pos de utilizar etiquetas. No sólo eso, sino que si hay variables ortográficas con respecto a la entidad objetivo, parece indicar la entidad objetivo pero con la etiqueta incorrecta... Esto va a ser un DOLOR para conseguir los IDs (necesarios para extraer toda la información)
2. ¿Qué medida empleamos para indicar la probabilidad de acierto en las predicciones completamente inductivas? ¿Es posible siquiera? En todo caso, esto va a requerir una revisión posterior seguro.
3. No trivial: ¿cómo definimos las reglas para las predicciones completamente inductivas? Ya que cada propiedad puede, y debe, ser un mundo.

Respecto al punto 3, yo pensaba en seguir tirando de Shex y de algún modo definir los "caminos" para cada propiedad. Tomemos _father_, por ejemplo.
```
<human> EXTRA wdt:P31 {
  wdt:P22 @<human> *;
}
```

En lugar de _<human>_, ¿tal vez podríamos crear una nueva shape que describa al padre?

```
<human> EXTRA wdt:P31 {
  wdt:P22 @<human> *;
}

<father> EXTRA wdt:P31 {
  wdt:P735 [<human>/P734/P31/PQ642] * ;                    # given name
  wdt:P734 [<human>/P735] * ;                    # family name
}
```

Resumen: hablar con el CEO of Shex.
