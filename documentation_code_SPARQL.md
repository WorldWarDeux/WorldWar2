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


## Question 1 : Évolution temporelle selon le rôle

```sparql
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wikibase: <http://wikiba.se/ontology#>
PREFIX bd: <http://www.bigdata.com/rdf#>

SELECT ?person ?personLabel ?birthYear ?roleLabel WHERE {
  ?person wdt:P31 wd:Q5;
          wdt:P1344 wd:Q362;
          wdt:P106 ?role.
  OPTIONAL { ?person wdt:P569 ?birth. BIND(YEAR(?birth) AS ?birthYear) }

  SERVICE wikibase:label { bd:serviceParam wikibase:language "fr,en". }
}

```


## Question 2 : Analyse bivariée Pays × Rôle (test du Chi-2)

```sparql
SELECT ?person ?personLabel ?countryLabel ?roleLabel WHERE {
  ?person wdt:P31 wd:Q5;
          wdt:P1344 wd:Q362;     # A participé à la SGM
          wdt:P27 ?country;      # Nationalité
          wdt:P106 ?role.        # Profession / rôle
          
  SERVICE wikibase:label { bd:serviceParam wikibase:language "fr,en". }
}
LIMIT 1000
```

## Question 3 : Analyse de réseau (co-appartenance à une organisation)

```sparql
SELECT ?person ?personLabel ?orgLabel WHERE {
  ?person wdt:P31 wd:Q5;
          wdt:P1344 wd:Q362;
          wdt:P463 ?org.    # Membre d’une organisation
          
  SERVICE wikibase:label { bd:serviceParam wikibase:language "fr,en". }
}
LIMIT 1000

``
