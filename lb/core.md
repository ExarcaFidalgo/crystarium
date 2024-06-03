Ha llegado la fatídica hora de poner en orden mis pensamientos.

# Orígenes

[Todo comenzó en el Milán](https://www.youtube.com/watch?v=FDNiE5CKuSw). Llegó a nuestras manos un corpus de documentos notariales del medievo asturiano. Vírgenes a los ojos de los mortales, sus secretos únicamente accesibles a los iniciados.
Surgió entonces la vaga idea de emplear nuestras artes oscuras, también conocidas como "tecnologías semánticas", para, de algún modo, facilitar su procesamiento y consulta. 

La extracción y clasificación de información del lenguaje natural se ha tratado hasta la extenuación, con un importante matiz: sobre lenguajes modernos. Mi querido lector, el español del siglo XIV dista lo suficiente del actual como para considerarlo prácticamente un idioma distinto.
Independientemente de las etiquetas que impongamos, lo cierto es que es lo suficientemente distinto como para que las herramientas de procesamiento fracasen como la invasión italiana de Egipto.
La ortografía y la gramática es una invención moderna; estos textos plasman el lenguaje hablado a gusto del escritor, que muchas veces tiende a "culturizar" las palabras añadiendo caracteres extra y otras gracietas. Una complicación más a añadir a la natural evolución de un lenguaje durante 700 años.

_E magar dixiesse que los dichos maravedís me non foran dados e metudos en mio poder, otorgo que me non vala._

Si bien en un inicio los sueños febriles podían apuntar a una clasificación del texto y sus secciones (como realizan los compañeros de DocuLab manualmente) estas circunstancias pronto dejaron claro que era una propuesta excesivamente ambiciosa.
Ello implicaría, seguramente, un análisis sintáctico del español medieval (que para mi sorpresa existe, consultad Freeling), un consiguiente análisis de la temática, etc... Cuestiones que atraen más al otro miembro de la sección historicista de WESO.

Así pues, mi deseo se materializó en la siguiente visión: una extracción de entidades y sus relaciones de aquestos textos de otroras eras, de cara a la construcción de nuestro propio grafo de conocimiento (KG para los amigos) o, para los de grado 33, base de conocimiento (KB)

Esta simple idea, como veremos, tiene sus... implicaciones.

# Soluciones

Cuando digo soluciones, entiéndase en potencia y no en acto. Principalmente, acudieron a mí las siguientes ideas:
* Reglas (rule-based approach). Aquellos que se aventuraron con esta idea siempre -hasta donde yo sé- optaron por esta vía. Básicamente, consiste en aplicar patrones al texto. Fácil de implementar, obviamente extremadamente limitado.
  Necesitaremos un experto que mapee todos los posibles patrones linguísticos que denotan una relación sujeto-objeto (y si no tenemos un análisis sintáctico, nos vamos a divertir trazando todas las posibles combinaciones). Pero el lenguaje natural es complejo, y la precisión/recall serán bajos.
  Para rematar, todo este trabajo estará extremadamente localizado. Ni que decir que esta labor es más propia de historiadores que de tecnosacerdotes.
* Normalización ortográfica/traducción. Si nuestra mayor limitación reside en las limitadas herramientas de NLP para el lenguaje histórico, ¿por qué no adaptar sus contenidos al equivalente moderno? Si En resumidas cuentas, si Mahoma no va a la montaña...
  De nuevo, existen algunas aproximaciones en este sentido pero están bastante localizadas, y ni siquiera son públicas. En pos de una generalización de nuestra metodología, descarto esta opción. Además, creo que es un temor bastante razonable la posible pérdida de información, sobre todo en cuanto a la traducción refiere...
* Procesamiento directo apoyado en KB externos. Sí... no nos queda otra. Por otra parte, esto lo acerca más a nuestro metafórico hogar.

Para ser precisos, la pipeline que tengo en mente es la siguiente:

1. Detección de entidades con BERT sobre el texto original (sanitizado). _Esto nos otorgará un listado de entidades y sus tipos._
2. Creación de un modelo de predicción de enlaces sobre dataset basado en KB externos.
3. Creación de nuevas entidades sobre el dataset a partir de las extraídas en 1).
4. Predicción de posibles relaciones entre dichas entidades.

[Esa es la idea.](https://www.youtube.com/watch?v=jbMyybiLbdE) Naturalmente, su ejecución es más... compleja. Pero vamos por partes.

## BERT

Por fortuna para mí, NER sobre textos históricos ya ha sido explorado empleando variantes de BERT-Multilingual. Hasta donde yo sé, español, francés, inglés y alemán. El español lo he probado y funciona maravillosamente. La existencia de diversos modelos con la misma base creo que es argumento suficiente para la generalización de este paso, con suficiente voluntad. En cuanto a mí respecta, con tenerlo disponible para los principales lenguajes europeos es suficiente.

## Modelo de predicción

Aquí viene lo bueno. El primer paso consiste en extraer tripletas relevantes de Wikidata. Wdsub está sobre la mesa como opción pero entretanto he rescatado WD-Scholia para llevar a cabo queries paginadas -dado que el nº de elementos es demasiado grande para una sola-. Principalmente estoy extrayendo personas nacidas en España antes de 1800 y tomando los atributos presentes en el EntitySchema de Q5; para acotarlo un poco, porque si no es un sindiós de propiedades.

Los problemas son los de siempre; los datos son muy dispersos (para las propiedades potenciales, los valores de la entidad media se reducen al mínimo) y a menudo los valores que aparecen son contraproducentes. Por ejemplo, [Hernán Cortés](https://www.wikidata.org/wiki/Q7326) tiene como P734 Cortés, pero su padre [Martín](https://www.wikidata.org/wiki/Q50824534) carece de dicho valor. Para inferir cuestiones tales como relaciones paterno-filiales, plantea unas cuantas dudas... eso sin tener en cuenta que en estas épocas, por lo que veo la herencia de apellidos suele ser una cuestión aristocrática y que nuestros humildes jornaleros toman el nombre del padre... Podemos apañar el problema procesando nosotros mismos la combinación nombre/apellidos a partir de la etiqueta de la entidad...

En fin, supongamos la creación de un grafo de conocimiento con las entidades extraídas en el párrafo previo. Queremos entrenar un modelo de predicción para que, al introducir las entidades detectadas por BERT, emita un juicio. ¿Qué modelo empleamos? Bueno, la predicción de enlaces en KG tiene algoritmos de cierta relevancia, como ComplEx o SimplE. 

Maldita sea la hora en que opté por usar la librería Stellargraph para tratar de aplicar ComplEx (viene integrado). En mi vida tuve que tratar con una librería tan contraproducente; la mayor parte de la documentación no se corresponde con el código real, y eso teniendo en cuenta que la documentación sólo plantea el entrenamiento (no el uso) del modelo sobre datasets (introducir los propios es una odisea) predefinidos (que tampoco funcionan). Después de unas cuantas lunas batallando con ello, he llegado a la conclusión de que está más roto que el alma moderna; no genera nada con sentido.

Visto el panorama, he optado por obviar tales intermediarios y estudiar directamente la aplicación de estos modelos de predicción sobre Wikidata. Y hay cosas interesantes: existe [un dataset basado en Wikidata/Wikipedia](https://paperswithcode.com/sota/link-prediction-on-wikidata5m) , sobre el cual se han aplicado diversos modelos como KGT5-context, ComplEx, SimplE, etc. Tengo por delante más combates con esto, pero parece más prometedor que lo anterior; cuanto menos, es más próximo a nuestras pretensiones.

*Ver [el artículo al respecto](./link.md)*

## Creación de entidades 

Llegado este punto, lo peor ha pasado; una vez cruzado Moore, OK, las probabilidades de ser arrollado por un tornado se reducen drásticamente. La información que se puede extraer del NER es escasa:
* Nombre (y apellidos si PER) es lo único directo; no gran cosa, pero los apellidos guardan gran significado para nosotros.
* Lugar. Las construcciones PER \[de\] LOC son relativamente comunes. Creo que nos podemos permitir el lujo.
* País y fecha. Extrapolación de los metadatos del documento.
