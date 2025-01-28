Bueno, creo que todo el paradigma acaba de cambiar por completo ya que de repente ha surgido un LLM que *sí* parece capaz de procesar textos del medievo (ya era hora). Supongo que Deepseek no ha hundido la bolsa estadounidense por nada.

En fin, que aportando como entrada un fragmento complejo de las notarías, por ejemplo:

"Connosçida cosa sea a quantos esta carta viren cómmo yo Menén Suáriz, morador en Borondés, e yo Loriença Suáriz, sua muller, dessenbargamos a vos Alffonso Fernándiz, oryz, morador enna çipdat de Oviedo, fillo de Aldonça Suáriz, hermana de mí, Menén Suáriz, e a vuestra muller María Álvariz, el sétimo de todos quantos heredamientos e lantados avían Suer Pérez e María Yánnez, padre e madre que foron de mi, Menén Suáriz, e de la dicha Aldonça Suáriz, vuestra madre, en Borondés e en Vallongo e en sos términos e en la alffoz de Grado, huquier que llos pertenesçían, tan bien los que ora están partidos commo por partir, el qual sétimo vos pertenez por nome de la dicha Aldonça Suáriz, vuestra madre."

  Obtenemos el siguiente resultado:

```
@prefix : <http://example.org/> .

:MenénSuáriz a :Person ;
    :residesIn :Borondés ;
    :marriedTo :LoriençaSuáriz ;
    :childOf :SuerPérez, :MaríaYánnez ;
    :siblingOf :AldonçaSuáriz .

:LoriençaSuáriz a :Person ;
    :marriedTo :MenénSuáriz .

:AlffonsoFernándiz a :Person ;
    :residesIn :Oviedo ;
    :marriedTo :MaríaÁlvariz ;
    :childOf :AldonçaSuáriz .

:MaríaÁlvariz a :Person ;
    :marriedTo :AlffonsoFernándiz .

:AldonçaSuáriz a :Person ;
    :childOf :SuerPérez, :MaríaYánnez ;
    :parentOf :AlffonsoFernándiz ;
    :siblingOf :MenénSuáriz .

:SuerPérez a :Person ;
    :parentOf :MenénSuáriz, :AldonçaSuáriz ;
    :spouseOf :MaríaYánnez .

:MaríaYánnez a :Person ;
    :parentOf :MenénSuáriz, :AldonçaSuáriz ;
    :spouseOf :SuerPérez .

:Borondés a :Place ;
    :residenceOf :MenénSuáriz, :LoriençaSuáriz .

:Oviedo a :Place ;
    :residenceOf :AlffonsoFernándiz, :MaríaÁlvariz .

:Vallongo a :Place ;
    :associatedWith :Inheritances .

:Grado a :Place ;
    :associatedWith :Inheritances .

:Inheritances a :Property ;
    :locatedIn :Borondés, :Vallongo, :Grado ;
    :ownedBy :SuerPérez, :MaríaYánnez ;
    :portionBelongsTo :AlffonsoFernándiz ;
    :portionValue "1/7" .

:MenénSuáriz :refersTo :AlffonsoFernándiz, :MaríaÁlvariz ;
    :describes :Inheritances .

:AlffonsoFernándiz :inheritsFrom :AldonçaSuáriz ;
    :ownsPortionOf :Inheritances .
```
A estudiar: posible pipeline de retroalimentación con Wikidata para el enriquecimiento tanto de nuestro grafo como de Wikidata...
