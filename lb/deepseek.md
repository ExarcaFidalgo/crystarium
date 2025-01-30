Bueno, creo que todo el paradigma acaba de cambiar por completo ya que de repente ha surgido un LLM que *sí* parece capaz de procesar textos del medievo (ya era hora). Supongo que Deepseek no ha hundido la bolsa estadounidense por nada.

En fin, que aportando como entrada el siguiente comando
```
Extract all entities and relations from the following text fragments from a 14th century Spanish corpus. The results must be in Turtle format,conforming to the classes and atributes described in the Shape Expression i provide. Note that when a person's name is accompanied by ‘de X’, this is not part of the surname itself but the X denotes belonging to a place.

prefix : <http://example.org/>
prefix xsd: <http://www.w3.org/2001/XMLSchema#>
prefix owl: <https://w3id.org/MON/person.owl#>

:Person {
  owl:firstName xsd:string ?;
  owl:lastName xsd:string ?;
  owl:nickname xsd:string ?;
  owl:auntOf @:Person *;
  owl:childInLawOf @:Person *;
  owl:childOf @:Person *;
  owl:cousinOf @:Person *;
  owl:fatherInLaw @:Person *;
  owl:fatherOf @:Person *;
  owl:friendOf @:Person *;
  owl:grandchildOf @:Person *;
  owl:grandparentOf @:Person *;
  owl:hasBirthPlace @:Location ?;
  owl:livesIn @:Location ?;
  owl:motherInLaw @:Person *;
  owl:motherOf @:Person *;
  owl:nephewOf @:Person *;
  owl:nieceOf @:Person *;
  owl:siblingOf @:Person *;
  owl:spouseOf @:Person *;
  owl:uncleOf @:Person *;
}

:Location {
  :name xsd:string ;
}
```
y un fragmento complejo de las notarías, por ejemplo:

"Connosçida cosa sea a quantos esta carta viren cómmo yo Menén Suáriz, morador en Borondés, e yo Loriença Suáriz, sua muller, dessenbargamos a vos Alffonso Fernándiz, oryz, morador enna çipdat de Oviedo, fillo de Aldonça Suáriz, hermana de mí, Menén Suáriz, e a vuestra muller María Álvariz, el sétimo de todos quantos heredamientos e lantados avían Suer Pérez e María Yánnez, padre e madre que foron de mi, Menén Suáriz, e de la dicha Aldonça Suáriz, vuestra madre, en Borondés e en Vallongo e en sos términos e en la alffoz de Grado, huquier que llos pertenesçían, tan bien los que ora están partidos commo por partir, el qual sétimo vos pertenez por nome de la dicha Aldonça Suáriz, vuestra madre. Otrossí vos dessenbargamos el medio sétimo de los dichos heredamientos e lantados que son ennos dichos lugares tan bien partidos commo por partir, el qual medio sétimo yera de Iohan Gonçáliz, vuestro quarmano, fillo de Gonçalo Suáriz e de María Álvariz."

  Obtenemos el siguiente resultado:

```
@prefix : <http://example.org/> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix owl: <https://w3id.org/MON/person.owl#> .

### Persons
:MenénSuáriz a owl:Person ;
    owl:firstName "Menén" ;
    owl:lastName "Suáriz" ;
    owl:livesIn :Borondés ;
    owl:spouseOf :LoriençaSuáriz ;
    owl:childOf :SuerPérez, :MaríaYánnez ;
    owl:siblingOf :AldonçaSuáriz ;
    owl:uncleOf :AlffonsoFernándiz .

:LoriençaSuáriz a owl:Person ;
    owl:firstName "Loriença" ;
    owl:lastName "Suáriz" ;
    owl:spouseOf :MenénSuáriz .

:AlffonsoFernándiz a owl:Person ;
    owl:firstName "Alffonso" ;
    owl:lastName "Fernándiz" ;
    owl:livesIn :Oviedo ;
    owl:childOf :AldonçaSuáriz ;
    owl:spouseOf :MaríaÁlvariz ;
    owl:cousinOf :MenénSuáriz .

:MaríaÁlvariz a owl:Person ;
    owl:firstName "María" ;
    owl:lastName "Álvariz" ;
    owl:spouseOf :AlffonsoFernándiz ;
    owl:childOf :GonçaloSuáriz, :MaríaÁlvariz .

:AldonçaSuáriz a owl:Person ;
    owl:firstName "Aldonça" ;
    owl:lastName "Suáriz" ;
    owl:childOf :SuerPérez, :MaríaYánnez ;
    owl:siblingOf :MenénSuáriz ;
    owl:motherOf :AlffonsoFernándiz .

:SuerPérez a owl:Person ;
    owl:firstName "Suer" ;
    owl:lastName "Pérez" ;
    owl:spouseOf :MaríaYánnez ;
    owl:fatherOf :MenénSuáriz, :AldonçaSuáriz .

:MaríaYánnez a owl:Person ;
    owl:firstName "María" ;
    owl:lastName "Yánnez" ;
    owl:spouseOf :SuerPérez ;
    owl:motherOf :MenénSuáriz, :AldonçaSuáriz .

:IohanGonçáliz a owl:Person ;
    owl:firstName "Iohan" ;
    owl:lastName "Gonçáliz" ;
    owl:childOf :GonçaloSuáriz, :MaríaÁlvariz ;
    owl:cousinOf :AlffonsoFernándiz .

:GonçaloSuáriz a owl:Person ;
    owl:firstName "Gonçalo" ;
    owl:lastName "Suáriz" ;
    owl:spouseOf :MaríaÁlvariz ;
    owl:fatherOf :IohanGonçáliz .

:SuerDíazDeCortina a owl:Person ;
    owl:firstName "Suer" ;
    owl:lastName "Díaz de Cortina" ;
    owl:nephewOf :GarçíaSuáriz ;
    owl:fiadorOf :MenénSuáriz, :LoriençaSuáriz .

:GarçíaSuáriz a owl:Person ;
    owl:firstName "Garçía" ;
    owl:lastName "Suáriz" ;
    owl:uncleOf :SuerDíazDeCortina .

:FernánFernándiz a owl:Person ;
    owl:firstName "Fernán" ;
    owl:lastName "Fernándiz" ;
    owl:role "escudero" ;
    owl:witnessOf :Carta .

:GonçaloRodríguiz a owl:Person ;
    owl:firstName "Gonçalo" ;
    owl:lastName "Rodríguiz" ;
    owl:childOf :RodrigoMartíniz ;
    owl:witnessOf :Carta .

:FernánSirgoDeVallo a owl:Person ;
    owl:firstName "Fernán" ;
    owl:lastName "Sirgo de Vallo" ;
    owl:witnessOf :Carta .

:SuerRodríguiz a owl:Person ;
    owl:firstName "Suer" ;
    owl:lastName "Rodríguiz" ;
    owl:role "escripván" ;
    owl:witnessOf :Carta .

:FernánGarot a owl:Person ;
    owl:firstName "Fernán" ;
    owl:lastName "Garot" ;
    owl:witnessOf :Carta .

:AlffonsoPérezDeSanRomano a owl:Person ;
    owl:firstName "Alffonso" ;
    owl:lastName "Pérez de San Romano" ;
    owl:witnessOf :Carta .

:PeroFernándiz a owl:Person ;
    owl:firstName "Pero" ;
    owl:lastName "Fernándiz" ;
    owl:role "notario público del rey" ;
    owl:livesIn :Grado ;
    owl:notaryOf :Carta .

### Locations
:Borondés a :Location ;
    :name "Borondés" .

:Oviedo a :Location ;
    :name "Oviedo" .

:Vallongo a :Location ;
    :name "Vallongo" .

:Grado a :Location ;
    :name "Grado" .

### Document (Carta)
:Carta a :Document ;
    :date "1365-01-03"^^xsd:date ;
    :notary :PeroFernándiz ;
    :witness :FernánFernándiz, :GonçaloRodríguiz, :FernánSirgoDeVallo, :SuerRodríguiz, :FernánGarot, :AlffonsoPérezDeSanRomano ;
    :concerns :MenénSuáriz, :LoriençaSuáriz, :AlffonsoFernándiz, :MaríaÁlvariz, :SuerDíazDeCortina .
```
A estudiar: 
* Posible pipeline de retroalimentación con Wikidata para el enriquecimiento tanto de nuestro grafo como de Wikidata...
* Impacto de Shex/SHACL como mecanismo para las LLM...?
