SPARQL Endpoint with demo corpus and loaded demo experiment results:
http://138.119.12.53:8080/openrdf-workbench/repositories/mutation-corpus-demo

Insert queries into Sesame triple store query interface (**Query** in the left panel)

## SPARQL: correctly grounded mutations ##
```
PREFIX sio:<http://semanticscience.org/resource/>
PREFIX mieo:<http://unbsj.biordf.net/ontologies/mutation-impact-extraction-ontology.owl#>
PREFIX lsrn:<http://purl.oclc.org/SADI/LSRN/>

SELECT DISTINCT ?pubmed_id ?wt_residue ?position_value ?mut_residue ?uniprot_record_id

WHERE {

GRAPH <http://example.com/enzyminer-corpus-demo.rdf> {
      #	'article'
      ?document a sio:SIO_000154 .
      #	'is subject of'
      ?document sio:SIO_000629 ?pubmed_record .
      #	'has attribute'
      ?pubmed_record sio:SIO_000008 ?pubmed_identifier .
      #	'has value'
      ?pubmed_identifier sio:SIO_000300 ?pubmed_id .

      #	'refers to'
      ?document sio:SIO_000628 ?mutation .
      ?mutation a mieo:CombinedAminoAcidSequenceChange .
      # 'has member
      ?mutation sio:SIO_000059 ?singular_mutation .
      ?singular_mutation mieo:mutationHasWildtypeResidue ?wt_residue .
      ?singular_mutation mieo:mutationHasMutantResidue ?mut_residue .
      ?singular_mutation mieo:mutationHasPosition ?position .
      #	'position'
      ?position a sio:SIO_000056 .
      #	'has value'
      ?position sio:SIO_000300 ?position_value .
    
      #	'refers to'
      ?document sio:SIO_000628 ?mutation_application .
      ?mutation_application a mieo:ProteinMutationApplication .
      ?mutation_application mieo:isApplicationOfMutation ?mutation .
      ?mutation_application mieo:isApplicationOfMutationToProtein ?protein .

      #	'refers to'
      ?document sio:SIO_000628 ?protein .
      #	'is subject of'
      ?protein sio:SIO_000629 ?uniprot_record .
      ?uniprot_record a lsrn:UniProt_Record .
      #	'has attribute'
      ?uniprot_record sio:SIO_000008 ?uniprot_record_identifier .
      ?uniprot_record_identifier a lsrn:UniProt_Identifier .
      #	'has value'
      ?uniprot_record_identifier sio:SIO_000300 ?uniprot_record_id .
      FILTER (?uniprot_record_id != "")
}

GRAPH <http://example.com/enzyminer-corpus-experiment.rdf> {
      
      ?document2 a sio:SIO_000154 .
      ?document2 sio:SIO_000629 ?pubmed_record2 .
      ?pubmed_record2 sio:SIO_000008 ?pubmed_identifier2 .
      ?pubmed_identifier2 sio:SIO_000300 ?pubmed_id .

      ?document2 sio:SIO_000628 ?mutation2 .
      ?mutation2 a mieo:CombinedAminoAcidSequenceChange .
      ?mutation2 sio:SIO_000059 ?singular_mutation2 .
      ?singular_mutation2 mieo:mutationHasWildtypeResidue ?wt_residue .
      ?singular_mutation2 mieo:mutationHasMutantResidue ?mut_residue .
      ?singular_mutation2 mieo:mutationHasPosition ?position2 .
      ?position2 a sio:SIO_000056 .
      ?position2 sio:SIO_000300 ?position_value .

      ?document2 sio:SIO_000628 ?mutation_application2 .
      ?mutation_application2 a mieo:ProteinMutationApplication .
      ?mutation_application2 mieo:isApplicationOfMutation ?mutation2 .
      ?mutation_application2 mieo:isApplicationOfMutationToProtein ?protein2 .

      ?document2 sio:SIO_000628 ?protein2 .
      ?protein2 sio:SIO_000629 ?uniprot_record2 .
      ?uniprot_record2 a lsrn:UniProt_Record .
      ?uniprot_record2 sio:SIO_000008 ?uniprot_record_identifier2 .
      ?uniprot_record_identifier2 a lsrn:UniProt_Identifier .
      ?uniprot_record_identifier2 sio:SIO_000300 ?uniprot_record_id .
      FILTER (?uniprot_record_id != "")      
}

} 
ORDER BY ?pubmed_id

# returns 11 results
```

# SPARQL: all grounded mutations #
```
PREFIX sio:<http://semanticscience.org/resource/>
PREFIX mieo:<http://unbsj.biordf.net/ontologies/mutation-impact-extraction-ontology.owl#>
PREFIX lsrn:<http://purl.oclc.org/SADI/LSRN/>

SELECT DISTINCT ?pubmed_id ?wt_residue ?position_value ?mut_residue ?uniprot_record_id
WHERE {

GRAPH <http://example.com/enzyminer-corpus-demo.rdf> {

      #	'article'
      ?document a sio:SIO_000154 .
      #	'is subject of'
      ?document sio:SIO_000629 ?pubmed_record .
      #	'has attribute'
      ?pubmed_record sio:SIO_000008 ?pubmed_identifier .
      #	'has value'
      ?pubmed_identifier sio:SIO_000300 ?pubmed_id .

      #	'refers to'
      ?document sio:SIO_000628 ?mutation .
      ?mutation a mieo:CombinedAminoAcidSequenceChange .
      # 'has member
      ?mutation sio:SIO_000059 ?singular_mutation .
      ?singular_mutation mieo:mutationHasWildtypeResidue ?wt_residue .
      ?singular_mutation mieo:mutationHasMutantResidue ?mut_residue .
      ?singular_mutation mieo:mutationHasPosition ?position .
      #	'position'
      ?position a sio:SIO_000056 .
      #	'has value'
      ?position sio:SIO_000300 ?position_value .
    
      #	'refers to'
      ?document sio:SIO_000628 ?mutation_application .
      ?mutation_application a mieo:ProteinMutationApplication .
      ?mutation_application mieo:isApplicationOfMutation ?mutation .
      ?mutation_application mieo:isApplicationOfMutationToProtein ?protein .

      #	'refers to'
      ?document sio:SIO_000628 ?protein .
      #	'is subject of'
      ?protein sio:SIO_000629 ?uniprot_record .
      ?uniprot_record a lsrn:UniProt_Record .
      #	'has attribute'
      ?uniprot_record sio:SIO_000008 ?uniprot_record_identifier .
      ?uniprot_record_identifier a lsrn:UniProt_Identifier .
      #	'has value'
      ?uniprot_record_identifier sio:SIO_000300 ?uniprot_record_id .
      FILTER (?uniprot_record_id != "")
}


FILTER EXISTS {
GRAPH <http://example.com/enzyminer-corpus-experiment.rdf> {

      ?document2 a sio:SIO_000154 .
      ?document2 sio:SIO_000629 ?pubmed_record2 .
      ?pubmed_record2 sio:SIO_000008 ?pubmed_identifier2 .
      ?pubmed_identifier2 sio:SIO_000300 ?pubmed_id .

      ?document2 sio:SIO_000628 ?mutation2 .
      ?mutation2 a mieo:CombinedAminoAcidSequenceChange .
      ?mutation2 sio:SIO_000059 ?singular_mutation2 .

      ?singular_mutation2 mieo:mutationHasWildtypeResidue ?wt_residue .
      ?singular_mutation2 mieo:mutationHasMutantResidue ?mut_residue .
      ?singular_mutation2 mieo:mutationHasPosition ?position2 .
      ?position2 a sio:SIO_000056 .
      ?position2 sio:SIO_000300 ?position_value .

      #?singular_mutation2 sio:SIO_000060 ?denotation2 .
      #?denotation2 sio:SIO_000300 ?denotation_value .


      ?document2 sio:SIO_000628 ?mutation_application2 .
      ?mutation_application2 a mieo:ProteinMutationApplication .
      ?mutation_application2 mieo:isApplicationOfMutation ?mutation2 .
      ?mutation_application2 mieo:isApplicationOfMutationToProtein ?protein2 .

      ?document2 sio:SIO_000628 ?protein2 .
      ?protein2 sio:SIO_000629 ?uniprot_record2 .
      ?uniprot_record2 a lsrn:UniProt_Record .
      ?uniprot_record2 sio:SIO_000008 ?uniprot_record_identifier2 .
      ?uniprot_record_identifier2 a lsrn:UniProt_Identifier .
      ?uniprot_record_identifier2 sio:SIO_000300 ?uniprot_record_id2 .
      FILTER (?uniprot_record_id2 != "")
}
}

} 
ORDER BY ?pubmed_id

# resturns 12  results

```


# SPARQL: all mutations in the gold standard #
```
PREFIX rdf:<http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs:<http://www.w3.org/2000/01/rdf-schema#>
PREFIX sio:<http://semanticscience.org/resource/>
PREFIX mieo:<http://unbsj.biordf.net/ontologies/mutation-impact-extraction-ontology.owl#>
PREFIX lsrn:<http://purl.oclc.org/SADI/LSRN/>

SELECT DISTINCT ?pubmed_id ?wt_residue ?position_value ?mut_residue ?uniprot_record_id

WHERE {

GRAPH <http://example.com/enzyminer-corpus-demo.rdf> {

      #	'article'
      ?document a sio:SIO_000154 .
      #	'is subject of'
      ?document sio:SIO_000629 ?pubmed_record .
      #	'has attribute'
      ?pubmed_record sio:SIO_000008 ?pubmed_identifier .
      #	'has value'
      ?pubmed_identifier sio:SIO_000300 ?pubmed_id .

      #	'refers to'
      ?document sio:SIO_000628 ?mutation .
      ?mutation a mieo:CombinedAminoAcidSequenceChange .
      # 'has member
      ?mutation sio:SIO_000059 ?singular_mutation .
      ?singular_mutation mieo:mutationHasWildtypeResidue ?wt_residue .
      ?singular_mutation mieo:mutationHasMutantResidue ?mut_residue .
      ?singular_mutation mieo:mutationHasPosition ?position .
      #	'position'
      ?position a sio:SIO_000056 .
      #	'has value'
      ?position sio:SIO_000300 ?position_value .
    
      #	'refers to'
      ?document sio:SIO_000628 ?mutation_application .
      ?mutation_application a mieo:ProteinMutationApplication .
      ?mutation_application mieo:isApplicationOfMutation ?mutation .
      ?mutation_application mieo:isApplicationOfMutationToProtein ?protein .

      #	'refers to'
      ?document sio:SIO_000628 ?protein .
      #	'is subject of'
      ?protein sio:SIO_000629 ?uniprot_record .
      ?uniprot_record a lsrn:UniProt_Record .
      #	'has attribute'
      ?uniprot_record sio:SIO_000008 ?uniprot_record_identifier .
      ?uniprot_record_identifier a lsrn:UniProt_Identifier .
      #	'has value'
      ?uniprot_record_identifier sio:SIO_000300 ?uniprot_record_id .
      FILTER (?uniprot_record_id != "")
}

GRAPH <http://example.com/enzyminer-corpus-experiment.rdf> {

      ?document2 a sio:SIO_000154 .
      ?document2 sio:SIO_000629 ?pubmed_record2 .
      ?pubmed_record2 sio:SIO_000008 ?pubmed_identifier2 .
      ?pubmed_identifier2 sio:SIO_000300 ?pubmed_id .

      ?document2 sio:SIO_000628 ?mutation2 .
      ?mutation2 a mieo:CombinedAminoAcidSequenceChange .
      ?mutation2 sio:SIO_000059 ?singular_mutation2 .

      ?singular_mutation2 mieo:mutationHasWildtypeResidue ?wt_residue .
      ?singular_mutation2 mieo:mutationHasMutantResidue ?mut_residue .
      ?singular_mutation2 mieo:mutationHasPosition ?position2 .
      ?position2 a sio:SIO_000056 .
      ?position2 sio:SIO_000300 ?position_value .
}

} 
ORDER BY ?pubmed_id

# returns 12 results
```