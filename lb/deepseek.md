Bueno, creo que todo el paradigma acaba de cambiar por completo ya que de repente ha surgido un LLM que *sí* parece capaz de procesar textos del medievo (ya era hora). Supongo que Deepseek no ha hundido la bolsa estadounidense por nada.

En fin, que aportando como entrada el comando "Extract all entities and relations from the following text fragments from a 14th century Spanish corpus. The results must conform to the Web Ontology Language, using the appropriate classes and properties. Note that when a person's name is accompanied by ‘de X’, this is not part of the surname itself but the X denotes belonging to a place." y un fragmento complejo de las notarías, por ejemplo:

"Connosçida cosa sea a quantos esta carta viren cómmo yo Menén Suáriz, morador en Borondés, e yo Loriença Suáriz, sua muller, dessenbargamos a vos Alffonso Fernándiz, oryz, morador enna çipdat de Oviedo, fillo de Aldonça Suáriz, hermana de mí, Menén Suáriz, e a vuestra muller María Álvariz, el sétimo de todos quantos heredamientos e lantados avían Suer Pérez e María Yánnez, padre e madre que foron de mi, Menén Suáriz, e de la dicha Aldonça Suáriz, vuestra madre, en Borondés e en Vallongo e en sos términos e en la alffoz de Grado, huquier que llos pertenesçían, tan bien los que ora están partidos commo por partir, el qual sétimo vos pertenez por nome de la dicha Aldonça Suáriz, vuestra madre."

  Obtenemos el siguiente resultado:

```
@prefix ex: <http://example.org/> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .

### Entities of Type Person
ex:MenénSuáriz a ex:Person ;
    ex:Name "Menén" ;
    ex:Surname "Suáriz" ;
    ex:residence ex:Borondés ;
    ex:spouse ex:LoriençaSuáriz .

ex:LoriençaSuáriz a ex:Person ;
    ex:Name "Loriença" ;
    ex:Surname "Suáriz" ;
    ex:spouse ex:MenénSuáriz .

ex:AlffonsoFernándiz a ex:Person ;
    ex:Name "Alffonso" ;
    ex:Surname "Fernándiz" ;
    ex:residence ex:Oviedo ;
    ex:mother ex:AldonçaSuáriz ;
    ex:spouse ex:MaríaÁlvariz .

ex:MaríaÁlvariz a ex:Person ;
    ex:Name "María" ;
    ex:Surname "Álvariz" ;
    ex:spouse ex:AlffonsoFernándiz .

ex:AldonçaSuáriz a ex:Person ;
    ex:Name "Aldonça" ;
    ex:Surname "Suáriz" ;
    ex:child ex:AlffonsoFernándiz ;
    ex:sibling ex:MenénSuáriz .

ex:SuerPérez a ex:Person ;
    ex:Name "Suer" ;
    ex:Surname "Pérez" ;
    ex:spouse ex:MaríaYánnez ;
    ex:child ex:MenénSuáriz, ex:AldonçaSuáriz .

ex:MaríaYánnez a ex:Person ;
    ex:Name "María" ;
    ex:Surname "Yánnez" ;
    ex:spouse ex:SuerPérez ;
    ex:child ex:MenénSuáriz, ex:AldonçaSuáriz .

ex:IohanGonçáliz a ex:Person ;
    ex:Name "Iohan" ;
    ex:Surname "Gonçáliz" ;
    ex:father ex:GonçaloSuáriz ;
    ex:mother ex:MaríaÁlvariz .

ex:GonçaloSuáriz a ex:Person ;
    ex:Name "Gonçalo" ;
    ex:Surname "Suáriz" ;
    ex:spouse ex:MaríaÁlvariz ;
    ex:child ex:IohanGonçáliz .

ex:SuerDíazDeCortina a ex:Person ;
    ex:Name "Suer" ;
    ex:Surname "Díaz de Cortina" ;
    ex:uncle ex:GarçíaSuáriz ;
    ex:role "Fiador" .

ex:GarçíaSuáriz a ex:Person ;
    ex:Name "Garçía" ;
    ex:Surname "Suáriz" ;
    ex:nephew ex:SuerDíazDeCortina .

### Entities of Type Location
ex:Borondés a ex:Location ;
    ex:name "Borondés" .

ex:Oviedo a ex:Location ;
    ex:name "Oviedo" .

ex:Vallongo a ex:Location ;
    ex:name "Vallongo" .

ex:Grado a ex:Location ;
    ex:name "Grado" .

### Entities of Type Event
ex:PropertyTransfer a ex:Event ;
    ex:date "1365-01-03"^^xsd:date ;
    ex:location ex:Borondés, ex:Vallongo, ex:Grado ;
    ex:involves ex:MenénSuáriz, ex:LoriençaSuáriz, ex:AlffonsoFernándiz, ex:MaríaÁlvariz ;
    ex:description "Transfer of property rights to Alffonso Fernándiz and María Álvariz." .

ex:LegalObligation a ex:Event ;
    ex:date "1365-01-03"^^xsd:date ;
    ex:involves ex:MenénSuáriz, ex:LoriençaSuáriz, ex:SuerDíazDeCortina ;
    ex:description "Legal obligation to pay 50 maravedís if the agreement is violated." .

### Relationships
ex:MenénSuáriz ex:owns ex:PropertyInBorondés, ex:PropertyInVallongo, ex:PropertyInGrado .
ex:AlffonsoFernándiz ex:inherits ex:PropertyInBorondés, ex:PropertyInVallongo, ex:PropertyInGrado .
ex:SuerDíazDeCortina ex:guarantees ex:LegalObligation .
```
A estudiar: posible pipeline de retroalimentación con Wikidata para el enriquecimiento tanto de nuestro grafo como de Wikidata...
