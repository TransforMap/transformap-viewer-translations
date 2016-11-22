# backups of taxonomies in different languages

This place is used to hold static file versions of the different taxonomies (in different languages) from https://base.transformap.co

The taxonomies are fetched via an RDF query to https://query.base.transformap.co/bigdata/#query . Use this curl command:

    curl  -H "Accept: application/json" -o taxonomy.$LANG_CODE.json --get --data-urlencode "query=$SPARQL" "https://query.base.transformap.co/bigdata/namespace/transformap/sparql"

to fetch the data. A usual SPARQL query looks like:

```
prefix bd: <http://www.bigdata.com/rdf#>
prefix wikibase: <http://wikiba.se/ontology#>
prefix wdt: <http://base.transformap.co/prop/direct/>
prefix wd: <http://base.transformap.co/entity/>

SELECT ?item ?itemLabel ?instance_of ?subclass_of ?type_of_initiative_tag
WHERE { 
      ?item wdt:P8* wd:Q8 .
        ?item wdt:P8 ?subclass_of .
        OPTIONAL { ?item wdt:P4 ?instance_of . }
        OPTIONAL { ?item wdt:P15 ?type_of_initiative_tag }
        SERVICE wikibase:label {bd:serviceParam wikibase:language "en" }
}
```

Use curl to update this data:

```
echo "query:"
echo "$(cat query|sed "s/\$LANG/en/")"
for LANG_CODE in $(cat langs); do
  curl  -H "Accept: application/json" -o taxonomy.$LANG_CODE.json --get --data-urlencode "$(cat query|sed "s/\$LANG/$LANG_CODE/")" https://query.base.transformap.co/bigdata/namespace/transformap/sparql
done
```
