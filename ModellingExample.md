# Modelling example #

We provide an RDF graph in a pseudo-N3 notation as an example of how the gold standard corpus data and mutation impact text mining system results are represented in our framework. Note that non-mnemonic ontological identifiers are replaced with pseudo-identifiers using the corresponding labels: e. g., sio:SIO 000011 and sio:SIO 000300 are replaced respectively with sio:’has attribute’ and sio:’has value’.
```
# Description of a singular amino acid substitution N30A:
:singular_mutation1 a mieo:AminoAcidSubstitution;
    mieo:mutationHasWildtypeResidue mieo:Asparagine;
    mieo:mutationHasMutantResidue mieo:Alanine;
    mieo:mutationHasPosition
	[ a sio:'position';
	  sio:'has value' "30"^^xsd:integer ] .

# Description of a singular amino acid substitution N50A:
:singular_mutation2 a mieo:AminoAcidSubstitution;
    mieo:mutationHasWildtypeResidue mieo:Asparagine;
    mieo:mutationHasMutanResidue mieo:Alanine;
    mieo:mutationHasPosition
	[ a sio:'position';
	  sio:'has value' "50"^^xsd:integer ] .

# Combined mutation ("mutation series") consisting of the two singular mutations:
:mutation a mieo:CombinedAminoAcidChange;
    sio:'has member' :singular_mutation1, :singular_mutation2;
    sio:'has attribute' :number_of_singular_mutations .
:number_of_singular_mutations a sio:'count';
    sio:'has value' "2"^^xsd:integer .

# Mutation application ("grounding") to a specific protein:
:mutation_application a mieo:ProteinMutationApplication; 
    mieo:isApplicationOfMutation :mutation;
    mieo:isApplicationOfMutationToProtein :protein .

# Description of the protein:
:protein a mieo:ProteinVariant; # it's a specific variant (uniquely identifies the sequence)
    mieo:proteinHasSequence :protein_sequence;
    sio:'is subject of' :uniprot_record .

# Standard SIO way to link entities, DB records and IDs: 
:uniprot_record a lsrn:UniProt_Record;
    sio:'has attribute'
	[ a lsrn:UniProt_Identifier;
	  sio:'has value' "P22635" ] .

# Provenance is mostly done with ao:hasTopic :
:document a sio:'article' .
:ann_mutation a ao:Annotation;
    aof:annotatesDocument :document;
    ao:hasTopic :mutation .
:ann_protein a ao:Annotation;
    aof:annotatesDocument :document;
    ao:hasTopic :protein .

:document sio:'is subject of' 
    [ a lsrn:PMID_Record;
      sio:'has attribute'
	  [ a lsrn:PMID_Identifier;
	    sio:'has value' "17526795"^^xsd:string ] . ] .
```