# AGIFT / CRS Thesaurus Linkset
This code repository contains a Linkset - a specialised Dataset linking objects in two other Datasets.

This Linkset contains [SKOS mapping properties](https://www.w3.org/TR/skos-reference/#mapping) between Concepts in the [AGIFT](http://data.naa.gov.au/def/agift) and Concepts in the [CRS Thesaurus](https://github.com/CSIRO-enviro-informatics/crsth) which are both vocabularies of Australian government functions.

### Link examples
The Concept "Advertising standards" in AGIFT has a broader match in the CRS Thesaurus with its Concept "advertising" so the link between the two, in this Linkset is as follows:

`agift:Advertising-standards skos:closeMatch crsth:advertising .`

Where the URIs for "Accommodation services" & "accommodation" are used and the SKOS mapping property.

Another example is where "Administrative-law" in AGIFT is the same as "administrative-law" in the CRS Thesaurus hence:

`agift:Administrative-law skos:exactMatch crsth:administrative-law .`

Finally, AGIFT's "Accommodation services" is similar to, but not exactly the same as CRS Thesaurus' "accomodation" hence:

`agift:Accommodation-services skos:closeMatch crsth:accommodation .`


### Linkset definition
The formal definition of what this form of a Linkset is, is provided by the [Location Index project (Loc-I)](http://locationindex.org/)'s within its project ontology, see:

* http://linked.data.gov.au/def/loci


## Repository Contents  
* [data.ttl](data.ttl) - this Linkset’s main data file. It is an RDF turtle file and contains individual links and also the contens of header.ttl
* [header.ttl](header.ttl) - this Linkset’s data.ttl header information, stored separately for ease of access
* [example-data.ttl](example-data.ttl) - 5 Statements from the Linkset for ease of access, in RDF (turtle) format, as per the main data file but none of the whole-of-Linkset information
* [example-data-unreified.ttl](example-data-unreified.ttl) - 5 Statements from the Linkset but flattened, 'unreified', for even easier use
* [README.md](README.md) - this file
* [LICENSE](LICENSE) - the data license assigned to this Linkset’s content
* [methods/](methods/) - a folder containing information (prose and code) about how this Linkset was generated


## Linkset description

### Generation
This linkset was generated by a partly automated and partly manual process which is described in [methods/](methods/). The method described
there is also linked to from the Linkset's data.ttl and header.ttl files.

### Link structure
While there are example of links above, they are simplified compared with what this Linkset actually contains. A more powerfule link
structure is used so this Linkset can contain more information per link than just the relations (the type of match, like `skos:exactMatch`).

A straightforward link, as per the example above, would be represented by RDF triples like this:

`<Thing_A> <relation_x> <Thing_B>` where the *subject* <Thing_A> and *object* <Thing_B> of the triple would be `skos:Concepts` in AGIFT (A)
and the CRS Thesaurus (B). The *predicate* <relation_x> would be one of the SKOS mapping properties, `skos:mappingRelation`,  
`skos:closeMatch`, `skos:exactMatch`, `skos:broadMatch`, `skos:narrowMatch` or `skos:relatedMatch` which are the only *predicates* that this
Linkset contains.

However, we wish to associate other information with the mapping, such as for what time periods it applies, who asserted the relation etc.
So, to do this we apply *[reification](http://patterns.dataincubator.org/book/reified-statement.html)* to the link by creating a container
that then contains the triple's parts and other parts. The container, an `rdf:Statement` object, for the above would look like this:

```
:statement_n
  a rdfs:Statement ;
  rdf:subject <Thing_A> ;
  rdf:predicate <relation_x> ;
  rdf:object <Thing_B>
  # other properties
.  
```

If we wanted to indicate when a link was made, say April the 5th, 2019 we could express its creation date like this:

```
:statement_n
  a rdfs:Statement ;
  rdf:subject <Thing_A> ;
  rdf:predicate <relation_x> ;
  rdf:object <Thing_B>
  dct:created "2019-04-05"^^xsd:date ;
.  
```

That `dct:created` property applies to the actual link statement (the `:statement_n` object above). This would be different to saying  
something like "we, the creators of this link assert that it is true for the time period Christmas Day, 2001, onwards" which could be  
encoded like this:

```
:statement_n
  a rdfs:Statement ;
  rdf:subject <Thing_A> ;
  rdf:predicate <relation_x> ;
  rdf:object <Thing_B>
  dct:created "2019-04-05"^^xsd:date ;
  time:hasTime [
    time:hasBeginning [
      time:inXSDDate "2001-12-25"^^xsd:date ;
    ] ;
    # if this had an end time, a time:hasEnd property could be placed here
  ] ;
.  
```

Here the `time:hasTime` property is indicating the time period for which this statement is true *in the real world* or, put differently,
the time period for which this statement represents reality.

### Complete link example
A complete example of a link from this Linkset is, echoing a short example at the top of this README file:

```
@prefix : <http://linked.data.gov.au/linkset/agiftcrsth/statement/> .
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix dct: <http://purl.org/dc/terms/> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix time: <http://www.w3.org/2006/time> .
@prefix skos: <http://www.w3.org/2004/02/skos/core#> .
@prefix agift: <https://data.naa.gov.au/def/agift/> .
@prefix crsth: <http://linked.data.gov.au/def/crsth/> .
@prefix loci: <http://linked.data.gov.au/def/loci#> .
@prefix m1: <http://linked.data.gov.au/linkset/agiftcrsth/method/1> .

:s6
  a rdf:Statement ;
  rdf:subject agift:Administrative-law ;
  rdf:predicate skos:exactMatch ;
  rdf:object crsth:administrative-law ;
  dct:created "2019-04-05"^^xsd:date ;
  loci:validFor [  # the claim here is that this relation
                   #has been true since the Federation of Australia
    time:hasBeginning [
      time:inXSDDate "1901-01-01"^^xsd:date ;
    ] ;
    # it is still true so no time:hasEnd property given (open-ended time interval)
  ] ;
  loci:hadGenerationMethod m1: ; # the method used to generate this link
                                 #is the same method for all links in this Linkset
.
```

### Linkset statistics
These statistics were calculated using SPARQL queries against the data in data.ttl stored in a triplestore. Some statistics require more information than just that in data.ttl: AGIFT & CRS Thesaurus themselves are needed for Statistic 2+.

No. | Statistic | Value
--|--|--
1. | No. of Links | 410
2. | Proportion of AGIFT Concepts linked | 0.45 (261/583)
3. | Proportion of CRS Th Concepts linked | 0.33 (239/727)
4. | Counts of Link type | `skos:exactMatch`: 66<br />`skos:closeMatch`: 33<br />`skos:croadMatch`: 221<br />`skos:narrowMatch`: 94


## Rights & License
The content of this repository is owned and published by the [National Archives of Australia](http://naa.gov.au) and licensed for use under the [Creative Commons 4.0 License](https://creativecommons.org/licenses/by/4.0/). See the [license deed](LICENSE) all details.


## Contacts
*Product owner:*  
**David Hearder**  
National Archives of Australia  
<david.header@naa.gov.au>  

*Linkset creator and technical contact:*  
**Nicholas Car**  
CSIRO Land & Water, Environmental Informatics Group  
<nicholas.car@csiro.au>  
