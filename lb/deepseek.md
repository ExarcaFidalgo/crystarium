Bueno, creo que todo el paradigma acaba de cambiar por completo ya que de repente ha surgido un LLM que *sí* parece capaz de procesar textos del medievo (ya era hora). Supongo que Deepseek no ha hundido la bolsa estadounidense por nada.

En fin, que aportando como entrada un fragmento complejo de las notarías, por ejemplo:

"Connosçida cosa sea a quantos esta carta viren cómmo yo Pero Suáriz de Borondés, fillo de Suer Rodríguiz, uiendo a vos Alffonso Ferrándiz, oriz, e a uuestra muller María Áluarez, moradores en Ouiedo, el dolzal de una tierra que iaz y en Borondés,"

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
