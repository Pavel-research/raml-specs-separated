## Markup Language

RAML dialects use [YAML 1.2](http://www.yaml.org/spec/1.2/spec.html) as its underlying format. 
YAML is a human-readable data format that aligns well with the design goals of this specification. 
As in YAML, all nodes such as keys, values, and tags, are case-sensitive.

RAML dialects definitions are YAML 1.2-compliant documents 
that begin with a REQUIRED YAML-comment line that indicates the dialect name , dialect version and optionally a public fragment name:

```yaml
#%Vehicles 1.0
title: My API
```

The first line of  document MUST begin with the text _#%(Dialect name)_ followed by a single space
followed by the text version of the dialect used in the document and nothing else before the end of the line. 
public fragment documents begin similarly with the dialect version comment and a public fragment identifier, 

The media type _application/raml+yaml_ and its associated file extension _.raml_ 
SHALL be used to designate files containing RAML API definitions, RAML fragments, 
and files that contain RAML dialect markups. 

RAML dialects are also capable of including documents of other media types, such as “application/schema+json” and “application/yaml”.

To facilitate the automated processing of documents, we impose the following restrictions and requirements
in addition to the core YAML 1.2 specification:

* The first line of a file consists of a YAML comment that specifies the dialect name and  version. 
Therefore, RAML dialect processors cannot completely ignore all YAML comments.
* The order of some properties at certain levels within a RAML dialect document is significant. Therefore, processors are expected to preserve this ordering.
