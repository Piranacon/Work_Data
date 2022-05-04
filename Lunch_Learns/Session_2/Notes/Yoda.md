# RDF WalkThrough 

**Note:** I'm going to model some Star Wars Data! this is deliberate!! I'm not doing this to be silly, but I've found model data is like talking politics, it gets messy quicky and people suddenly want to prove the very simple model is WRONG..... In fact in my notes I've even writen WRONG in capitals
So


``` 
Yoda Visited Korriban 
```



We're Starting with the Subject, the thing we're going to describe for this we need So we could use:

```
<Starwars:yoda>  
```

But this is silly we know Yoda, so we can link to his identifier in Wikidata, Lets start as we mean to go on ans use his wikidata page. 

```
<https://www.wikidata.org/wiki/Q51730>
```

Remeber this doesn't pull data in, this is a way of indenitifying it in our system. The fact that it links is an added bonus.
Infact lets follow the link.

The cool thing here is there is more links!!!!

---
Next we're going to add the prediate. This is the will be  



```
<http://www.example.org/Paul/Schema/Actions#Visited>
```


```
"Korriban"
```

But Korriban is a thing!! We can "name it" so:
```
<https://www.wikidata.org/wiki/Q2085181>
```

Finally we have to say that this object is done with. So add our full stop.

```
<https://www.wikidata.org/wiki/Q51730>
    <http://www.example.org/Paul/Schema/Actions#Visited> <https://www.wikidata.org/wiki/Q2085181> .
```

So lets add a few more people Also Visited 
```
<https://www.wikidata.org/wiki/Q51746>  #Luke
    <http://www.example.org/Paul/Schema/Actions#Visited> <https://www.wikidata.org/wiki/Q2085181> .

<https://www.wikidata.org/wiki/Q51770>  #Palps
    <http://www.example.org/Paul/Schema/Actions#Visited> <https://www.wikidata.org/wiki/Q2085181> .

<https://www.wikidata.org/wiki/Q51764>  #Bane
    <http://www.example.org/Paul/Schema/Actions#Visited> <https://www.wikidata.org/wiki/Q2085181> .
```

No we get into an intresting discusssion, this is horrible to read. to which you'll say "Paul Linked data is Machine first, it does not matter that you can't read it. 
No it doesn't but if we keep going this way it will be unreadable and use more disk space to store and its my demo!!

So we're going to introduce a "Prefix" 

```
@prefix wd:<https://www.wikidata.org/wiki/> .

wd:Q51730
    <http://www.example.org/Paul/Schema/Actions#Visited> wd:Q2085181.
```

Developers will know, but for every one else when the parser scans the text when it sees "wd:" it will change the prefix from wd to  https://www.wikidata.org/wiki/. Thus giving us the same fuctionalliy but in less words.

we can also change the predicate as well
```
@prefix wd:<https://www.wikidata.org/wiki/> .
@prefix actions: <http://www.example.org/Paul/Schema/Actions#> .

wd:Q51730
    actions:Visited wd:Q2085181.
```
Nice, so in the vain of its my demo, in my data store I want to better model

``` 
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .

wd:Q51730
    actions:Visited wd:Q2085181 .
    
wd:Q51730
    rdfs:comment "Documenting Yodas visit"@en .
```
Thats Valid!! but we can do better, and save space and make it readable

```
@prefix wd:<https://www.wikidata.org/wiki/> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .

wd:Q51730
    actions:Visited wd:Q2085181 ;
    rdfs:comment "Documenting Yodas visit"@en .
```
Notice the Semi-Colon? thats telling the parser "we've defined a predict and object but there more to add"

Next I want to make my data more structured for me, so I'm goint to use the FOAF Friend of a Freind onotly to enhance my data. an Onotly describes the Data, and how the data joins

```
@pfrefix foaf: <http://xmlns.com/foaf/0.1/> .
    a foaf:person ; 
    foaf:Name : "Yoda" ;
```

Now someone is going to say but yoda isn't a person!! but if we follow the like to the spec, we can see they don't nit pick

Finally we'll make some changes because I want to list other place hes been

```
@prefix Location: <http://www.example.org/Paul/Schema/Places#> .

 actions:Visited [ location:Planet wd:Q2085181;  rdfs:comment "Documenting Yodas visit"@en ], [location:mountain <https://starwars.fandom.com/wiki/Monument_Plaza> ] .

```

This is a list of blank nodes.
```
@prefix wd:<https://www.wikidata.org/wiki/> .
@prefix foaf: <http://xmlns.com/foaf/0.1/> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix Location: <http://www.example.org/Paul/Schema/Places#> .
@prefix actions: <http://www.example.org/Paul/Schema/Actions#> .

wd:Q51730
a foaf:person ;
    foaf:name "Yoda" ;
    actions:Visited [ 
        location:Planet wd:Q2085181;  
        rdfs:comment "Documenting Yodas visit"@en 
    ], [
        location:mountain <https://starwars.fandom.com/wiki/Monument_Plaza> ;
        rdfs:comment "Documenting Yodas visit"@en 
    ] .
```
And that is the Crash course in RDF
