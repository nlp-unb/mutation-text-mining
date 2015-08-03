

# Evaluation Task: Mutation Grounding to UniProtIDs (Protein Sequences) #
**Mutation Grounding** is assigning mutations from text to protein sequences. <br>
The specific task of mutation grounding algorithms is to find, in a sequence database, the exact sequence referred to in the text and to do this by verifying whether the exact positions and identity of the wild-type amino acid residues, described in the texts, are found on the candidate sequence.<br>
<br>
<br>
<h2>Metrics</h2>
Two standard measures for mutation grounding performance evaluation are used:<br>
<ul><li><b>precision</b>, defined as the fraction of <b>correctly grounded mutations</b> (true positives) over <b>all grounded mutations</b> (true positives + false positives), and<br>
</li><li><b>recall</b>, defined as the fraction of <b>correctly grounded mutations</b> over <b>all mutations in the gold standard</b> (true positives + false negatives).</li></ul>

The example below shows evaluation using <b>micro averaging</b> that, essentially, treats the whole corpus as one large document: it calculates global recall and precision over all evaluated instances.<br>
<br>
<h2>SPARQL queries to calculate results</h2>
Here are presented SPARQL query templates for performance metrics computation.<br>
<br><br>
SPARQL Endpoint with demo corpus and loaded demo experiment results:<br>
<a href='http://138.119.12.53:8080/openrdf-workbench/repositories/mutation-corpus-demo/query'>http://138.119.12.53:8080/openrdf-workbench/repositories/mutation-corpus-demo/query</a>

Insert queries into Sesame triple store query interface (<b>Query</b> in the left panel)<br>
<br>
<h2>SPARQL: correctly grounded mutations</h2>
<pre><code>PREFIX sio:&lt;http://semanticscience.org/resource/&gt;<br>
PREFIX mieo:&lt;http://unbsj.biordf.net/ontologies/mutation-impact-extraction-ontology.owl#&gt;<br>
PREFIX lsrn:&lt;http://purl.oclc.org/SADI/LSRN/&gt;<br>
<br>
SELECT DISTINCT ?pubmed_id ?wt_residue ?position_value ?mut_residue ?uniprot_record_id<br>
<br>
WHERE {<br>
<br>
GRAPH &lt;http://example.com/enzyminer-corpus-demo.rdf&gt; {<br>
      #	'article'<br>
      ?document a sio:SIO_000154 .<br>
      #	'is subject of'<br>
      ?document sio:SIO_000629 ?pubmed_record .<br>
      #	'has attribute'<br>
      ?pubmed_record sio:SIO_000008 ?pubmed_identifier .<br>
      #	'has value'<br>
      ?pubmed_identifier sio:SIO_000300 ?pubmed_id .<br>
<br>
      #	'refers to'<br>
      ?document sio:SIO_000628 ?mutation .<br>
      ?mutation a mieo:CombinedAminoAcidSequenceChange .<br>
      # 'has member<br>
      ?mutation sio:SIO_000059 ?singular_mutation .<br>
      ?singular_mutation mieo:mutationHasWildtypeResidue ?wt_residue .<br>
      ?singular_mutation mieo:mutationHasMutantResidue ?mut_residue .<br>
      ?singular_mutation mieo:mutationHasPosition ?position .<br>
      #	'position'<br>
      ?position a sio:SIO_000056 .<br>
      #	'has value'<br>
      ?position sio:SIO_000300 ?position_value .<br>
    <br>
      #	'refers to'<br>
      ?document sio:SIO_000628 ?mutation_application .<br>
      ?mutation_application a mieo:ProteinMutationApplication .<br>
      ?mutation_application mieo:isApplicationOfMutation ?mutation .<br>
      ?mutation_application mieo:isApplicationOfMutationToProtein ?protein .<br>
<br>
      #	'refers to'<br>
      ?document sio:SIO_000628 ?protein .<br>
      #	'is subject of'<br>
      ?protein sio:SIO_000629 ?uniprot_record .<br>
      ?uniprot_record a lsrn:UniProt_Record .<br>
      #	'has attribute'<br>
      ?uniprot_record sio:SIO_000008 ?uniprot_record_identifier .<br>
      ?uniprot_record_identifier a lsrn:UniProt_Identifier .<br>
      #	'has value'<br>
      ?uniprot_record_identifier sio:SIO_000300 ?uniprot_record_id .<br>
      FILTER (?uniprot_record_id != "")<br>
}<br>
<br>
GRAPH &lt;http://example.com/enzyminer-corpus-experiment.rdf&gt; {<br>
      <br>
      ?document2 a sio:SIO_000154 .<br>
      ?document2 sio:SIO_000629 ?pubmed_record2 .<br>
      ?pubmed_record2 sio:SIO_000008 ?pubmed_identifier2 .<br>
      ?pubmed_identifier2 sio:SIO_000300 ?pubmed_id .<br>
<br>
      ?document2 sio:SIO_000628 ?mutation2 .<br>
      ?mutation2 a mieo:CombinedAminoAcidSequenceChange .<br>
      ?mutation2 sio:SIO_000059 ?singular_mutation2 .<br>
      ?singular_mutation2 mieo:mutationHasWildtypeResidue ?wt_residue .<br>
      ?singular_mutation2 mieo:mutationHasMutantResidue ?mut_residue .<br>
      ?singular_mutation2 mieo:mutationHasPosition ?position2 .<br>
      ?position2 a sio:SIO_000056 .<br>
      ?position2 sio:SIO_000300 ?position_value .<br>
<br>
      ?document2 sio:SIO_000628 ?mutation_application2 .<br>
      ?mutation_application2 a mieo:ProteinMutationApplication .<br>
      ?mutation_application2 mieo:isApplicationOfMutation ?mutation2 .<br>
      ?mutation_application2 mieo:isApplicationOfMutationToProtein ?protein2 .<br>
<br>
      ?document2 sio:SIO_000628 ?protein2 .<br>
      ?protein2 sio:SIO_000629 ?uniprot_record2 .<br>
      ?uniprot_record2 a lsrn:UniProt_Record .<br>
      ?uniprot_record2 sio:SIO_000008 ?uniprot_record_identifier2 .<br>
      ?uniprot_record_identifier2 a lsrn:UniProt_Identifier .<br>
      ?uniprot_record_identifier2 sio:SIO_000300 ?uniprot_record_id .<br>
      FILTER (?uniprot_record_id != "")      <br>
}<br>
<br>
} <br>
ORDER BY ?pubmed_id<br>
<br>
# returns 11 results<br>
</code></pre>

<h1>SPARQL: all grounded mutations</h1>
<pre><code>PREFIX sio:&lt;http://semanticscience.org/resource/&gt;<br>
PREFIX mieo:&lt;http://unbsj.biordf.net/ontologies/mutation-impact-extraction-ontology.owl#&gt;<br>
PREFIX lsrn:&lt;http://purl.oclc.org/SADI/LSRN/&gt;<br>
<br>
SELECT DISTINCT ?pubmed_id ?wt_residue ?position_value ?mut_residue ?uniprot_record_id<br>
WHERE {<br>
<br>
GRAPH &lt;http://example.com/enzyminer-corpus-demo.rdf&gt; {<br>
<br>
      #	'article'<br>
      ?document a sio:SIO_000154 .<br>
      #	'is subject of'<br>
      ?document sio:SIO_000629 ?pubmed_record .<br>
      #	'has attribute'<br>
      ?pubmed_record sio:SIO_000008 ?pubmed_identifier .<br>
      #	'has value'<br>
      ?pubmed_identifier sio:SIO_000300 ?pubmed_id .<br>
<br>
      #	'refers to'<br>
      ?document sio:SIO_000628 ?mutation .<br>
      ?mutation a mieo:CombinedAminoAcidSequenceChange .<br>
      # 'has member<br>
      ?mutation sio:SIO_000059 ?singular_mutation .<br>
      ?singular_mutation mieo:mutationHasWildtypeResidue ?wt_residue .<br>
      ?singular_mutation mieo:mutationHasMutantResidue ?mut_residue .<br>
      ?singular_mutation mieo:mutationHasPosition ?position .<br>
      #	'position'<br>
      ?position a sio:SIO_000056 .<br>
      #	'has value'<br>
      ?position sio:SIO_000300 ?position_value .<br>
    <br>
      #	'refers to'<br>
      ?document sio:SIO_000628 ?mutation_application .<br>
      ?mutation_application a mieo:ProteinMutationApplication .<br>
      ?mutation_application mieo:isApplicationOfMutation ?mutation .<br>
      ?mutation_application mieo:isApplicationOfMutationToProtein ?protein .<br>
<br>
      #	'refers to'<br>
      ?document sio:SIO_000628 ?protein .<br>
      #	'is subject of'<br>
      ?protein sio:SIO_000629 ?uniprot_record .<br>
      ?uniprot_record a lsrn:UniProt_Record .<br>
      #	'has attribute'<br>
      ?uniprot_record sio:SIO_000008 ?uniprot_record_identifier .<br>
      ?uniprot_record_identifier a lsrn:UniProt_Identifier .<br>
      #	'has value'<br>
      ?uniprot_record_identifier sio:SIO_000300 ?uniprot_record_id .<br>
      FILTER (?uniprot_record_id != "")<br>
}<br>
<br>
<br>
FILTER EXISTS {<br>
GRAPH &lt;http://example.com/enzyminer-corpus-experiment.rdf&gt; {<br>
<br>
      ?document2 a sio:SIO_000154 .<br>
      ?document2 sio:SIO_000629 ?pubmed_record2 .<br>
      ?pubmed_record2 sio:SIO_000008 ?pubmed_identifier2 .<br>
      ?pubmed_identifier2 sio:SIO_000300 ?pubmed_id .<br>
<br>
      ?document2 sio:SIO_000628 ?mutation2 .<br>
      ?mutation2 a mieo:CombinedAminoAcidSequenceChange .<br>
      ?mutation2 sio:SIO_000059 ?singular_mutation2 .<br>
<br>
      ?singular_mutation2 mieo:mutationHasWildtypeResidue ?wt_residue .<br>
      ?singular_mutation2 mieo:mutationHasMutantResidue ?mut_residue .<br>
      ?singular_mutation2 mieo:mutationHasPosition ?position2 .<br>
      ?position2 a sio:SIO_000056 .<br>
      ?position2 sio:SIO_000300 ?position_value .<br>
<br>
      #?singular_mutation2 sio:SIO_000060 ?denotation2 .<br>
      #?denotation2 sio:SIO_000300 ?denotation_value .<br>
<br>
<br>
      ?document2 sio:SIO_000628 ?mutation_application2 .<br>
      ?mutation_application2 a mieo:ProteinMutationApplication .<br>
      ?mutation_application2 mieo:isApplicationOfMutation ?mutation2 .<br>
      ?mutation_application2 mieo:isApplicationOfMutationToProtein ?protein2 .<br>
<br>
      ?document2 sio:SIO_000628 ?protein2 .<br>
      ?protein2 sio:SIO_000629 ?uniprot_record2 .<br>
      ?uniprot_record2 a lsrn:UniProt_Record .<br>
      ?uniprot_record2 sio:SIO_000008 ?uniprot_record_identifier2 .<br>
      ?uniprot_record_identifier2 a lsrn:UniProt_Identifier .<br>
      ?uniprot_record_identifier2 sio:SIO_000300 ?uniprot_record_id2 .<br>
      FILTER (?uniprot_record_id2 != "")<br>
}<br>
}<br>
<br>
} <br>
ORDER BY ?pubmed_id<br>
<br>
# resturns 12  results<br>
<br>
</code></pre>


<h1>SPARQL: all mutations in the gold standard</h1>
<pre><code>PREFIX rdf:&lt;http://www.w3.org/1999/02/22-rdf-syntax-ns#&gt;<br>
PREFIX rdfs:&lt;http://www.w3.org/2000/01/rdf-schema#&gt;<br>
PREFIX sio:&lt;http://semanticscience.org/resource/&gt;<br>
PREFIX mieo:&lt;http://unbsj.biordf.net/ontologies/mutation-impact-extraction-ontology.owl#&gt;<br>
PREFIX lsrn:&lt;http://purl.oclc.org/SADI/LSRN/&gt;<br>
<br>
SELECT DISTINCT ?pubmed_id ?wt_residue ?position_value ?mut_residue ?uniprot_record_id<br>
<br>
WHERE {<br>
<br>
GRAPH &lt;http://example.com/enzyminer-corpus-demo.rdf&gt; {<br>
<br>
      #	'article'<br>
      ?document a sio:SIO_000154 .<br>
      #	'is subject of'<br>
      ?document sio:SIO_000629 ?pubmed_record .<br>
      #	'has attribute'<br>
      ?pubmed_record sio:SIO_000008 ?pubmed_identifier .<br>
      #	'has value'<br>
      ?pubmed_identifier sio:SIO_000300 ?pubmed_id .<br>
<br>
      #	'refers to'<br>
      ?document sio:SIO_000628 ?mutation .<br>
      ?mutation a mieo:CombinedAminoAcidSequenceChange .<br>
      # 'has member<br>
      ?mutation sio:SIO_000059 ?singular_mutation .<br>
      ?singular_mutation mieo:mutationHasWildtypeResidue ?wt_residue .<br>
      ?singular_mutation mieo:mutationHasMutantResidue ?mut_residue .<br>
      ?singular_mutation mieo:mutationHasPosition ?position .<br>
      #	'position'<br>
      ?position a sio:SIO_000056 .<br>
      #	'has value'<br>
      ?position sio:SIO_000300 ?position_value .<br>
    <br>
      #	'refers to'<br>
      ?document sio:SIO_000628 ?mutation_application .<br>
      ?mutation_application a mieo:ProteinMutationApplication .<br>
      ?mutation_application mieo:isApplicationOfMutation ?mutation .<br>
      ?mutation_application mieo:isApplicationOfMutationToProtein ?protein .<br>
<br>
      #	'refers to'<br>
      ?document sio:SIO_000628 ?protein .<br>
      #	'is subject of'<br>
      ?protein sio:SIO_000629 ?uniprot_record .<br>
      ?uniprot_record a lsrn:UniProt_Record .<br>
      #	'has attribute'<br>
      ?uniprot_record sio:SIO_000008 ?uniprot_record_identifier .<br>
      ?uniprot_record_identifier a lsrn:UniProt_Identifier .<br>
      #	'has value'<br>
      ?uniprot_record_identifier sio:SIO_000300 ?uniprot_record_id .<br>
      FILTER (?uniprot_record_id != "")<br>
}<br>
<br>
GRAPH &lt;http://example.com/enzyminer-corpus-experiment.rdf&gt; {<br>
<br>
      ?document2 a sio:SIO_000154 .<br>
      ?document2 sio:SIO_000629 ?pubmed_record2 .<br>
      ?pubmed_record2 sio:SIO_000008 ?pubmed_identifier2 .<br>
      ?pubmed_identifier2 sio:SIO_000300 ?pubmed_id .<br>
<br>
      ?document2 sio:SIO_000628 ?mutation2 .<br>
      ?mutation2 a mieo:CombinedAminoAcidSequenceChange .<br>
      ?mutation2 sio:SIO_000059 ?singular_mutation2 .<br>
<br>
      ?singular_mutation2 mieo:mutationHasWildtypeResidue ?wt_residue .<br>
      ?singular_mutation2 mieo:mutationHasMutantResidue ?mut_residue .<br>
      ?singular_mutation2 mieo:mutationHasPosition ?position2 .<br>
      ?position2 a sio:SIO_000056 .<br>
      ?position2 sio:SIO_000300 ?position_value .<br>
}<br>
<br>
} <br>
ORDER BY ?pubmed_id<br>
<br>
# returns 12 results<br>
</code></pre>