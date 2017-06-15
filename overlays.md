### Overlays and Extensions

YAML specifications might need to be extended in a variety of ways for different needs. 
Extending an YAML definition by adding to its behavior, or overriding certain aspects, 
is another way to satisfy different needs. This specification provides two mechanisms for extending specification definitions:
overlays and extensions.

Overlays and extensions are document fragments that add or override nodes of a specification definition. 

An overlay or extension document MUST contain a root-level `extends` node whose value MUST be the location of a valid specification definition or another overlay or extension; the location specification is an absolute or relative path, or a URL, equivalent to an [!include tag argument](#includes). The document specified in the `extends` node is called the master  document.

The remainder of an overlay or extension document follows the same rules as a original specification definition, but with certain [restrictions](#overlays) in case of an overlay.

To apply an overlay or extension, specifications processors MUST apply the [merging algorithm](#merging-rules) to the master  document and the extension or overlay, thereby producing a modified specification definition; in the case of applying an overlay, the modified API definition is then validated against the master document to adhere to the restrictions on overlays.

To apply any combination of overlays and/or extensions, all must share the same master specification document. The application process is:

1. Apply the first overlay or extension to the master specification document, producing a modified specification definition and validating the result in the case of an overlay.
2. Apply the second overlay or extension to the modified specification definition as if the latter were the master specification document, and again validate the result in the case of an overlay.
3. Repeat the previous step until the last overlay or extension is applied.
4. Resolve all !include tags before any application of the merging algorithm, validate restrictions on overlays after each overlay is applied, and apply all inheritances of types, resource types, traits, and annotation types.

#### Overlays

An overlay adds or overrides nodes of a specification definition while preserving its behavioral, functional aspects. Certain nodes of a specification definition specify the behavior of an specification. Let's name this node - Functional Nodes.

In contrast, other nodes, such as descriptions or annotations, address concerns beyond the functional interface, such as the human-oriented descriptive documentation in some language, or implementation or verification information for use in automated tools. These nodes can be changed by applying an overlay. Let's name this nodes - Informative Nodes.

Overlays are particularly important for separating interface from implementation. Overlays enable separate lifecycles for the behavioral aspects of the specification that need to be controlled tightly, such as a contract between the specification provider and its consumers, versus those needing little control, such as the human- or implementation-oriented aspects that can evolve at different paces. For example, adding hooks for testing and monitoring tools, appending metadata relevant to a registry of specification, or providing updated or translated human documentation can be achieved without changing any aspects of the behavioral aspects of the specification. These things can be controlled through a rigorous version and change management process.

It is difficult to draw a definitive line between the behavioral and implementation-oriented aspects of the specification because, for example, some semantics of the specification are often captured only in human documentation. 

Some specification languages does, however, define the specific behavior-invariance restrictions on overlay files that processors MUST follow. Processors can then choose to offer the master specification definition as well as its modifications after applying one or more overlays, so the consumer can benefit from all the information available. For example, if overlay files are provided as a means of localizing textual descriptions of resources, methods, and data, the consumer of generated documentation can be offered a choice of which localized overlays to apply.

The behavior-invariance restrictions of an overlay are defined as follows: after applying the [merging algorithm](#merging-rules) as well as application of resource types and traits, the tree of nodes in the merged document is compared with the tree of nodes in the master YAML document after resolving all modularization related things. Any differences in the documents MUST be only in the informative nodes.

```yaml
#%RAML 1.0
# This file is located at librarybooks.raml
title: Book Library API
documentation:
  - title: Introduction
    content: Automated access to books
  - title: Licensing
    content: Please respect copyrights on our books.
/books:
  description: The collection of library books
  get:
```

```yaml
#%RAML 1.0 Overlay
usage: Spanish localization
extends: librarybooks.raml
documentation:
  - title: Introducción
    content: El acceso automatizado a los libros
  - title: Licencias
    content: Por favor respeta los derechos de autor de los libros
/books:
  description: La colección de libros de la biblioteca
```

```yaml
#%RAML 1.0 Overlay
usage: Hints for monitoring the library books API
extends: librarybooks.raml
annotationTypes:
  monitor:
    properties:
      frequency:
        properties:
          interval: integer
          unitOfMeasure:
            enum: [ seconds, minutes, hours ]
      script:
/books:
  get:
    (monitor):
      frequency:
        interval: 5
        unitOfMeasure: minutes
      script: randomBooksFetch
```

#### Extensions

An extension broadens a specification definition by adding to, or modifying aspects of its behavior and other functionality. An extension can be useful in separating a core, broadly-available specofication from layers of functionality available to more restricted audiences, for creating variants of an specification for somewhat different purposes, or for specifying instance-specific nodes of an specification, such as its service endpoint (URL) without altering its pure interface definition document.

The following examples using RAML 1.0 language build on examples in the Overlays section by adding an extension for admins to add book items to a collection, adding an overlay to provide a translation of the added functionality, and adding an extension that locates a particular service endpoint of the API.

```yaml
#%RAML 1.0 Extension
usage: Add administrative functionality
extends: librarybooks.raml
/books:
  post:
    description: Add a new book to the collection
```

```yaml
#%RAML 1.0 Overlay
usage: Spanish localization for admin functionality
extends: librarybooks.raml
/books:
  post:
    description: A?adir un nuevo libro para la colecci?n
```

```yaml
#%RAML 1.0 Extension
usage: The location of the public instance of the Piedmont library API
extends: librarybooks.raml
baseUri: http://api.piedmont-library.com
```

#### Merging Rules

This section describes how an overlay/extension structure is applied to the master.  

##### Terminology

**Object & Property**

_Object_ is a MAP or a SEQUENCE containing MAPPINGS in terms of YAML.

_Property_ is a MAPPING in terms of YAML, a key and its value pair.

In the following example, the yellow "properties" is a _Property_ key, and the corresponding green _Object_ is the value.

<pre style="background-color:#111;">
<font color="yellow">properties:</font>
<font color="#44ff44">  statusCode: 200</font>
<font color="#44ff44">    responseParameters:</font>
<font color="#44ff44">      type: object</font>
<font color="#44ff44">      description: "some description"</font>
</pre>

In the same example, there is also a green "responseParameters" _Property_ key and its _Object_ value:

<pre style="background-color:#111;">
<font color="white">properties:</font>
<font color="white">  statusCode: 200</font>
<font color="yellow">    responseParameters:</font>
<font color="#44ff44">      type: object</font>
<font color="#44ff44">      description: "some description"</font>
</pre>

And while the yellow "statusCode", "type" and "description" are also properties, their values are not _Objects_:

<pre style="background-color:#111;">
<font color="white">properties:</font>
<font color="yellow">   statusCode:</font> <font color="white">200</font>
<font color="white">   responseParameters:</font>
<font color="yellow">       type:</font> <font color="white">object</font>
<font color="yellow">       description:</font> <font color="white">"some description"</font>
</pre>

In the following sample, yellow "FilteredByPrice" and "Paged" are _Properties_ with green _Object_ values.

<pre style="background-color:#111;">
<font color="white">traits:</font>
<font color="yellow"> - FilterableByPrice:</font>
<font color="#44ff44">     queryParameters:</font>
<font color="#44ff44">       priceLessThen?:</font>
<font color="#44ff44">         type: number</font>
<font color="#44ff44">       priceMoreThen?:</font>
<font color="#44ff44">        type: number</font>
<font color="yellow"> - Paged:</font>
<font color="#44ff44">     queryParameters:</font>
<font color="#44ff44">       offset: number</font>
<font color="#44ff44">       length: number</font>
</pre>

**Array**

_Array_ is a SEQUENCE containing SCALARs or SEQUENCE containing MAPs in terms of YAML.

In the following example, the yellow "enum" _Property_ key has a blue _Array_ value.

<pre style="background-color:#111;">
<font color="yellow">enum:</font>
<font color="#4444ff"> - White</font>
<font color="#4444ff"> - Black</font>
<font color="#4444ff"> - Colored</font>
</pre>

In this example of an _Array_ definition, a "documentation" _Property_ key has an _Array_ value that contains two green _Objects_:

<pre style="background-color:#111;">
<font color="yellow">documentation:</font>
<font color="#44ff44">- title: Introduction</font>
<font color="#44ff44">  content: Automated access to books</font>

<font color="#44ff44">- title: Licensing</font>
<font color="#44ff44">  content: Please respect copyrights on our books.</font>
</pre>

**Property Types**

In the merging algorithm, the _Property_ types are referred to as _Property Kind_, which can be one of the following properties highlighted in **bold**:

**_Object Property_** - a _Property_ having _Object_ as a value.

**_Array Property_** - a _Property_ having _Array_ of _Objects_ as a value.

**_Simple property_** - a _Property_ having YAML SCALAR or a SEQUENCE of YAML SCALARS as a value.

**_Single-value Simple Property_** - a Simple property having YAML SCALAR value.

**_Multi-value Simple Property_** - a Simple property having a SEQUENCE of YAML SCALARS as value.

**_Object Simple Property_** - a _Property_ having _Object_ as a value but acting as a Simple Property.

**Conflicting Properties**

Conflicting properties are the properties that cannot coexist in the same Object.

**Ignored properties**

_Ignored Properties_ - Some properties may be declared as ignored.

**The Trees**

_Master Tree_ - Master file document YAML parsing tree result.
_Extension Tree_ - overlay or extension YAML parsing tree result.
_Target Tree_ - the result tree.

**Merging Algorithm:**

Master document and Extension or Overlay are parsed by YAML parser to produce _Master Tree_ and _Extension Tree_.

_Master Tree_ and _Extension Tree_ are validated, in case of an error the merge process is cancelled.

All _includes_ are resolved and applied for both _Master Tree_ and _Extension Tree_.

All _uses_ are resolved and applied for both _Master Tree_ and _Extension Tree_. The trees MUST NOT have _uses_ instructions with the same namespace referring to different files.

All Trait and Resource Types applications are applied in the _Master Tree_

Initially, _Target Tree_ is made equal to the _Master Tree_.

**Current Extension Tree Object** is set to the _Extension Tree_ root (API).
**Current Target Tree Object** is set to the _Target Tree_ root (API).

For each **Current Extension Tree Object property** the following is done:

* If the **property** is an _Ignored Property_, continue to process the next property.
* If the **property** with the same name exists in **Current Target Tree Object**:
  * If the **property** and the identically named property in **Current Target Tree Object** have different _Property Kind_:
    * The **property** value in the identically named **Current Target Tree Object** property is replaced with its value from **Current Extension Tree Object** property.
  * If the **property** is a _Simple Property_
    * If the **property** is a _Single-value Simple Property_,
      * The **property** value in the identically named **Current Target Tree Object** property is replaced with its value from **Current Extension Tree Object** property.
    * If the **property** is a _Multi-value Simple Property_
      * The **property** value from **Current Extension Tree Object** property is added to the identically named **Current Target Tree Object** property values if no such value already exists.
  * If the **property** is an _Object Property_:
    * The same _Merging Algorithm_ is recursively performed for **Current Extension Tree Object** being set to the **property** value, and **Current Target Tree Object** being set to the value of the identically named property in **Current Target Tree Object**.
  * If the **property** is an _Array Property_:
    * _Objects_ from the property value are added to the identically named **Current Target Tree Object** property value.
* If the **property** with the same name does not exist in **Current Target Tree Object**:
  * All _Conflicting Properties_ are removed from the **Current Target Tree Object**
  * The **property** is added to the **Current Target Tree Object**.

_Target Tree_ is validated.

If the _Extension Tree_ is an Overlay, _Target Tree_ is compared to the _Master Tree_. There MUST NOT be any differences, except of the differences in informative nodes. Otherwise the process is cancelled.

_Target Tree_ has its resource types and Traits applied.

_Target Tree_ is being serialized to a document or returned as the algorithm output.
