# String Alignment Tool #
The provenance enhancement utility helps in situations when the sources of annotation data only provide fragments of texts as provenance, without specifying their positions in the text, such as in the Enzyminer and OMM Impact corpora. Note that the annotations in the OMM Impact corpus have position numbers, but, since the original text is not provided, the alignment of annotations with the original text still requires an additional simple program.<br>
We implemented a procedure to align corresponding text fragments based on their similarity. To calculate a similarity score for two fragment candidates we use the implementation of Levenstein distance algorithm from Lucene. If fragments had differing lengths, we calculated the similarity score for their overlap. We avoided multiple alignments – each text fragment in a systems results can<br>
be aligned to the gold standard only once. Two fragments get aligned if the similarity score is above the threshold. We used a threshold equal to 0.83 to boost the precision on the Enzyminer corpus used for training to 100%. The price for this is a slight decrease in recall, which still reaches 0.99. The procedure achieved >0.99 for both precision and recall on the test corpus – the OMM Impact corpus.<br>
Technically, the tool tries to align all instances of aos:TextSelector class in two graphs and adds 4 statements/triples:<br>
<pre><code>   :sim1 a mieo:StringSimilarity .<br>
   :text_selector_1 sio:'has attribute' :sim1 . # SIO_000008<br>
   :text_selector_2 sio:'has attribute' :sim1 . # SIO_000008<br>
   :sim sio:'has value' :value . # SIO_000300<br>
</code></pre>

<h2>Download</h2>
String Aligner is part of <i>mutation-benchmark</i> project. Download jar file <a href='http://code.google.com/p/mutation-text-mining/downloads/detail?name=mutation-benchmark-1.0-SNAPSHOT-jar-with-dependencies.jar&can=2&q='>HERE</a> <br>
Source Code is available <a href='http://code.google.com/p/mutation-text-mining/downloads/detail?name=mutation-benchmark-1.0-SNAPSHOT-project.zip&can=2&q='>HERE</a>

<h2>Usage</h2>
Interface:<br>
<pre><code>java -cp mutation-benchmark-&lt;version&gt;-jar-with-dependencies.jar \<br>
ca.unbsj.cbakerlab.mutation_benchmark.SimilarStringAlignerCLI \<br>
-s &lt;sesame-server-url&gt; \<br>
-r &lt;sesame-remository-name&gt; \<br>
-g &lt;context-1&gt; \<br>
-e &lt;context-2&gt; \<br>
--mode &lt;mode&gt;<br>
</code></pre>
Note: In mode "run": all alignments will be applied; in "simulate" mode, alignments are not applied to the repository)<br>
<br><br>
Example: align strings in <i>impact-omm</i> repository on <i><code>http://localhost:8080/openrdf-sesame</code></i> between two graphs - <i><code>http://example.com/gold-standard.rdf</code></i> and <i><code>http://example.com/experiment.rdf</code></i> in <i>run</i> mode .<br>
<pre><code>java -cp mutation-benchmark-1.0-jar-with-dependencies.jar \<br>
ca.unbsj.cbakerlab.mutation_benchmark.SimilarStringAlignerCLI \<br>
-s http://localhost:8080/openrdf-sesame \<br>
-r impact-omm \<br>
-g http://example.com/gold-standard.rdf \<br>
-e http://example.com/experiment.rdf \<br>
--mode run<br>
</code></pre>

<h2>Delete alignment</h2>
<pre><code>PREFIX rdf:&lt;http://www.w3.org/1999/02/22-rdf-syntax-ns#&gt;<br>
PREFIX mieo:&lt;http://unbsj.biordf.net/ontologies/mutation-impact-extraction-ontology.owl#&gt;<br>
PREFIX sio:&lt;http://semanticscience.org/resource/&gt;<br>
DELETE WHERE {<br>
   ?sim a mieo:StringSimilarity .<br>
   ?str1 sio:SIO_000008 ?sim . <br>
   ?str2 sio:SIO_000008 ?sim .<br>
   ?sim sio:SIO_000300 ?value .<br>
}<br>
</code></pre>