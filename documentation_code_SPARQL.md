## code SPARQL utiliser pour extraire la liste des informations disponibles dans Wikidata ou autres sources

```sparql
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wikibase: <http://wikiba.se/ontology#>
PREFIX bd: <http://www.bigdata.com/rdf#>

SELECT ?p ?propLabel ?eff
WHERE {
  {
    SELECT ?p (COUNT(*) AS ?eff)
    WHERE {
      ?item wdt:P31 wd:Q5;               # humain
            wdt:P569 ?birthDate;         # a une date de naissance
            wdt:P1344 wd:Q362.           # a participé à la Seconde Guerre mondiale
      
      ?item ?p ?o.                       # toutes les propriétés utilisées

      BIND(REPLACE(str(?birthDate), "(.*)([0-9]{4})(.*)", "$2") AS ?year)
      FILTER(xsd:integer(?year) >= 1800 && xsd:integer(?year) <= 1950)
    }
    GROUP BY ?p
  }

  ?prop wikibase:directClaim ?p .
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],fr,en". }
}
ORDER BY DESC(?eff)


```
