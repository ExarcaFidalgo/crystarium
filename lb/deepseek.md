Bueno, creo que todo el paradigma acaba de cambiar por completo ya que de repente ha surgido un LLM que *sí* parece capaz de procesar textos del medievo (ya era hora). Supongo que Deepseek no ha hundido la bolsa estadounidense por nada.

En fin, que aportando como entrada el comando
"Extract all entities and relations from the following text fragments from a 14th century Spanish corpus. The results must be in Turtle format,using the appropriate OWL classes and properties. Include First name and Last name properties, if data is available. Note that when a person's name is accompanied by ‘de X’, this is not part of the surname itself but the X denotes belonging to a place." 
y un fragmento complejo de las notarías, por ejemplo:

"Connosçida cosa sea a quantos esta carta viren cómmo yo Menén Suáriz, morador en Borondés, e yo Loriença Suáriz, sua muller, dessenbargamos a vos Alffonso Fernándiz, oryz, morador enna çipdat de Oviedo, fillo de Aldonça Suáriz, hermana de mí, Menén Suáriz, e a vuestra muller María Álvariz, el sétimo de todos quantos heredamientos e lantados avían Suer Pérez e María Yánnez, padre e madre que foron de mi, Menén Suáriz, e de la dicha Aldonça Suáriz, vuestra madre, en Borondés e en Vallongo e en sos términos e en la alffoz de Grado, huquier que llos pertenesçían, tan bien los que ora están partidos commo por partir, el qual sétimo vos pertenez por nome de la dicha Aldonça Suáriz, vuestra madre. Otrossí vos dessenbargamos el medio sétimo de los dichos heredamientos e lantados que son ennos dichos lugares tan bien partidos commo por partir, el qual medio sétimo yera de Iohan Gonçáliz, vuestro quarmano, fillo de Gonçalo Suáriz e de María Álvariz."

  Obtenemos el siguiente resultado:

```
@prefix rdf: &lt;http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix owl: &lt;http://www.w3.org/2002/07/owl#> .
@prefix xsd: &lt;http://www.w3.org/2001/XMLSchema#> .
@prefix ex: &lt;http://example.org/ontology#> .

# Persons
ex:MenénSuáriz a ex:Person ;
    ex:hasFirstName "Menén" ;
    ex:hasLastName "Suáriz" ;
    ex:hasSpouse ex:LoriençaSuáriz ;
    ex:hasParent ex:SuerPérez, ex:MaríaYánnez ;
    ex:hasSibling ex:AldonçaSuáriz ;
    ex:livesIn ex:Borondés .

ex:LoriençaSuáriz a ex:Person ;
    ex:hasFirstName "Loriença" ;
    ex:hasLastName "Suáriz" ;
    ex:hasSpouse ex:MenénSuáriz .

ex:AlffonsoFernándiz a ex:Person ;
    ex:hasFirstName "Alffonso" ;
    ex:hasLastName "Fernándiz" ;
    ex:hasParent ex:AldonçaSuáriz ;
    ex:hasSpouse ex:MaríaÁlvariz ;
    ex:livesIn ex:Oviedo .

ex:MaríaÁlvariz a ex:Person ;
    ex:hasFirstName "María" ;
    ex:hasLastName "Álvariz" ;
    ex:hasSpouse ex:AlffonsoFernándiz .

ex:AldonçaSuáriz a ex:Person ;
    ex:hasFirstName "Aldonça" ;
    ex:hasLastName "Suáriz" ;
    ex:hasParent ex:SuerPérez, ex:MaríaYánnez ;
    ex:hasSibling ex:MenénSuáriz ;
    ex:hasChild ex:AlffonsoFernándiz .

ex:SuerPérez a ex:Person ;
    ex:hasFirstName "Suer" ;
    ex:hasLastName "Pérez" ;
    ex:hasSpouse ex:MaríaYánnez ;
    ex:hasChild ex:MenénSuáriz, ex:AldonçaSuáriz .

ex:MaríaYánnez a ex:Person ;
    ex:hasFirstName "María" ;
    ex:hasLastName "Yánnez" ;
    ex:hasSpouse ex:SuerPérez ;
    ex:hasChild ex:MenénSuáriz, ex:AldonçaSuáriz .

ex:IohanGonçáliz a ex:Person ;
    ex:hasFirstName "Iohan" ;
    ex:hasLastName "Gonçáliz" ;
    ex:hasParent ex:GonçaloSuáriz, ex:MaríaÁlvariz .

ex:GonçaloSuáriz a ex:Person ;
    ex:hasFirstName "Gonçalo" ;
    ex:hasLastName "Suáriz" ;
    ex:hasSpouse ex:MaríaÁlvariz ;
    ex:hasChild ex:IohanGonçáliz .

# Places
ex:Borondés a ex:Place .
ex:Oviedo a ex:Place .
ex:Vallongo a ex:Place .
ex:Grado a ex:Place .
```
A estudiar: posible pipeline de retroalimentación con Wikidata para el enriquecimiento tanto de nuestro grafo como de Wikidata...
