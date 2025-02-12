Bueno, creo que todo el paradigma acaba de cambiar por completo ya que de repente ha surgido un LLM que *sí* parece capaz de procesar textos del medievo (ya era hora). Supongo que Deepseek no ha hundido la bolsa estadounidense por nada.

En fin, que aportando como entrada el siguiente comando
```
Extract all entities and relations from the following text fragments from a 14th century Spanish corpus. The results must be in Turtle format,conforming to the classes and atributes described in the Shape Expression i provide.

prefix : <http://example.org/>
prefix xsd: <http://www.w3.org/2001/XMLSchema#>
prefix owl: <https://w3id.org/MON/person.owl#>

:Person CLOSED {
  owl:firstName xsd:string ?;
  owl:lastName xsd:string ?;
  owl:nickname xsd:string ?;
  owl:auntOf @:Person *;
  owl:childInLawOf @:Person *;
  owl:childOf @:Person *;
  owl:cousinOf @:Person *;
  owl:fatherInLaw @:Person *;
  owl:fatherOf @:Person *;
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

:Property {
  :name xsd:string ;
}

:NotarialEvent {
  :type ["Sale" "Disembargo" "Retrosale" "Donation" "Lease" "Compromise" "Testimony" "Relocation"];
  :date xsd:date;
  :concerns :Property *;
  :grantor :Person +;
  :recipient :Person +;
  :notary :Person +;
  :scribe :Person *;
  :witness :Person *;
  :location :Location ?;
}
```
y un fragmento complejo de las notarías, por ejemplo:

"Connosçida cosa sea a quantos esta carta viren cómmo yo Menén Suáriz, morador en Borondés, e yo Loriença Suáriz, sua muller, dessenbargamos a vos Alffonso Fernándiz, oryz, morador enna çipdat de Oviedo, fillo de Aldonça Suáriz, hermana de mí, Menén Suáriz, e a vuestra muller María Álvariz, el sétimo de todos quantos heredamientos e lantados avían Suer Pérez e María Yánnez, padre e madre que foron de mi, Menén Suáriz, e de la dicha Aldonça Suáriz, vuestra madre, en Borondés e en Vallongo e en sos términos e en la alffoz de Grado, huquier que llos pertenesçían, tan bien los que ora están partidos commo por partir, el qual sétimo vos pertenez por nome de la dicha Aldonça Suáriz, vuestra madre. Otrossí vos dessenbargamos el medio sétimo de los dichos heredamientos e lantados que son ennos dichos lugares tan bien partidos commo por partir, el qual medio sétimo yera de Iohan Gonçáliz, vuestro quarmano, fillo de Gonçalo Suáriz e de María Álvariz. E otorgamos e prometemos a bona fedt e sen enganno de vos non enbargar esto sobredicho nin parte dello per nos nin per otri de que en delantre en algún tienpo nin en alguna manera. E si contra ello passarmos, obligámosnos de vos dar e pagar por nomne de interese e de postura que convusco ponemos çinquaenta maravedís de la moneda del rey don Fernando a onze dineros menos terçia de un dinero el maravedí, per nos e per nuestros bienes, assí moble commo rayz. E el interesse e postura pagado o non, esto sobredicho que vala para siempre. E por siguridat de vos e que aiades esto dessenbargado e vos paguemos la dicha postura e interesse si nos contra esto pasarmos, dámosvos por fiador e debdor e prinçipal pagador a Suer Díaz de Cortina, sobrino de Garçía Suáriz, nos e él cada uno de nos por el todo. E yo Suer Díaz, fiador ia dicho, assí lo otorgo per mí e per mios bienes. E tan bien nos, Menén Suáriz e Loriença Suáriz, commo yo, Suer Díaz, obligámosnos que si demandas o demanda ovierdes vos, Alffonso Fernándiz o vuestra muller o otri alguno contra nos o contra qualquier de nos por algunas destas razones, obligamos de vos conplir e fazer derecho per quales iustiçias vos mays quissierdes, espiritales o tenporales. Fecha la carta tres días de genero, era de mille e trezientos e sesaenta e çinco annos. E desto Alffonso Fernándiz pidió un póblico esturmento para sua garda. Testigos: Fernán Fernándiz, escudero, e Gonçalo Rodríguiz, fillo de Rodrigo Martíniz; e Fernán Sirgo de Vallo, e Suer Rodríguiz, escripván; e Fernán Garot, Alffonso Pérez de San Romano, e otros. Yo, Suer Rodríguiz, la escripví e fuy presente por mandado de Pero Fernándiz, notario póblico del rey enna pobla de Grado. Yo Pero Fernándiz, notario sobredicho, fuy presente e fizi escrevir esta carta, e fizi en ella mio singno (S)."

  Obtenemos el siguiente resultado:

```
@prefix : <http://example.org/> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix owl: <https://w3id.org/MON/person.owl#> .

### Persons
:MenénSuáriz a :Person ;
  owl:firstName "Menén" ;
  owl:lastName "Suáriz" ;
  owl:livesIn :Borondés ;
  owl:spouseOf :LoriençaSuáriz ;
  owl:childOf :SuerPérez, :MaríaYánnez ;
  owl:siblingOf :AldonçaSuáriz ;
  owl:uncleOf :AlffonsoFernándiz .

:LoriençaSuáriz a :Person ;
  owl:firstName "Loriença" ;
  owl:lastName "Suáriz" ;
  owl:spouseOf :MenénSuáriz .

:AlffonsoFernándiz a :Person ;
  owl:firstName "Alffonso" ;
  owl:lastName "Fernándiz" ;
  owl:livesIn :Oviedo ;
  owl:childOf :AldonçaSuáriz ;
  owl:spouseOf :MaríaÁlvariz ;
  owl:cousinOf :MenénSuáriz .

:MaríaÁlvariz a :Person ;
  owl:firstName "María" ;
  owl:lastName "Álvariz" ;
  owl:spouseOf :AlffonsoFernándiz .

:AldonçaSuáriz a :Person ;
  owl:firstName "Aldonça" ;
  owl:lastName "Suáriz" ;
  owl:childOf :SuerPérez, :MaríaYánnez ;
  owl:siblingOf :MenénSuáriz ;
  owl:motherOf :AlffonsoFernándiz .

:SuerPérez a :Person ;
  owl:firstName "Suer" ;
  owl:lastName "Pérez" ;
  owl:spouseOf :MaríaYánnez ;
  owl:fatherOf :MenénSuáriz, :AldonçaSuáriz .

:MaríaYánnez a :Person ;
  owl:firstName "María" ;
  owl:lastName "Yánnez" ;
  owl:spouseOf :SuerPérez ;
  owl:motherOf :MenénSuáriz, :AldonçaSuáriz .

:IohanGonçáliz a :Person ;
  owl:firstName "Iohan" ;
  owl:lastName "Gonçáliz" ;
  owl:childOf :GonçaloSuáriz, :MaríaÁlvariz ;
  owl:cousinOf :AlffonsoFernándiz .

:GonçaloSuáriz a :Person ;
  owl:firstName "Gonçalo" ;
  owl:lastName "Suáriz" ;
  owl:spouseOf :MaríaÁlvariz ;
  owl:fatherOf :IohanGonçáliz .

:SuerDíazDeCortina a :Person ;
  owl:firstName "Suer" ;
  owl:lastName "Díaz" ;
  owl:nickname "de Cortina" ;
  owl:nephewOf :GarçíaSuáriz ;
  owl:fiadorOf :MenénSuáriz, :LoriençaSuáriz .

:GarçíaSuáriz a :Person ;
  owl:firstName "Garçía" ;
  owl:lastName "Suáriz" ;
  owl:uncleOf :SuerDíazDeCortina .

### Locations
:Borondés a :Location ;
  :name "Borondés" .

:Oviedo a :Location ;
  :name "Oviedo" .

:Vallongo a :Location ;
  :name "Vallongo" .

:Grado a :Location ;
  :name "Grado" .

### Notarial Event
:DisembargoEvent a :NotarialEvent ;
  :type "Disembargo" ;
  :date "1365-01-03"^^xsd:date ;
  :concerns :Heredamientos ;
  :grantor :MenénSuáriz, :LoriençaSuáriz ;
  :recipient :AlffonsoFernándiz, :MaríaÁlvariz ;
  :notary :PeroFernándiz ;
  :scribe :SuerRodríguiz ;
  :witness :FernánFernándiz, :GonçaloRodríguiz, :FernánSirgoDeVallo, :FernánGarot, :AlffonsoPérezDeSanRomano ;
  :location :Grado .

### Property
:Heredamientos a :Property ;
  :name "Heredamientos e lantados en Borondés, Vallongo, e Grado" .

### Notary and Scribe
:PeroFernándiz a :Person ;
  owl:firstName "Pero" ;
  owl:lastName "Fernándiz" ;
  owl:notaryOf :DisembargoEvent .

:SuerRodríguiz a :Person ;
  owl:firstName "Suer" ;
  owl:lastName "Rodríguiz" ;
  owl:scribeOf :DisembargoEvent .

### Witnesses
:FernánFernándiz a :Person ;
  owl:firstName "Fernán" ;
  owl:lastName "Fernándiz" ;
  owl:witnessOf :DisembargoEvent .

:GonçaloRodríguiz a :Person ;
  owl:firstName "Gonçalo" ;
  owl:lastName "Rodríguiz" ;
  owl:witnessOf :DisembargoEvent .

:FernánSirgoDeVallo a :Person ;
  owl:firstName "Fernán" ;
  owl:lastName "Sirgo" ;
  owl:nickname "de Vallo" ;
  owl:witnessOf :DisembargoEvent .

:FernánGarot a :Person ;
  owl:firstName "Fernán" ;
  owl:lastName "Garot" ;
  owl:witnessOf :DisembargoEvent .

:AlffonsoPérezDeSanRomano a :Person ;
  owl:firstName "Alffonso" ;
  owl:lastName "Pérez" ;
  owl:nickname "de San Romano" ;
  owl:witnessOf :DisembargoEvent .
```
A estudiar: 
* Posible pipeline de retroalimentación con Wikidata para el enriquecimiento tanto de nuestro grafo como de Wikidata...
* Impacto de Shex/SHACL como mecanismo para las LLM...?
* Zero-shot vs few-shot con framework de shapes.
