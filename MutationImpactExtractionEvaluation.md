
# Evaluation Task: Mutation Impact Extraction #

**Mutation Impact Extraction** is identifying the impact of the mutational change with respect to the wild-type protein. The result can be of any form, but should contain all the knowledge stated in a source concerning the impact. <br>

<h2>Metrics</h2>
Two standard measures for mutation impact extraction performance evaluation are used:<br>
<ul><li><b>precision</b>, defined as the number of <b>correct mutation impacts</b> over <b>all retrieved mutation impacts</b>
</li><li><b>recall</b>, defined as the number of <b>correct mutation impacts</b> over <b>all uniquely mentioned mutation impacts</b>.</li></ul>

What to consider as correct mutation impact depends on the focus of evaluation. See example of specified metric below.<br>
<br>
<h2>Specified metric #1</h2>
The metrics are taken from <code>[1]</code>.<br>
In order for an extracted mutation impact to be considered correct the following  parts have to be correct i.e. <b>the protein property</b> that is being impacted, <b>the direction</b> of the impact and <b>the causal mutation</b>. If the protein property was a molecular function, it had to be normalised by grounding to Gene Ontology.<br>
<br>
Open Mutation Miner <code>[2]</code> and Mutation Impact Extraction System <code>[3]</code> were evaluated on 2 different corpora -- Enzyminer Corpus and DHLA Corpus (see details <a href='http://code.google.com/p/mutation-text-mining/wiki/Corpora'>here</a>). For each experiment, a SPARQL repository/endpoint is available:<br>
<ul><li>Open Mutation Miner on Enzyminer Corpus:<br>
<ul><li><a href='http://138.119.12.53:8080/openrdf-workbench/repositories/enzyminer-omm/query'>http://138.119.12.53:8080/openrdf-workbench/repositories/enzyminer-omm/query</a>
<br>
</li></ul></li><li>Open Mutation Miner on DHLA Corpus:<br>
<ul><li><a href='http://138.119.12.53:8080/openrdf-workbench/repositories/dhla-omm/query'>http://138.119.12.53:8080/openrdf-workbench/repositories/dhla-omm/query</a>
<br>
</li></ul></li><li>Mutation Impact Extraction System on Enzyminer Corpus:<br>
<ul><li><a href='http://138.119.12.53:8080/openrdf-workbench/repositories/enzyminer-mies/query'>http://138.119.12.53:8080/openrdf-workbench/repositories/enzyminer-mies/query</a>
<br>
</li></ul></li><li>Mutation Impact Extraction System on DHLA Corpus:<br>
<ul><li><a href='http://138.119.12.53:8080/openrdf-workbench/repositories/dhla-mies/query'>http://138.119.12.53:8080/openrdf-workbench/repositories/dhla-mies/query</a>
<br></li></ul></li></ul>

Contexts/Named Graphs are for the four experiments:<br>
<code>&lt;http://example.com/gold-standard.rdf&gt;</code><br>
<code>&lt;http://example.com/experiment.rdf&gt;</code>
<br><br>

<h3>SPARQL Query</h3>
Calculates Precision and Recall<br>
<pre><code>PREFIX rdf:&lt;http://www.w3.org/1999/02/22-rdf-syntax-ns#&gt;<br>
PREFIX rdfs:&lt;http://www.w3.org/2000/01/rdf-schema#&gt;<br>
PREFIX sio:&lt;http://semanticscience.org/resource/&gt;<br>
PREFIX mieo:&lt;http://cbakerlab.unbsj.ca:8080/ontologies/mutation-impact-extraction-ontology.owl#&gt;<br>
PREFIX lsrn:&lt;http://purl.oclc.org/SADI/LSRN/&gt;<br>
PREFIX ao:&lt;http://purl.org/ao/&gt;<br>
PREFIX aof:&lt;http://purl.org/ao/foaf/&gt;<br>
PREFIX pav:&lt;http://purl.org/pav/&gt;<br>
PREFIX xsd: &lt;http://www.w3.org/2001/XMLSchema#&gt;<br>
<br>
SELECT <br>
(str(xsd:float(?correct)/xsd:float(?found)) AS ?precision)<br>
(str(xsd:float(?correct)/xsd:float(?all)) AS ?recall) <br>
?correct  ?found ?all<br>
WHERE<br>
{<br>
# correct<br>
{<br>
SELECT (COUNT (*) as ?correct)<br>
WHERE{<br>
SELECT DISTINCT ?pubmed_id ?mut_id ?protein_property_class ?property_change_class<br>
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
      ?mutation sio:SIO_000673 ?mut_identifier .<br>
      ?mut_identifier a mieo:CombinedAminoAcidSequenceChange_Identifier .<br>
      ?mut_identifier sio:SIO_000300 ?mut_id .<br>
<br>
      ?ann_mutation_application a ao:Annotation .<br>
      ?ann_mutation_application aof:annotatesDocument ?document .<br>
      ?ann_mutation_application ao:hasTopic ?mutation_application .<br>
      ?mutation_application a mieo:ProteinMutationApplication .<br>
      ?mutation_application mieo:isApplicationOfMutation ?mutation .<br>
<br>
      ?ann_statement_of_mutation_effect a ao:Annotation .<br>
      ?ann_statement_of_mutation_effect aof:annotatesDocument ?document .<br>
      ?ann_statement_of_mutation_effect ao:hasTopic ?statement_of_mutation_effect .<br>
      ?statement_of_mutation_effect a mieo:StatementOfMutationEffect .<br>
      ?statement_of_mutation_effect mieo:arg1 ?mutation_application .<br>
      ?statement_of_mutation_effect mieo:arg2 ?property_change .<br>
<br>
      ?ann_property_change a ao:Annotation .<br>
      ?ann_property_change aof:annotatesDocument ?document .<br>
      ?ann_property_change ao:hasTopic ?property_change .<br>
      ?property_change a ?property_change_class .<br>
      ?property_change_class rdfs:subClassOf mieo:ProteinPropertyChange .<br>
      #?mutation_application mieo:mutationApplicationCausesChange ?property_change .<br>
      ?property_change mieo:propertyChangeAppliesTo ?protein_property .<br>
<br>
      ?ann_protein_property a ao:Annotation .<br>
      ?ann_protein_property aof:annotatesDocument ?document .<br>
      ?ann_protein_property ao:hasTopic ?protein_property .<br>
      ?protein_property a ?protein_property_class .<br>
      ?protein_property_class rdfs:subClassOf mieo:ProteinProperty .<br>
<br>
}<br>
<br>
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
      ?mutation2 sio:SIO_000673 ?mut_identifier2 .<br>
      ?mut_identifier2 a mieo:CombinedAminoAcidSequenceChange_Identifier .<br>
      ?mut_identifier2 sio:SIO_000300 ?mut_id .<br>
<br>
      ?ann_mutation_application2 a ao:Annotation .<br>
      ?ann_mutation_application2 aof:annotatesDocument ?document2 .<br>
      ?ann_mutation_application2 ao:hasTopic ?mutation_application2 .<br>
      ?mutation_application2 a mieo:ProteinMutationApplication .<br>
      ?mutation_application2 mieo:isApplicationOfMutation ?mutation2 .<br>
<br>
<br>
      ?ann_statement_of_mutation_effect2 a ao:Annotation .<br>
      ?ann_statement_of_mutation_effect2 aof:annotatesDocument ?document2 .<br>
      ?ann_statement_of_mutation_effect2 ao:hasTopic ?statement_of_mutation_effect2 .<br>
      ?statement_of_mutation_effect2 a mieo:StatementOfMutationEffect .<br>
      ?statement_of_mutation_effect2 mieo:arg1 ?mutation_application2 .<br>
      ?statement_of_mutation_effect2 mieo:arg2 ?property_change2 .<br>
<br>
      ?ann_property_change2 a ao:Annotation .<br>
      ?ann_property_change2 aof:annotatesDocument ?document2 .<br>
      ?ann_property_change2 ao:hasTopic ?property_change2 .<br>
      ?property_change2 a ?property_change_class .<br>
      ?property_change_class rdfs:subClassOf mieo:ProteinPropertyChange .<br>
      #?mutation_application mieo:mutationApplicationCausesChange ?property_change .<br>
<br>
      ?property_change2 mieo:propertyChangeAppliesTo ?protein_property2 .<br>
<br>
      ?ann_protein_property2 a ao:Annotation .<br>
      ?ann_protein_property2 aof:annotatesDocument ?document2 .<br>
      ?ann_protein_property2 ao:hasTopic ?protein_property2 .<br>
      ?protein_property2 a ?protein_property_class .<br>
      ?protein_property_class rdfs:subClassOf mieo:ProteinProperty .<br>
<br>
}<br>
}<br>
 <br>
}<br>
}<br>
# end correct<br>
<br>
# found<br>
{<br>
SELECT (COUNT (*) as ?found)<br>
WHERE{<br>
SELECT DISTINCT ?pubmed_id ?mut_id ?protein_property_class ?property_change_class<br>
<br>
WHERE {<br>
<br>
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
      ?mutation2 sio:SIO_000673 ?mut_identifier2 .<br>
      ?mut_identifier2 a mieo:CombinedAminoAcidSequenceChange_Identifier .<br>
      ?mut_identifier2 sio:SIO_000300 ?mut_id .<br>
<br>
      ?ann_mutation_application2 a ao:Annotation .<br>
      ?ann_mutation_application2 aof:annotatesDocument ?document2 .<br>
      ?ann_mutation_application2 ao:hasTopic ?mutation_application2 .<br>
      ?mutation_application2 a mieo:ProteinMutationApplication .<br>
      ?mutation_application2 mieo:isApplicationOfMutation ?mutation2 .<br>
<br>
      ?ann_statement_of_mutation_effect2 a ao:Annotation .<br>
      ?ann_statement_of_mutation_effect2 aof:annotatesDocument ?document2 .<br>
      ?ann_statement_of_mutation_effect2 ao:hasTopic ?statement_of_mutation_effect2 .<br>
      ?statement_of_mutation_effect2 a mieo:StatementOfMutationEffect .<br>
      ?statement_of_mutation_effect2 mieo:arg1 ?mutation_application2 .<br>
      ?statement_of_mutation_effect2 mieo:arg2 ?property_change2 .<br>
<br>
      ?ann_property_change2 a ao:Annotation .<br>
      ?ann_property_change2 aof:annotatesDocument ?document2 .<br>
      ?ann_property_change2 ao:hasTopic ?property_change2 .<br>
      ?property_change2 a ?property_change_class .<br>
      ?property_change_class rdfs:subClassOf mieo:ProteinPropertyChange .<br>
      #?mutation_application mieo:mutationApplicationCausesChange ?property_change .<br>
      ?property_change2 mieo:propertyChangeAppliesTo ?protein_property2 .<br>
<br>
      ?ann_protein_property2 a ao:Annotation .<br>
      ?ann_protein_property2 aof:annotatesDocument ?document2 .<br>
      ?ann_protein_property2 ao:hasTopic ?protein_property2 .<br>
      ?protein_property2 a ?protein_property_class .<br>
      ?protein_property_class rdfs:subClassOf mieo:ProteinProperty .<br>
<br>
}<br>
} <br>
<br>
}<br>
}<br>
# end found<br>
<br>
# all<br>
{<br>
SELECT (COUNT (*) as ?all)<br>
WHERE{<br>
SELECT DISTINCT ?pubmed_id ?mut_id ?protein_property_class ?property_change_class<br>
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
      ?mutation sio:SIO_000673 ?mut_identifier .<br>
      ?mut_identifier a mieo:CombinedAminoAcidSequenceChange_Identifier .<br>
      ?mut_identifier sio:SIO_000300 ?mut_id .<br>
<br>
      ?ann_mutation_application a ao:Annotation .<br>
      ?ann_mutation_application aof:annotatesDocument ?document .<br>
      ?ann_mutation_application ao:hasTopic ?mutation_application .<br>
      ?mutation_application a mieo:ProteinMutationApplication .<br>
      ?mutation_application mieo:isApplicationOfMutation ?mutation .<br>
<br>
      ?ann_statement_of_mutation_effect a ao:Annotation .<br>
      ?ann_statement_of_mutation_effect aof:annotatesDocument ?document .<br>
      ?ann_statement_of_mutation_effect ao:hasTopic ?statement_of_mutation_effect .<br>
      ?statement_of_mutation_effect a mieo:StatementOfMutationEffect .<br>
      ?statement_of_mutation_effect mieo:arg1 ?mutation_application .<br>
      ?statement_of_mutation_effect mieo:arg2 ?property_change .<br>
<br>
      ?ann_property_change a ao:Annotation .<br>
      ?ann_property_change aof:annotatesDocument ?document .<br>
      ?ann_property_change ao:hasTopic ?property_change .<br>
      ?property_change a ?property_change_class .<br>
      ?property_change_class rdfs:subClassOf mieo:ProteinPropertyChange .<br>
      #?mutation_application mieo:mutationApplicationCausesChange ?property_change .<br>
      ?property_change mieo:propertyChangeAppliesTo ?protein_property .<br>
<br>
      ?ann_protein_property a ao:Annotation .<br>
      ?ann_protein_property aof:annotatesDocument ?document .<br>
      ?ann_protein_property ao:hasTopic ?protein_property .<br>
      ?protein_property a ?protein_property_class .<br>
      ?protein_property_class rdfs:subClassOf mieo:ProteinProperty .<br>
<br>
}<br>
} <br>
}<br>
}<br>
# all<br>
}<br>
<br>
# returns n results<br>
<br>
</code></pre>


<h2>Specified metric #2</h2>
In order for an extracted mutation impact to be considered correct all the parts have to be correct i.e. <b>the protein</b> the mutation is grounded to, <b>the protein property</b> that is being impacted, <b>the direction</b> of the impact and <b>the causal mutation</b>.<br>
<br>
The example below shows evaluation using <b>micro averaging</b> that, essentially, treats the whole corpus as one large document: it calculates global recall and precision over all evaluated instances.<br>
<br>
<h3>SPARQL queries to calculate results</h3>
Here are presented SPARQL query templates for performance metrics computation.<br>
<br><br>
SPARQL Endpoint with demo corpus and loaded demo experiment results: <a href='http://138.119.12.53:8080/openrdf-workbench/repositories/mutation-corpus-demo/query'>http://138.119.12.53:8080/openrdf-workbench/repositories/mutation-corpus-demo/query</a>

Insert queries into Sesame triple store query interface (Query in the left panel)<br>
<br>
<br>
<h3>SPARQL: correct mutation impacts</h3>
<pre><code>PREFIX rdf:&lt;http://www.w3.org/1999/02/22-rdf-syntax-ns#&gt;<br>
PREFIX rdfs:&lt;http://www.w3.org/2000/01/rdf-schema#&gt;<br>
PREFIX sio:&lt;http://semanticscience.org/resource/&gt;<br>
PREFIX mieo:&lt;http://unbsj.biordf.net/ontologies/mutation-impact-extraction-ontology.owl#&gt;<br>
PREFIX lsrn:&lt;http://purl.oclc.org/SADI/LSRN/&gt;<br>
<br>
SELECT DISTINCT ?pubmed_id ?wt_residue ?position_value ?mut_residue ?uniprot_record_id ?protein_property_class ?property_change_class<br>
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
<br>
      #	'refers to'<br>
      ?document sio:SIO_000628 ?property_change .<br>
      ?mutation_application mieo:mutationApplicationCausesChange ?property_change .<br>
      ?property_change a ?property_change_class .<br>
<br>
      #	'refers to'<br>
      ?document sio:SIO_000628 ?statement_of_mutation_effect .<br>
      ?statement_of_mutation_effect mieo:arg1 ?mutation_application .<br>
      ?statement_of_mutation_effect mieo:arg2 ?property_change .<br>
<br>
      #	'refers to'<br>
      ?document sio:SIO_000628 ?protein_property .<br>
      ?property_change mieo:propertyChangeAppliesTo ?protein_property .<br>
<br>
      # 'is property of'<br>
      ?protein_property sio:SIO_000224 ?protein .<br>
      ?protein_property a ?protein_property_class .<br>
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
<br>
      ?document2 sio:SIO_000628 ?mutation_application2 .<br>
      ?mutation_application2 a mieo:ProteinMutationApplication .<br>
      ?mutation_application2 mieo:isApplicationOfMutation ?mutation2 .<br>
      ?mutation_application2 mieo:isApplicationOfMutationToProtein ?protein2 .<br>
<br>
<br>
      ?document2 sio:SIO_000628 ?protein2 .<br>
      ?protein2 sio:SIO_000629 ?uniprot_record2 .<br>
      ?uniprot_record2 a lsrn:UniProt_Record .<br>
      ?uniprot_record2 sio:SIO_000008 ?uniprot_record_identifier2 .<br>
      ?uniprot_record_identifier2 a lsrn:UniProt_Identifier .<br>
      ?uniprot_record_identifier2 sio:SIO_000300 ?uniprot_record_id .<br>
      FILTER (?uniprot_record_id != "")<br>
<br>
      ?document2 sio:SIO_000628 ?property_change2 .<br>
      ?mutation_application2 mieo:mutationApplicationCausesChange ?property_change2 .<br>
      ?property_change2 a ?property_change_class .<br>
<br>
      ?document2 sio:SIO_000628 ?statement_of_mutation_effect2 .<br>
      ?statement_of_mutation_effect2 mieo:arg1 ?mutation_application2 .<br>
      ?statement_of_mutation_effect2 mieo:arg2 ?property_change2 .<br>
<br>
      ?document2 sio:SIO_000628 ?protein_property2 .<br>
      ?property_change2 mieo:propertyChangeAppliesTo ?protein_property2 .<br>
<br>
      ?protein_property2 sio:SIO_000224 ?protein2 .<br>
      ?protein_property2 a ?protein_property_class .<br>
}<br>
<br>
} <br>
ORDER BY ?pubmed_id<br>
<br>
# returns n results<br>
</code></pre>


<h3>SPARQL: all retrieved mutation impacts</h3>
<pre><code>PREFIX rdf:&lt;http://www.w3.org/1999/02/22-rdf-syntax-ns#&gt;<br>
PREFIX rdfs:&lt;http://www.w3.org/2000/01/rdf-schema#&gt;<br>
PREFIX sio:&lt;http://semanticscience.org/resource/&gt;<br>
PREFIX mieo:&lt;http://unbsj.biordf.net/ontologies/mutation-impact-extraction-ontology.owl#&gt;<br>
PREFIX lsrn:&lt;http://purl.oclc.org/SADI/LSRN/&gt;<br>
<br>
<br>
SELECT DISTINCT ?pubmed_id ?wt_residue ?position_value ?mut_residue ?uniprot_record_id ?protein_property_class ?property_change_class<br>
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
<br>
      #	'refers to'<br>
      ?document sio:SIO_000628 ?property_change .<br>
      ?mutation_application mieo:mutationApplicationCausesChange ?property_change .<br>
      ?property_change a ?property_change_class .<br>
<br>
      #	'refers to'<br>
      ?document sio:SIO_000628 ?statement_of_mutation_effect .<br>
      ?statement_of_mutation_effect mieo:arg1 ?mutation_application .<br>
      ?statement_of_mutation_effect mieo:arg2 ?property_change .<br>
<br>
      #	'refers to'<br>
      ?document sio:SIO_000628 ?protein_property .<br>
      ?property_change mieo:propertyChangeAppliesTo ?protein_property .<br>
<br>
      # 'is property of'<br>
      ?protein_property sio:SIO_000224 ?protein .<br>
      ?protein_property a ?protein_property_class .<br>
}<br>
<br>
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
      FILTER (?uniprot_record_id != "")<br>
<br>
      ?document2 sio:SIO_000628 ?property_change2 .<br>
      ?mutation_application2 mieo:mutationApplicationCausesChange ?property_change2 .<br>
      ?property_change2 a ?property_change_class2 .<br>
<br>
      ?document2 sio:SIO_000628 ?statement_of_mutation_effect2 .<br>
      ?statement_of_mutation_effect2 mieo:arg1 ?mutation_application2 .<br>
      ?statement_of_mutation_effect2 mieo:arg2 ?property_change2 .<br>
<br>
      ?document2 sio:SIO_000628 ?protein_property2 .<br>
      ?property_change2 mieo:propertyChangeAppliesTo ?protein_property2 .<br>
<br>
      # 'is property of'<br>
      ?protein_property2 sio:SIO_000224 ?protein2 .<br>
      ?protein_property2 a ?protein_property_class2 .<br>
}<br>
<br>
} <br>
ORDER BY ?pubmed_id<br>
<br>
# returns 14 results<br>
<br>
</code></pre>

<h3>SPARQL: all uniquely mentioned mutation impacts and available in gold standard</h3>
<pre><code>PREFIX rdf:&lt;http://www.w3.org/1999/02/22-rdf-syntax-ns#&gt;<br>
PREFIX rdfs:&lt;http://www.w3.org/2000/01/rdf-schema#&gt;<br>
PREFIX sio:&lt;http://semanticscience.org/resource/&gt;<br>
PREFIX mieo:&lt;http://unbsj.biordf.net/ontologies/mutation-impact-extraction-ontology.owl#&gt;<br>
PREFIX lsrn:&lt;http://purl.oclc.org/SADI/LSRN/&gt;<br>
<br>
<br>
SELECT DISTINCT ?pubmed_id ?denotation_value ?wt_residue ?position_value ?mut_residue ?uniprot_record_id ?protein_property_class ?property_change_class<br>
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
<br>
      #	'refers to'<br>
      ?document sio:SIO_000628 ?property_change .<br>
      ?mutation_application mieo:mutationApplicationCausesChange ?property_change .<br>
      ?property_change a ?property_change_class .<br>
<br>
      #	'refers to'<br>
      ?document sio:SIO_000628 ?statement_of_mutation_effect .<br>
      ?statement_of_mutation_effect mieo:arg1 ?mutation_application .<br>
      ?statement_of_mutation_effect mieo:arg2 ?property_change .<br>
<br>
      #	'refers to'<br>
      ?document sio:SIO_000628 ?protein_property .<br>
      ?property_change mieo:propertyChangeAppliesTo ?protein_property .<br>
<br>
      # 'is property of'<br>
      ?protein_property sio:SIO_000224 ?protein .<br>
      ?protein_property a ?protein_property_class .<br>
}<br>
<br>
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
# returns 24 results<br>
<br>
</code></pre>



<h2>References</h2>
<br>
<code>[1]</code> <a href='http://www.biomedcentral.com/1471-2164/13/S4/S10'>Laurila JB, Naderi N, Witte R, Riazanov A, Kouznetsov A, Baker CJO: Algorithms and semantic infrastructure for mutation impact extraction and grounding. BMC Genomics 2010, 11(Suppl 4):S24.</a>
<br>
<code>[2]</code> <a href='http://www.semanticsoftware.info/open-mutation-miner'>http://www.semanticsoftware.info/open-mutation-miner</a>
<br>
<code>[3]</code> <a href='http://www.biomedcentral.com/1471-2164/11/S4/S24'>Naderi N, Witte R: Automated extraction and semantic analysis of mutation impacts from the biomedical literature. BMC Genomics 2012, 13(Suppl 4):S10.</a>