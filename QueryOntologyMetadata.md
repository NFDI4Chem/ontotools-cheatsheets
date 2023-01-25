# Ontology commandline hacks

The following examples might come in useful if somebody creates, say, 
a spreadsheet table with information about a number of ontologies. 

Some of that information and metadata is available from multiple places, 
such as the OBOfoundry collection of ontologies, or portals like the 
[NCBO BioPortal](bioportal.bioontology.org/), 
the [Ontology Lookup Service (OLS)](https://www.ebi.ac.uk/ols/)
or the [Terminology Services](https://service.tib.eu/ts4tib/) in several NFDI consortia.

You probably would like to avoid manual copy&paste across all these, 
so here are some code snippets to query and extract the metadata 
through / from these services. *Some* effort was made to ensure that 
the output has one line per given ontology.

### Dependencies

The code snippets started as (longish) one-liners, and were reformatted here 
for better readability. They use common (linux) command line tools.

The `jq` and `yq` are JSON and Yaml swiss-army knifes (similar to xq for XML) 
and a nice way to extract, query and pretty-print JSON and yaml, 
see https://blog.lazy-evaluation.net/posts/linux/jq-xq-yq.html ,
https://stedolan.github.io/jq/ and https://github.com/mikefarah/yq/.

## Bioportal

### Get Bioportal Categories (keywords) 
You will need an APIkey, create an account at Bioportal 
and go to your account profile https://bioportal.bioontology.org/account
to get your real `apikey=........-....-....-....-............"` 
which is made up from groups of letters and numbers.

The following snippet will query the Bioportal `categories` (think: keywords) for each ontology,
and create a comma-separated list, and an empty line if none is listed. 

```
export APIKEY=........-....-....-....-............
for ONTO in PATO PO AGRO EO TO NCBITAXON OBI EFO CHMO MS CHEBI GO UO EDAM SWO ; do 
  echo -n -e "ONTO=$ONTO\t"; 
  curl -s --fail-with-body "https://data.bioontology.org/ontologies/$ONTO/categories?apikey=$APIKEY" |\
  jq 'map(.name) | join(", ")' 2>/dev/null || echo NOT_ON_BIOPORTAL
done | tr -d \" | cut -f 2
```

## OBOFoundry

### Get Ontology License from OBOFoundry metadata

Some OBOFoundry ontology metadata is available in combined Yaml+markdown files, 
one for each ontology, in the OBOFoundry github repository.
Note that filenames are in lower case. `awk` is used to extract the Yaml parts, 
and `yq` to extract the wanted content. 

```
for ONTO in PATO PO AGRO EO TO NCBITAXON OBI EFO CHMO MS CHEBI GO UO EDAM SWO ; do
  wget -q -O- https://raw.githubusercontent.com/OBOFoundry/OBOFoundry.github.io/master/ontology/$(echo $ONTO | tr '[:upper:]' '[:lower:]').md |\
  awk 'BEGIN {RS="---"} FNR==2 {print $0}' |\
  yq '.license.label'; 
done | sed -e 's/^null//'
```

### Get Domain (keywords) from OBOFoundry metadata
Similar to the above, extracting the `domain` field (think: keywords).
```
for ONTO in PATO PO AGRO EO TO NCBITAXON OBI EFO CHMO MS CHEBI GO UO EDAM SWO ; do 
  wget -q -O- https://raw.githubusercontent.com/OBOFoundry/OBOFoundry.github.io/master/ontology/$(echo $ONTO | tr '[:upper:]' '[:lower:]').md |\
  awk 'BEGIN {RS="---"} FNR==2 {print $0}' |\
  yq '.domain'
done
```

There is also a combined `ontologies.yml` file. Probably the above could be rewritten 
to use that instead of the individual `wget` calls for better efficiency. 
The following extracts the list of dependencies (usually, but not always other ontologies),
and creates a human-friendly, comma separated list.

```
wget https://raw.githubusercontent.com/OBOFoundry/OBOFoundry.github.io/master/registry/ontologies.yml
for ONTO in ONTO in PATO PO AGRO EO TO NCBITAXON OBI EFO CHMO MS CHEBI GO UO EDAM SWO ; do 
  echo -n -e "$ONTO: " ; cat ontologies.yml |\
  yq eval "(.ontologies[]|select(.id==\"$ONTO\")).dependencies | map_values(.id) | join(\", \") "  
  echo
done | grep -v "^$" | cut -f 2
```


## The OLS API

This section is unfinished. You get metadata for one Ontology.
`wget -O- 'http://www.ebi.ac.uk/ols/api/ontologies/efo' | jq "."`
See OLS Docs https://www.ebi.ac.uk/ols/docs/api.

