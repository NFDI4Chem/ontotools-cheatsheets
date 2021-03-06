# SPARQL cheatsheet 

## FILTER

### FILTER EXISTS
Returns graph patterns that 
* Match the condition. `FILTER EXISTS {?subject rdfs:range rdfs:Literal}`
* Do not match the condition `FILTER NOT EXISTS {?subject rdfs:range rdfs:Literal}`
Example: return all the subjects with rdfs:range rdfs:Literal
```SPARQL
SELECT DISTINCT ?subject ?predicate ?range
WHERE {
    ?subject rdf:type ?predicate.
    ?subject rdfs:range ?range.
    FILTER EXISTS {?subject rdfs:range rdfs:Literal} 
}
``` 


### FILTER blank nodes: [isBlank](https://www.w3.org/TR/rdf-sparql-query/#func-isBlank)

* filter in only bank nodes `FILTER (isBlank(?term))` 
* filter out `FILTER (!isBlank(?term))` 
``` SPARQL
SELECT DISTINCT ?subject ?predicate
WHERE {
    ?subject rdf:type ?predicate.
    FILTER (isBlank(?subject)) 
}
```


### FILTER [isIRI](https://www.w3.org/TR/rdf-sparql-query/#func-isIRI)


### FILTER [isLiteral](https://www.w3.org/TR/rdf-sparql-query/#func-isLiteral)


### FILTER [lang](https://www.w3.org/TR/rdf-sparql-query/#func-lang)
Returns the language tag. 

Example: filter only ?label with language tag `@es`
`FILTER(lang(?label) = 'es')`

`FILTER(lang(?label) = 'es')`

Example: filter only ?label with *any* `@en` variant (will match all of these: `@en`, `@en-US`, `@en-GB`)
`FILTER(langMatches(lang(?label), 'en'))`


### FILTER [datatype](https://www.w3.org/TR/rdf-sparql-query/#func-datatype)


## String Operations
* `UCASE()` convert to upper-case 
* `LCASE()` convert to lower-case
* `STRLEN()` lenght of a string
* `SUBSTR()` substring of first arg, second arg: start position; third arg: character lenght; 
* `STRSTARTS()`string in first arg starts with string in second arg
* `STRENDS()`  string in first arg ends with string in second arg
* `CONTAINS()` string in second arg is within first arg. Example: `CONTAINS("SPARQL", "QL")` is True. `FILTER(CONTAINS(str(?term), "iao") || CONTAINS(str(?term), "bfo") || CONTAINS(str(?term), "obo")) ` 
* `REPLACE()` replaces 1st agument (input) that matches the 2nd argument with the 3rd argument. Example: `REPLACE(?contact_email_tmp_str, "mailto:", "")` replacing with nothing. 
    * The result can be bound to a new variable: `BIND(REPLACE(?contact_email_tmp_str, "mailto:", "") AS ?contact_email) .`

## Data Conversions
**Uses xsd: daatatypes**

For instance a URI converted to a string:
`BIND(xsd:string(?contact_email_tmp) AS ?contact_email_tmp_str) .`

Or a number in a string `"100"` converted to an integer:
`BIND(xsd:integer(?numb_str) AS ?numb) .`



## Aggregation Functions
* `COUNT`
* `SUM`
* `AVG`
* `MIN`, `MAX`
* `SAMPLE`
* `GROUP_CONCAT`

----

# SPARQL Resources
* DuCharme, Bob. Learning SPARQL: Querying and Updating with SPARQL 1.1. Second edition. Sebastopol, CA: O’Reilly Media, 2013.
* “SPARQL 1.1 Query Language.” https://www.w3.org/TR/sparql11-query/.
* “Bob DuCharme Blog.” http://www.bobdc.com/blog/.
* “Module: SPARQL — Web Portal for RDF.Rb.” http://rdf.greggkellogg.net/yard/SPARQL.html.
* “Wikidata:Request a Query - Wikidata.” https://www.wikidata.org/wiki/Wikidata:Request_a_query.





