# [ROBOT is an OBO Tool](http://robot.obolibrary.org/)
> ROBOT is a tool for working with Open Biomedical Ontologies. It can be used as a command-line tool or as a library for any language on the Java Virtual Machine.

## Convert
[Official documentation](http://robot.obolibrary.org/convert)

Convert an ontology between different formats: owl, ttl, omn, obo. 

The input and output format is determined by the file extension.

```sh
robot convert --input ontology.ttl --output ontology.owl
```


## Extract terms form one ontology onto another
[Official documentation](http://robot.obolibrary.org/extract)

```sh
robot extract --method BOT --input source_onto.owl --term-file terms.txt --output destination_onto.owl
```


**--method** defines the method for importing the terms, which can be:
* BOT or BOTTOM: *takes a view from the BOTTOM of the class-hierarchy upwards*. Importing the term request and super-classes and the inter-relations.
* TOP: *takes a view from the TOP of the class-hierarchy downwards* 
* SMART: *terms ... and the inter-relations between them (not necessarily sub- and super-classes)*


**--term-file**
is a file containing the IRIs of the terms to be imported from the source ontology to the destination ontology

Example `--term-file` file:
```
http://purl.obolibrary.org/obo/IAO_0000004 # has measurement value
http://purl.obolibrary.org/obo/IAO_0000404 # has x coordinate value
```






