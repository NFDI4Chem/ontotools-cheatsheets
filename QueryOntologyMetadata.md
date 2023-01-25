# Ontology commandline hacks

## Bioportal

### Get Bioportal Categories (keywords) 
You will need an APIkey, create an account at Bioportal 
and go to your account profile to get your real `apikey=fdf...5df"`

```
for ONTO in PATO PO AGRO EO TO NCBITAXON OBI EFO CHMO MS CHEBI GO UO EDAM SWO ; do 
  echo -n -e "ONTO=$ONTO\t"; 
  curl -s --fail-with-body "https://data.bioontology.org/ontologies/$ONTO/categories?apikey=fdf...5df" |\
  jq 'map(.name) | join(", ")' 2>/dev/null || echo NOT_ON_BIOPORTAL
done | tr -d \" | cut -f 2
```

## OBOfoundry

### Get Ontology License from OBOfoundry metadata

```
for ONTO in PATO PO AGRO EO TO NCBITAXON OBI EFO CHMO MS CHEBI GO UO EDAM SWO ; do
  wget -q -O- https://raw.githubusercontent.com/OBOFoundry/OBOFoundry.github.io/master/ontology/$(echo $ONTO | tr '[:upper:]' '[:lower:]').md |\
  awk 'BEGIN {RS="---"} FNR==2 {print $0}' |\
  yq '.license.label'; 
done | sed -e 's/^null//'
```

### Get Domain (keywords) from OBOfoundry metadata
```
for ONTO in PATO PO AGRO EO TO NCBITAXON OBI EFO CHMO MS CHEBI GO UO EDAM SWO ; do 
  wget -q -O- https://raw.githubusercontent.com/OBOFoundry/OBOFoundry.github.io/master/ontology/$(echo $ONTO | tr '[:upper:]' '[:lower:]').md |\
  awk 'BEGIN {RS="---"} FNR==2 {print $0}' |\
  yq '.domain'
done
```

## The OLS API

### Dependencies as known to OLS
for ONTO in ONTO in PATO PO AGRO EO TO NCBITAXON OBI EFO CHMO MS CHEBI GO UO EDAM SWO ; do 
  echo -n -e "$ONTO: " ; cat /tmp/ontologies.yml |\
  yq eval "(.ontologies[]|select(.id==\"$ONTO\")).dependencies | map_values(.id) | join(\", \") "  
  echo
done | grep -v "^$" | cut -f 2

```

### 

This section is unfinished. You get metadata for one Ontology.
OLS Docs https://www.ebi.ac.uk/ols/docs/api

wget -O- 'http://www.ebi.ac.uk/ols/api/ontologies/efo' | jq "."
