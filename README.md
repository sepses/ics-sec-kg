# The ICS-SEC KG Engine (v 1.0)

The ICS-SEC KG is a cybersecurity knowledge graph that integrates and links critical cybersecurity information of Industrial Control System (ICS) and Operational Technology (OT) from various publicly available sources. It extends the existing <a href="https://github.com/sepses/cyber-kg-converter" target="_blank">SEPSES-CSKG</a> by including ICS related security resource such as <a href="https://www.icsadvisoryproject.com/" terget="_blank">Industrial Control System Advisory (ICSA)</a> and <a href="https://attack.mitre.org/matrices/ics" target="_blank">MITRE ATT&CK Matrics for ICS</a> resources. The Knowledge Graph is continuously updated to reflect the dynamic changes in ICS-Security landscapes.


## Integrated ISC-SEC Ontology
The ICS-SEC KG leveraged an integrated ontology as depicted in the following figure:

![ ](https://raw.githubusercontent.com//sepses/main/ics-sec-ontology.png)<p align="center"> **Figure 1:** Integrated ICS-SEC Ontology.

It consist of several vocabularies are developed to represent the ICS-SEC knowledge graphs in an integrated manner, as follows:

| Prefix | Description                               | Link                                                                                   |
|--------|-------------------------------------------|----------------------------------------------------------------------------------------|
| attack | MITRE ATT&CK Framework                    | <a href="http://w3id.org/sepses/vocab/ref/attack" target="_blank">http://w3id.org/sepses/vocab/ref/attack</a> |
| capec  | Common Attack Pattern Enumeration and Classification (CAPEC) | <a href="http://w3id.org/sepses/vocab/ref/capec" target="_blank">http://w3id.org/sepses/vocab/ref/capec</a>     |
| cwe    | Common Weakness Enumeration (CWE)         | <a href="http://w3id.org/sepses/vocab/ref/cwe" target="_blank">http://w3id.org/sepses/vocab/ref/cwe</a>         |
| icsa   | Industrial Control System (ICS) Advisory  | <a href="http://w3id.org/sepses/vocab/ref/icsa" target="_blank">http://w3id.org/sepses/vocab/ref/icsa</a>     |
| cve    | Common Vulnerabilities and Exposures (CVE) | <a href="http://w3id.org/sepses/vocab/ref/cve" target="_blank">http://w3id.org/sepses/vocab/ref/cve</a>         |
| cvss   | Common Vulnerability Scoring System (CVSS)| <a href="http://w3id.org/sepses/vocab/ref/cvss" target="_blank">http://w3id.org/sepses/vocab/ref/cvss</a>       |
| cpe    | Common Platform Enumeration (CPE)         | <a href="http://w3id.org/sepses/vocab/ref/cpe" target="_blank">http://w3id.org/sepses/vocab/ref/cpe</a>         |


## Installation

### Requirements

To run this prototype, the prerequisite is that you have a JDK 8+ and Maven installed in your computer.

### Configuration
Additionally, the config.properties is build for local Jena fuseki installation. Make sure that: 
* the `config.properties` is available (and adjust it if necessary; especially with regards to the triplestore/fuseki installation)
* you have an empty repo called 'sepses' in your fuseki/virtuoso installation
    * you can also run it without storing the data to triplestore using "dummy" as storage
    * currently still need an active sparql endpoint (TODO: to fix this).


### Run the Code

The following steps are required to run the engine: 
* run `mvn clean` to build the required jar files from the `lib` folder
* run `mvn install -DskipTests=true` to build the application
    * optionally, you can also run the tests (without the `-DskipTests=true`) to run checks of extracted data against a set of SHACL constraints to make sure that the conversion for each source is correctly defined
* run `java -jar target/cyber-kb-<version>-jar-with-dependencies.jar -p <type-of-source>` 
    * replace `<type-of-source>` with one of the following: capec, cwe, cve, cat, icsa )
    * replace `<version>` with the version of the Cyber-KB
    * (optional) you can also add `-v` as parameter to activate SHACL constraint checking 
        * Note: this option may add a significant time to the process (especially for CPE)

The prototype will then 
* (i) generate the RDF graph from these sources and create necessary linking
* (ii) (*optional*) check the generated RDF data against a set of SHACL constraints (using constraints from `src/main/resources/shacl/*.ttl`)
* (iii) store the data in the triplestore

We have tried and tested it in OSX (Intel i7@3,1GHz, OSX Mojave, 16GB RAM). 
The benchmark result (excluding SHACL check) is available in the following [link](https://github.com/sepses/cyber-kg-converter/blob/master/doc/benchmark.png)

## Access Services

Example queries are now added (`example-queries.txt`), which can be tested in our [SPARQL endpoint](https://w3id.org/sepses/sparql).

Other interface beyond SPARQL are also provided, such as [Linked Data Interface](https://sepses.ifs.tuwien.ac.at/index.php/cyber-kg/), [Triple Pattern Fragment](http://ldf-server.sepses.ifs.tuwien.ac.at/) and [Dump-files](https://sepses.ifs.tuwien.ac.at/index.php/datasets/)   (in .turtle and .HDT).


@SEPSES team
