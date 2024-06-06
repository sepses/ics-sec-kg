# The ICS-SEC KG (v 1.0)

The ICS-SEC KG is a cybersecurity knowledge graph that integrates and links critical cybersecurity information of Industrial Control System (ICS) and Operational Technology (OT) from various publicly available sources. It extends the existing <a href="https://github.com/sepses/cyber-kg-converter" target="_blank">SEPSES-CSKG</a> by including ICS-related security resource such as <a href="https://www.icsadvisoryproject.com/" terget="_blank">Industrial Control System Advisory (ICSA)</a> and <a href="https://attack.mitre.org/matrices/ics" target="_blank">MITRE ATT&CK for ICS</a>. The Knowledge Graph is continuously updated to reflect the dynamic changes in ICS-Security landscape.


## Integrated ISC-SEC Ontology
The ICS-SEC ontology is depicted in the following figure:

![ ](https://raw.githubusercontent.com/sepses/ics-sec-kg/master/ics-sec-ontology.png)<p align="center"> **Figure 1:** <a href="https://w3id.org/sepses/vocab/ref/ics-sec">Integrated ICS-SEC Ontology</a>.

The integrated ICS-SEC ontology can be found <a href="https://w3id.org/sepses/vocab/ref/ics-sec">here</a>. It consists of seven RDF/OWL-based vocabularies:

| Prefix | Description                               | Namespace and Documentation (click at the link!)                                                                                   |
|--------|-------------------------------------------|----------------------------------------------------------------------------------------|
| attack | MITRE ATT&CK / ATT&CK for ICS                    | <a href="http://w3id.org/sepses/vocab/ref/attack" target="_blank">http://w3id.org/sepses/vocab/ref/attack</a> |
| icsa   | Industrial Control System (ICS) Advisory  | <a href="http://w3id.org/sepses/vocab/ref/icsa" target="_blank">http://w3id.org/sepses/vocab/ref/icsa</a>     |
| capec  | Common Attack Pattern Enumeration and Classification (CAPEC) | <a href="http://w3id.org/sepses/vocab/ref/capec" target="_blank">http://w3id.org/sepses/vocab/ref/capec</a>     |
| cwe    | Common Weakness Enumeration (CWE)         | <a href="http://w3id.org/sepses/vocab/ref/cwe" target="_blank">http://w3id.org/sepses/vocab/ref/cwe</a>         |
| cve    | Common Vulnerabilities and Exposures (CVE) | <a href="http://w3id.org/sepses/vocab/ref/cve" target="_blank">http://w3id.org/sepses/vocab/ref/cve</a>         |
| cvss   | Common Vulnerability Scoring System (CVSS)| <a href="http://w3id.org/sepses/vocab/ref/cvss" target="_blank">http://w3id.org/sepses/vocab/ref/cvss</a>       |
| cpe    | Common Platform Enumeration (CPE)         | <a href="http://w3id.org/sepses/vocab/ref/cpe" target="_blank">http://w3id.org/sepses/vocab/ref/cpe</a>         |

## Querying / Accessing the KG

To query/access the constructed KG, we provide several interfaces: 

| Service Type | Description |  Lisence                               | Persistent URL                                                                                   |
|--------|--------------------|-----------------------|----------------------------------------------------------------------------------------|
| SPARQL Endpoint | Using SPARQL to query the KG   |  MIT License                    | http://w3id.org/sepses/sparql |
| Linked Data Interface  | Browsing the linked data through a web page | MIT License | http://w3id.org/sepses/resource/cpe/product/triton (example)    |
| Linked Data Fragment-Server   |Using <a href="https://ldf-client.sepses.ifs.tuwien.ac.at">Communica</a> to query the KG | MIT License         | http://w3id.org/sepses/ldf         |
| Dump File |KG provided in Turtle (.ttl) and HDT (.hdt) format | MIT License  | http://w3id.org/sepses/dumps/latest   |



## Example Query

```bash
PREFIX cvss: <http://w3id.org/sepses/vocab/ref/cvss#>
PREFIX cve: <http://w3id.org/sepses/vocab/ref/cve#>
PREFIX icsa: <http://w3id.org/sepses/vocab/ref/icsa#> 

SELECT ?cveId ?score  ?icsaId 
WHERE {
  ?cveId cve:hasCPE ?cpe . 
  ?cveId cve:hasCVSS3BaseMetric ?cvss .
  ?cvss cvss:baseScore ?score. 
  ?icsaId icsa:hasCVE ?cveId .
}
 ORDER by DESC(?score)
LIMIT 5
```

another example queries can be found <a href="https://github.com/sepses/ics-sec-kg/tree/master/queries">here</a>.

## RML Mapping & SHACL Validation
To guarantee the quality and consistency of the constructed knowledge graph, ICS-SEC leveraged a declarative RDF Mapping (i.e., RML) and used SHACL to validate them. The following snippets depicts an example of RML mapping for ICSA resource from JSON file and its SHACL rule. 
### RML Mapping Example for ICSA:
```bash
..
  <#SubjectMapping> a rr:TriplesMap ;
  rml:logicalSource [
    rml:source [
        a carml:Stream ;
    ] ;
    rml:referenceFormulation ql:JSONPath ;
    rml:iterator "$"
  ] ;

  rr:subjectMap [
    rr:template "http://w3id.org/sepses/resource/icsa/{document.tracking.id}" ;
  ] ;

  rr:predicateObjectMap [
    rr:predicate rdf:type;
        rr:objectMap [ rr:template "http://w3id.org/sepses/vocab/ref/icsa#ICSA" ];
    ];

rr:predicateObjectMap [
        rr:predicate dcterm:identifier;
        rr:objectMap [ rml:reference  "document.tracking.id" ];
        rr:datatype rdfs:Literal ;
    ];
...
```
The complete RML mapping be found <a href="https://github.com/sepses/ics-sec-kg/tree/master/src/main/resources/rml">here</a>.

### SHACL Rule Example for ICSA
```bash
...
ssr:icsaValidator
    a               sh:NodeShape ;
    sh:closed       true ;
    sh:name         "ICSA Class validation" ;
    sh:description  "ICSA Class validation" ;
    sh:property     [ sh:maxCount  1 ;
                      sh:minCount  1 ;
                      sh:path      rdf:type
                    ] ;
    sh:property     [ sh:maxCount  1 ;
                      sh:minCount  1 ;
                      sh:path      dcterm:identifier
                    ] ;
    sh:property     [ sh:maxCount  1 ;
                      sh:minCount  1 ;
                      sh:path      dcterm:title
                    ] ;
    sh:property     [ sh:maxCount  1 ;
                      sh:minCount  1 ;
                      sh:path      capec:issued
                    ] ;
    sh:property     [ sh:minCount  1 ;
                      sh:path      icsa:hasCVE ;
                      sh:dataType  cve:CVE
                    ] ;
    sh:property     [ sh:minCount  1 ;
                      sh:path      icsa:hasProduct ;
                      sh:dataType  cpe:Product
                    ] ;
    sh:targetClass  icsa:ICSA
...
```
The complete SHACL rule be found <a href="https://github.com/sepses/ics-sec-kg/tree/master/src/main/resources/shacl">here</a>.

## ICS-SEC KG Construction Pipeline Installation
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

## License

The ICS-SEC KG is written by SEPSES team and released under the [MIT license](http://opensource.org/licenses/MIT).



