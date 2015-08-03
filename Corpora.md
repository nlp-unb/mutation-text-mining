

# Available corpora #

## EnzyMiner corpus ##
One of our seed corpora is based on an extract from the EnzyMinner (link) abstract database. It was annotated manually and comprises 38 randomly selected full text documents with 176 different mutations linked to 49 different protein sequences.
In what follows, we call it simply “EnzyMiner corpus”.

We annotated documents with mutation impact information which includes:
  1. Studied protein-level mutations, in the form of singular amino acid substitutions. They are represented as triples specifying the wild type and mutant residues, and the absolute positions of the mutations on the corresponding amino acid sequences. For situations when the effects of several simultaneous amino acid substitutions are studied, we allow grouping them in what we call combined mutations.
  1. Proteins to which the mutations are related, identified with UniProt IDs. The host organisms and sets of specific protein sequences can be identified via the UniProt IDs.
  1. Molecular functions specified as Gene Ontology (link) classes.
  1. Mutation impacts qualified as Positive, Negative or Neutral.
  1. Text fragments the information was extracted from. Typical fragments contain mentions of protein properties, impact directionality words, such as “increased” or “worse”, mutation mentions, protein and organism names, etc.
  1. Documents identified with PubMed IDs.

## DHLA corpus ##
This is a small corpus comprising 13 documents with 54 unique mutations on Haloalkane Dehalogenases, manually annotated similarly to the EnzyMiner documents.

## COSMIC-based corpus ##
We have an extract from the COSMIC database (link) containing 63 documents for three target genes: FGFR3, MEN1 and PIK3CA. Unlike the EnzyMiner and DHLA corpora, this corpus does not identify mutation impacts, although it links mutations to proteins and, thus, is suitable for mutation grounding benchmarking.

## KinMutBase-based corpus ##
We retrieved 201 documents annotated with singular amino acid substitutions grounded to proteins, from the [KinMutBase](http://bioinf.uta.fi/KinMutBase/) database. We additionally curated the selection by running [MutationFinder](http://mutationfinder.sourceforge.net), which is a reliable tool for this purpose due to its very high recall, and comparing the results with the annotations
in the database. Based on this comparison, we discarded about 70 documents that appear annotated with protein-level mutations that they don’t seem to mention directly, although this may be due to the translation from SNPs made by the curators. The final size of the corpus is 128 documents. In total, we have 271 mutations linked to 26 different UniProt identifiers.

## OMM Impact corpus ##
This corpus containing 40 documents was used in `[1](1.md)' to test the Open Mutation Miner system. It contains impact sentence annotations with the EC codes of enzymes, host organisms and mutations. An impact sentence describes a mutation impact on a protein property and does not necessarily contain a mutation
mention. 48 of 2045 impact sentences were not grounded to mutations. If a sentence contained several impact mentions, it was annotated several times. Unlike the Enzyminer corpus, the OMM Impact corpus was not annotated with protein properties or mutation impact direction.

# Statistics #
| | **Number of documents** | **UniProt IDs** | **Mutations** |
|:|:------------------------|:----------------|:--------------|
| EnzyMiner | 38                      | 49              | 176           |
| KinMutBase | 128                     | 26              | 271           |
| DHLA | 13                      | 4               | 52            |
| PIK3CA | 30                      | 1               | 169           |
| FGFR3 | 26                      | 1               | 174           |
| MEN1 | 7                       | 1               | 22            |

| | **Number of documents** | **Impact mutations** | **Impacts** (mutation, protein property, impact direction) | **Impact sentences** | **Impact sentences grounded to mutations** |
|:|:------------------------|:---------------------|:-----------------------------------------------------------|:---------------------|:-------------------------------------------|
| OMM Impact | 40                      | 223                  | -                                                          | 2045                 | 1997                                       |
| EnzyMiner | 38                      | 172                  | 282                                                        | 440                  | 440                                        |
| DHLA | 13                      | 52                   | 73                                                         | -                    | -                                          |

# Format #
Corpora are available in RDF format.
**You can obtains them by simple registration [HERE](RegistrationPage.md)**
To see annotation in table format use Sesame Query Tool (link to downloads) to convert RDF to CSV.

# Demo corpus #
[Here](http://code.google.com/p/mutation-text-mining/wiki/DemoCorpus) you can try demo corpus, example experimental data, try example queries, etc.

# References #
<br>
<code>[1]</code> <a href='http://www.biomedcentral.com/1471-2164/13/S4/S10'>Laurila JB, Naderi N, Witte R, Riazanov A, Kouznetsov A, Baker CJO: Algorithms and semantic infrastructure for mutation impact extraction and grounding. BMC Genomics 2010, 11(Suppl 4):S24.</a>
<br>