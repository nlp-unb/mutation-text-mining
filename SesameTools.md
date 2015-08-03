# Sesame Command Line Utilities #
[Download](http://mutation-text-mining.googlecode.com/files/sesame-utils-1.0-jar-with-dependencies.jar)

**Get help:** java -cp `sesame-utils-1.0-jar-with-dependencies.jar` ca.unbsj.cbakerlab.sesame\_utils.SesameCLI -?

<br>
<b>Usage example:</b> Add <code>input.rdf</code> to experiment-repo repository on server <code>http://localhost:8080/openrdf-sesame</code> with context <code>http://example.com/gold-standard.rdf</code>

java -cp benchmark/sesame-utils-1.0-jar-with-dependencies.jar ca.unbsj.cbakerlab.sesame_utils.SesameCLI <br>
-t add <br>
-i input.rdf <br>
-s <code>http://localhost:8080/openrdf-sesame</code> <br>
-r experiment-repo <br>
-c <code>http://example.com/gold-standard.rdf</code>