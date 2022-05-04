# SPARQL

So I will appolgise now for the roughness of this part of the demo, I've gone over many different approaches, Sql comparissions, huge spaning data but at 7am this morning I noticed that it went above and beyond what should be in a lunch and learn 

Lets just get into this, I'm using WikiData, as we are currently reviewing the model that we're building for the Gateway replacement and as said I ejected a large part of this demo at 7am, so needed a instressing data source. 


So hopely this looks familar


[Link](https://query.wikidata.org/#PREFIX%20rdfs%3A%20%3Chttp%3A%2F%2Fwww.w3.org%2F2000%2F01%2Frdf-schema%23%3E%0APREFIX%20wd%3A%20%3Chttp%3A%2F%2Fwww.wikidata.org%2Fentity%2F%3E%0APREFIX%20wdt%3A%20%3Chttp%3A%2F%2Fwww.wikidata.org%2Fprop%2Fdirect%2F%3E%0A%0ASELECT%20%2a%20WHERE%20%7B%0A%20%20%3FSubject%20%3FPredicate%20%3FObject%20.%0A%7D%20Limit%2010)

```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>

SELECT * WHERE {
  ?Subject ?Predicate ?Object .
} Limit 10
```

So 


```
  ?painting wdt:P31|wdt:P279 wd:Q3305213 ;
```

Ok cool we now have all the images ini wikidata. Note we've only got the identifer node beacuse thats what we asked for 

and that thats note helpful, normally we'd querry and return the labels in the right lanugages, but wikidata has an inbuilt service, so wie'll change our code to this:
```
SELECT DISTINCT ?paintingLabel WHERE {
  ?painting wdt:P31|wdt:P279 wd:Q3305213 ;
    SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }    
}
```

post fixing the ?selection with Label, we'll no get the label value of the item,

 What about a view of the image? there could be copyright issues, so Wikidata might not have an image for all.

 ```
OPTIONAL { ?painting wdt:P18 ?image } .
```

Cool that now returns images, we can also change the view in wikipedia to show as an image grid, we can even set the query tool to set it by default

```#defaultView:ImageGrid```

Cool lets get some details about the painter, so we need to select the painter

```
;
            wdt:P170 ?painter. 
```

Now we only intrested in painters with "country of citizenship" "United Kingdom of Great Britain and Ireland"


OK, now i want to mix it up, I only want pictures of thing on the nhle,

SO I'm going to write a sub query!

```
  { SELECT ?designation WHERE { ?designation wdt:P1216 ?nhle. } }
```
then I'm going to select set the depiction

```wdt:P180 ?designation; ```

Cool,

```
  wdt:P276 ?location .
  ?location wdt:P625 ?point .
```

Again Now we can see on a map where they are currently housed,

what about when they where painted?

```
    wdt:P571 ?date;
```

change to timeline,

We can also find out if more men paint NHLE records than women

```
SELECT ?genderLabel (COUNT(*) as ?count) WHERE {
  ?painting wdt:P31 wd:Q3305213;
    wdt:P180 ?designation;
    wdt:P571 ?date;
    wdt:P170 ?painter.
  ?painter wdt:P27 wd:Q174193 ;
           wdt:P21 ?gender.
  { SELECT ?designation WHERE { ?designation wdt:P1216 ?nhle. } }
  OPTIONAL { ?painting wdt:P18 ?image. }
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
} Group by ?genderLabel
```

```
SELECT DISTINCT ?painting ?paintingLabel ?image ?painterLabel ?date ?genderLabel WHERE {
  ?painting wdt:P31 wd:Q3305213;
    wdt:P180 ?designation;
    wdt:P571 ?date;
    wdt:P170 ?painter.
  ?painter wdt:P27 wd:Q174193 ;
           wdt:P21 ?gender.
  { SELECT ?designation WHERE { ?designation wdt:P1216 ?nhle. } }
  OPTIONAL { ?painting wdt:P18 ?image. }
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
} Group by ?gender
```


Finally fenderated Querries: 
```
PREFIX wikibase: <http://wikiba.se/ontology#>  
PREFIX wd: <http://www.wikidata.org/entity/> 
PREFIX wdt: <http://www.wikidata.org/prop/direct/> 
PREFIX bd: <http://www.bigdata.com/rdf#> 
PREFIX HAR: <http://PaulsDemo.com/ns/HAR#>  
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#> 
PREFIX Designation: <http://PaulsDemo.com/ns/Designation#>  
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>  
PREFIX Risk: <http://PaulsDemo.com/ns/Risk#>  

SELECT DISTINCT ?Subject ?NHLE_Number ?image  ?SubjectLabel ?OwnerLabel  ?har ?PublishedName  ?LEN ?riskName ?OnReg  WHERE { 
        SERVICE <https://query.wikidata.org/sparql> { SELECT DISTINCT ?Subject ?NHLE_Number (group_concat(distinct ?images;separator="; ") as ?image) ?SubjectLabel ?OwnerLabel  WHERE { 
            ?Subject wdt:P1216 ?NHLE_Number ;  
                     wdt:P18|wdt:P117 ?images ; 
                            wdt:P127 ?Owner .  
            SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". } 
        } GROUP BY ?NHLE_Number ?Subject ?SubjectLabel ?OwnerLabel limit 6  
    }  
    
    ?des Designation:LEN ?NHLE_Number. 
    ?har HAR:Designation ?des ; 
         HAR:PublishedName ?PublishedName ; 
         HAR:ONHAR ?OnReg; 
         HAR:RiskLevel ?risk.  
    
    ?des Designation:LEN ?LEN. 
    ?risk  Risk:name ?riskName.
} 
```


This one works much like the Sub query, we go off to the wikidata, pull the records back and joins them..... There are far more complex.

Finally Thank you for listening to me waffle 