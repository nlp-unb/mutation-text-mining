
# Corpora anaysis and search #
The SPARQL query language can be directly used for calculating system performance metrics as well as **for various drill-down searches in the gold standard corpora and experiment results**. It is very flexible and implement easy querying mechanism.

## Example Endpoints ##
With Impacts
  * Open Mutation Miner on Enzyminer Corpus:
    * http://138.119.12.53:8080/openrdf-workbench/repositories/enzyminer-omm/query
  * Open Mutation Miner on OMM Impact Corpus:
    * http://138.119.12.53:8080/openrdf-workbench/repositories/impact-omm/query
  * Mutation Impact Extraction System on Enzyminer Corpus:
    * http://138.119.12.53:8080/openrdf-workbench/repositories/enzyminer-mies/query
  * Mutation Impact Extraction System on OMM Impact Corpus:
    * http://138.119.12.53:8080/openrdf-workbench/repositories/impact-mies/query
Without Impacts
  * Open Mutation Miner on DHLA Corpus:
    * http://138.119.12.53:8080/openrdf-workbench/repositories/dhla-omm/query
  * Mutation Impact Extraction System on DHLA Corpus:
    * http://138.119.12.53:8080/openrdf-workbench/repositories/dhla-mies/query

## Example SPARQL Queries ##

### Get document ids in gold standard corpus (number of documents in corpus) ###
```
SELECT DISTINCT ?pubmed_id
WHERE {
GRAPH <http://example.com/gold-standard.rdf> {
      ?document a sio:SIO_000154 .
      ?document sio:SIO_000629 ?pubmed_record .
      ?pubmed_record a lsrn:PMID_Record .
      ?pubmed_record sio:SIO_000008 ?pubmed_identifier .
      ?pubmed_identifier a lsrn:PMID_Identifier .
      ?pubmed_identifier sio:SIO_000300 ?pubmed_id .
}
}
```


### Get mutations (including combined mutations - mutation series) ###
```
SELECT DISTINCT ?pubmed_id ?mut_id
WHERE {
GRAPH <http://example.com/gold-standard.rdf> {
      ?document a sio:SIO_000154 .
      ?document sio:SIO_000629 ?pubmed_record .
      ?pubmed_record a lsrn:PMID_Record .
      ?pubmed_record sio:SIO_000008 ?pubmed_identifier .
      ?pubmed_identifier a lsrn:PMID_Identifier .
      ?pubmed_identifier sio:SIO_000300 ?pubmed_id .

 ?ann_mutation a ao:Annotation .
      ?ann_mutation aof:annotatesDocument ?document .
      ?ann_mutation ao:hasTopic ?mutation .
      ?mutation a mieo:CombinedAminoAcidSequenceChange .
      ?mutation sio:SIO_000673 ?mut_identifier .
      ?mut_identifier a mieo:CombinedAminoAcidSequenceChange_Identifier .
      ?mut_identifier sio:SIO_000300 ?mut_id .
	# for OMM corpus which contains 'unknown' (='n/a') mutations
      FILTER (!regex(str(?mut_id),"UNKNOWN"))
}
}
```

### Get impact sentences ###
```
SELECT DISTINCT ?pubmed_id ?property_change ?text_selector
WHERE {
  GRAPH <http://example.com/gold-standard.rdf>
   {
      ?document a sio:SIO_000154 .
      ?document sio:SIO_000629 ?pubmed_record .
      ?pubmed_record sio:SIO_000008 ?pubmed_identifier .
      ?pubmed_identifier sio:SIO_000300 ?pubmed_id .

      ?ann a ao:Annotation .
      ?ann aof:annotatesDocument ?document .
      ?ann ao:hasTopic ?property_change .
      ?property_change a ?property_change_class .
      ?property_change_class rdfs:subClassOf mieo:ProteinPropertyChange .

      FILTER NOT EXISTS {
          ?ann ao:hasTopic ?topic3 .
          FILTER (?property_change != ?topic3) .
      }

      ?ann ao:context ?text_selector .
      ?text_selector a aos:TextSelector . 
     # ?text_selector aos:exact ?text .

   } 
}
```

### Get false negatives ###
The SPARQL negation-related features proved especially useful in this effort because they allowed us to identify false negatives – cases presented in gold standard and absent from system results, thus identifying potential targets for optimisation. The following query represents such a use case where the FILTER NOT
EXISTS feature is applied to exclude correct system results for mutation grounding from the set of all results.
<br>
Endpoints: <br>
<ul><li>Open Mutation Miner on DHLA Corpus:<br>
<ul><li><a href='http://138.119.12.53:8080/openrdf-workbench/repositories/dhla-omm/query'>http://138.119.12.53:8080/openrdf-workbench/repositories/dhla-omm/query</a>
</li></ul></li><li>Mutation Impact Extraction System on DHLA Corpus:<br>
<ul><li><a href='http://138.119.12.53:8080/openrdf-workbench/repositories/dhla-mies/query'>http://138.119.12.53:8080/openrdf-workbench/repositories/dhla-mies/query</a>
Query:<br>
<pre><code>PREFIX aof:&lt;http://purl.org/ao/foaf/&gt;<br>
PREFIX foaf:&lt;http://xmlns.com/foaf/0.1/&gt;<br>
PREFIX aot:&lt;http://purl.org/ao/types/&gt;<br>
PREFIX aos:&lt;http://purl.org/ao/selectors/&gt;<br>
PREFIX mieo:&lt;http://cbakerlab.unbsj.ca:8080/ontologies/mutation-impact-extraction-ontology.owl#&gt;<br>
PREFIX rdfs:&lt;http://www.w3.org/2000/01/rdf-schema#&gt;<br>
PREFIX ao:&lt;http://purl.org/ao/&gt;<br>
PREFIX pav:&lt;http://purl.org/pav/&gt;<br>
PREFIX lsrn:&lt;http://purl.oclc.org/SADI/LSRN/&gt;<br>
PREFIX sio:&lt;http://semanticscience.org/resource/&gt;<br>
PREFIX rdf:&lt;http://www.w3.org/1999/02/22-rdf-syntax-ns#&gt;<br>
<br>
SELECT DISTINCT ?pubmed_id ?wt_residue ?position_value ?mut_residue ?uniprot_record_id<br>
<br>
WHERE {<br>
<br>
GRAPH &lt;http://example.com/gold-standard.rdf&gt; {<br>
<br>
      ?document a sio:SIO_000154 .<br>
      ?document sio:SIO_000629 ?pubmed_record .<br>
      ?pubmed_record a lsrn:PMID_Record .<br>
      ?pubmed_record sio:SIO_000008 ?pubmed_identifier .<br>
      ?pubmed_identifier a lsrn:PMID_Identifier .<br>
      ?pubmed_identifier sio:SIO_000300 ?pubmed_id .<br>
<br>
      ?ann_mutation a ao:Annotation .<br>
      ?ann_mutation aof:annotatesDocument ?document .<br>
      ?ann_mutation ao:hasTopic ?mutation .<br>
      ?mutation a mieo:CombinedAminoAcidSequenceChange .<br>
      ?mutation sio:SIO_000059 ?singular_mutation .<br>
      ?singular_mutation a mieo:AminoAcidSubstitution .<br>
<br>
      ?singular_mutation mieo:mutationHasWildtypeResidue ?wt_residue .<br>
      ?singular_mutation mieo:mutationHasMutantResidue ?mut_residue .<br>
      ?singular_mutation mieo:mutationHasPosition ?position .<br>
      ?position a sio:SIO_000056 .<br>
      ?position sio:SIO_000300 ?position_value .<br>
<br>
      ?ann_mutation_application a ao:Annotation .<br>
      ?ann_mutation_application aof:annotatesDocument ?document .<br>
      ?ann_mutation_application ao:hasTopic ?mutation_application .<br>
      ?mutation_application a mieo:ProteinMutationApplication .<br>
      ?mutation_application mieo:isApplicationOfMutation ?mutation .<br>
      ?mutation_application mieo:isApplicationOfMutationToProtein ?protein .<br>
<br>
      ?ann_protein a ao:Annotation .<br>
      ?ann_protein aof:annotatesDocument ?document .<br>
      ?ann_protein ao:hasTopic ?protein .<br>
      ?protein a mieo:ProteinVariant .<br>
      ?protein sio:SIO_000629 ?uniprot_record .<br>
      ?uniprot_record a lsrn:UniProt_Record .<br>
      ?uniprot_record sio:SIO_000008 ?uniprot_record_identifier .<br>
      ?uniprot_record_identifier a lsrn:UniProt_Identifier .<br>
      ?uniprot_record_identifier sio:SIO_000300 ?uniprot_record_id .<br>
      FILTER (?uniprot_record_id != "")<br>
}<br>
<br>
FILTER NOT EXISTS {<br>
GRAPH &lt;http://example.com/experiment.rdf&gt; {<br>
      <br>
      ?document2 a sio:SIO_000154 .<br>
      ?document2 sio:SIO_000629 ?pubmed_record2 .<br>
      ?pubmed_record2 a lsrn:PMID_Record .<br>
      ?pubmed_record2 sio:SIO_000008 ?pubmed_identifier2 .<br>
      ?pubmed_identifier2 a lsrn:PMID_Identifier .<br>
      ?pubmed_identifier2 sio:SIO_000300 ?pubmed_id .<br>
<br>
      ?ann_mutation2 a ao:Annotation .<br>
      ?ann_mutation2 aof:annotatesDocument ?document2 .<br>
      ?ann_mutation2 ao:hasTopic ?mutation2 .<br>
      ?mutation2 a mieo:CombinedAminoAcidSequenceChange .<br>
      ?mutation2 sio:SIO_000059 ?singular_mutation2 .<br>
      ?singular_mutation2 a mieo:AminoAcidSubstitution .<br>
<br>
      ?singular_mutation2 mieo:mutationHasWildtypeResidue ?wt_residue .<br>
      ?singular_mutation2 mieo:mutationHasMutantResidue ?mut_residue .<br>
      ?singular_mutation2 mieo:mutationHasPosition ?position2 .<br>
      ?position2 a sio:SIO_000056 .<br>
      ?position2 sio:SIO_000300 ?position_value .<br>
<br>
      ?ann_mutation_application2 a ao:Annotation .<br>
      ?ann_mutation_application2 aof:annotatesDocument ?document2 .<br>
      ?ann_mutation_application2 ao:hasTopic ?mutation_application2 .<br>
      ?mutation_application2 a mieo:ProteinMutationApplication .<br>
      ?mutation_application2 mieo:isApplicationOfMutation ?mutation2 .<br>
      ?mutation_application2 mieo:isApplicationOfMutationToProtein ?protein2 .<br>
<br>
      ?ann_protein2 a ao:Annotation .<br>
      ?ann_protein2 aof:annotatesDocument ?document2 .<br>
      ?ann_protein2 ao:hasTopic ?protein2 .<br>
      ?protein2 a mieo:ProteinVariant .<br>
      ?protein2 sio:SIO_000629 ?uniprot_record2 .<br>
      ?uniprot_record2 a lsrn:UniProt_Record .<br>
      ?uniprot_record2 sio:SIO_000008 ?uniprot_record_identifier2 .<br>
      ?uniprot_record_identifier2 a lsrn:UniProt_Identifier .<br>
      ?uniprot_record_identifier2 sio:SIO_000300 ?uniprot_record_id .<br>
      FILTER (?uniprot_record_id != "") <br>
}<br>
}<br>
} ORDER BY ?pubmed_id <br>
<br>
<br>
</code></pre>