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


