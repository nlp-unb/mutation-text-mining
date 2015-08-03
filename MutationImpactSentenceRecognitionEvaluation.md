

# Evaluation Task: Mutation Impact Sentence Recognition #

This task was evaluated in `[1]`.
<br>
<ul><li><b>precision</b>, defined as the number of correctly identified impact sentences over all recognized impact sentences. The sentence is correctly identified if it matched the gold standard.<br>
</li><li><b>recall</b>, defined as the number of correctly identified impact sentences over all impact sentences in gold standard.<br>
<br></li></ul>



<h2>Example</h2>
Open Mutation Miner <code>[2]</code> and Mutation Impact Extraction System <code>[3]</code> were evaluated on 2 different corpora -- Enzyminer Corpus and OMM Impact Corpus (see details <a href='http://code.google.com/p/mutation-text-mining/wiki/Corpora'>here</a>). For each experiment, a SPARQL repository/endpoint is available:<br>
<ul><li>Open Mutation Miner on Enzyminer Corpus:<br>
<ul><li><a href='http://138.119.12.53:8080/openrdf-workbench/repositories/enzyminer-omm/query'>http://138.119.12.53:8080/openrdf-workbench/repositories/enzyminer-omm/query</a>
<br>
</li></ul></li><li>Open Mutation Miner on OMM Impact Corpus:<br>
<ul><li><a href='http://138.119.12.53:8080/openrdf-workbench/repositories/impact-omm/query'>http://138.119.12.53:8080/openrdf-workbench/repositories/impact-omm/query</a>
<br>
</li></ul></li><li>Mutation Impact Extraction System on Enzyminer Corpus:<br>
<ul><li><a href='http://138.119.12.53:8080/openrdf-workbench/repositories/enzyminer-mies/query'>http://138.119.12.53:8080/openrdf-workbench/repositories/enzyminer-mies/query</a>
<br>
</li></ul></li><li>Mutation Impact Extraction System on OMM Impact Corpus:<br>
<ul><li><a href='http://138.119.12.53:8080/openrdf-workbench/repositories/impact-mies/query'>http://138.119.12.53:8080/openrdf-workbench/repositories/impact-mies/query</a>
<br></li></ul></li></ul>

Contexts/Named Graphs are for the four experiments:<br>
<code>&lt;http://example.com/gold-standard.rdf&gt;</code><br>
<code>&lt;http://example.com/experiment.rdf&gt;</code>
<br><br>


<h2>SPARQL Query</h2>
Calculates Precision and Recall<br>
<pre><code>PREFIX mieo:&lt;http://cbakerlab.unbsj.ca:8080/ontologies/mutation-impact-extraction-ontology.owl#&gt;<br>
PREFIX rdfs:&lt;http://www.w3.org/2000/01/rdf-schema#&gt;<br>
PREFIX foaf:&lt;http://xmlns.com/foaf/0.1/&gt;<br>
PREFIX aof:&lt;http://purl.org/ao/foaf/&gt;<br>
PREFIX ao:&lt;http://purl.org/ao/&gt;<br>
PREFIX aos:&lt;http://purl.org/ao/selectors/&gt;<br>
PREFIX lsrn:&lt;http://purl.oclc.org/SADI/LSRN/&gt;<br>
PREFIX sio:&lt;http://semanticscience.org/resource/&gt;<br>
PREFIX owl:&lt;http://www.w3.org/2002/07/owl#&gt;<br>
PREFIX rdf:&lt;http://www.w3.org/1999/02/22-rdf-syntax-ns#&gt;<br>
PREFIX xsd: &lt;http://www.w3.org/2001/XMLSchema#&gt;<br>
<br>
SELECT <br>
(xsd:float(?correct)/xsd:float(?retrieved) AS ?precision) <br>
(xsd:float(?correct)/xsd:float(?relevant) AS ?recall)  <br>
?correct  ?retrieved ?relevant<br>
WHERE<br>
{<br>
# correct<br>
{<br>
SELECT (COUNT (*) as ?correct)<br>
WHERE{<br>
SELECT DISTINCT ?pubmed_id ?property_change ?text_selector<br>
WHERE {<br>
  GRAPH &lt;http://example.com/gold-standard.rdf&gt;<br>
   {<br>
      ?document a sio:SIO_000154 .<br>
      ?document sio:SIO_000629 ?pubmed_record .<br>
      ?pubmed_record sio:SIO_000008 ?pubmed_identifier .<br>
      ?pubmed_identifier sio:SIO_000300 ?pubmed_id .<br>
<br>
      ?ann a ao:Annotation .<br>
      ?ann aof:annotatesDocument ?document .<br>
      ?ann ao:hasTopic ?property_change .<br>
      ?property_change a ?property_change_class .<br>
      ?property_change_class rdfs:subClassOf mieo:ProteinPropertyChange .<br>
<br>
      ?ann ao:context ?text_selector .<br>
      ?text_selector a aos:TextSelector . <br>
     # ?text_selector aos:exact ?text .<br>
<br>
   } .<br>
<br>
  GRAPH &lt;http://example.com/experiment.rdf&gt;<br>
   {<br>
      ?document2 a sio:SIO_000154 .<br>
      ?document2 sio:SIO_000629 ?pubmed_record2 .<br>
      ?pubmed_record2 sio:SIO_000008 ?pubmed_identifier2 .<br>
      ?pubmed_identifier2 sio:SIO_000300 ?pubmed_id .<br>
<br>
      ?ann2 a ao:Annotation .<br>
      ?ann2 aof:annotatesDocument ?document2 .<br>
      ?ann2 ao:hasTopic ?property_change2 .<br>
      ?property_change2 a ?property_change_class2 .<br>
      ?property_change_class2 rdfs:subClassOf mieo:ProteinPropertyChange .<br>
<br>
      ?ann2 ao:context ?text_selector2 .<br>
      ?text_selector2 a aos:TextSelector . <br>
     # ?text_selector2 aos:exact ?text2 .<br>
   } .<br>
   ?sim a mieo:StringSimilarity .<br>
   ?text_selector sio:SIO_000008 ?sim . <br>
   ?text_selector2 sio:SIO_000008 ?sim .<br>
   ?sim sio:SIO_000300 ?value . <br>
} ORDER BY ?pubmed_id ?property_change ?text_selector<br>
}<br>
}<br>
# end correct<br>
<br>
# retrieved<br>
{<br>
SELECT (COUNT (*) as ?retrieved)<br>
WHERE{<br>
SELECT DISTINCT ?pubmed_id ?property_change ?text_selector<br>
WHERE {<br>
  GRAPH &lt;http://example.com/experiment.rdf&gt;<br>
   {<br>
     ?document a sio:SIO_000154 .<br>
      ?document sio:SIO_000629 ?pubmed_record .<br>
      ?pubmed_record sio:SIO_000008 ?pubmed_identifier .<br>
      ?pubmed_identifier sio:SIO_000300 ?pubmed_id .<br>
<br>
      ?ann a ao:Annotation .<br>
      ?ann aof:annotatesDocument ?document .<br>
      ?ann ao:hasTopic ?property_change .<br>
      ?property_change a ?property_change_class .<br>
      ?property_change_class rdfs:subClassOf mieo:ProteinPropertyChange .<br>
<br>
      ?ann ao:context ?text_selector .<br>
      ?text_selector a aos:TextSelector . <br>
     # ?text_selector aos:exact ?text .<br>
<br>
   } .<br>
} ORDER BY ?pubmed_id<br>
<br>
} <br>
}<br>
# end retrieved<br>
<br>
# relevant<br>
{<br>
SELECT (COUNT (*) as ?relevant)<br>
WHERE{<br>
SELECT DISTINCT ?pubmed_id ?property_change ?text_selector<br>
WHERE {<br>
  GRAPH &lt;http://example.com/gold-standard.rdf&gt;<br>
   {<br>
      ?document a sio:SIO_000154 .<br>
      ?document sio:SIO_000629 ?pubmed_record .<br>
      ?pubmed_record sio:SIO_000008 ?pubmed_identifier .<br>
      ?pubmed_identifier sio:SIO_000300 ?pubmed_id .<br>
<br>
      ?ann a ao:Annotation .<br>
      ?ann aof:annotatesDocument ?document .<br>
      ?ann ao:hasTopic ?property_change .<br>
      ?property_change a ?property_change_class .<br>
      ?property_change_class rdfs:subClassOf mieo:ProteinPropertyChange .<br>
<br>
      ?ann ao:context ?text_selector .<br>
      ?text_selector a aos:TextSelector . <br>
     # ?text_selector aos:exact ?text .<br>
<br>
   } .<br>
} ORDER BY ?pubmed_id<br>
<br>
}<br>
}<br>
# relevant<br>
<br>
}<br>
<br>
# returns n results<br>
<br>
</code></pre>

<h2>References</h2>
<br>
<code>[1]</code> <a href='http://www.biomedcentral.com/1471-2164/13/S4/S10'>Laurila JB, Naderi N, Witte R, Riazanov A, Kouznetsov A, Baker CJO: Algorithms and semantic infrastructure for mutation impact extraction and grounding. BMC Genomics 2010, 11(Suppl 4):S24.</a>
<br>
<code>[2]</code> <a href='http://www.semanticsoftware.info/open-mutation-miner'>http://www.semanticsoftware.info/open-mutation-miner</a>
<br>
<code>[3]</code> <a href='http://www.biomedcentral.com/1471-2164/11/S4/S24'>Naderi N, Witte R: Automated extraction and semantic analysis of mutation impacts from the biomedical literature. BMC Genomics 2012, 13(Suppl 4):S10.</a>