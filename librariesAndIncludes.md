## Modularization

RAML provides several mechanisms to help modularize the ecosystem of an API specification:
* Includes
* Libraries
* Overlays
* Extensions

### Includes

RAML processors MUST support the OPTIONAL **!include** tag, which specifies the inclusion of external files into the API specification. Being a YAML tag, the exclamation point ("!") prefix is required. In an API specification, the !include tag is located only in a node value position. The !include tag MUST be the value of a node, which assigns the contents of the file named by the !include tag to the value of the node.

In the following example, the set of types to be used in the API specification is retrieved from a file called myTypes.raml and used as the value of the types node.

```yaml
#%RAML 1.0
title: My API with Types
types: !include myTypes.raml
```

The !include tag accepts a single argument, the location of the content to be included, that MUST be specified explicitly. The value of the argument MUST be a path or URL as described in the following table:

|Argument | Description | Examples |
|:--------|:------------|:---------|
| absolute path | A path that begins with a single slash ("/") and is interpreted relative to the root RAML file location. | /traits/pageable.raml
| relative path | A path that neither begins with a single slash ("/") nor constitutes a URL, and is interpreted relative to the location of the included file. | description.md<br>../traits/pageable.raml
| URL | An absolute URL | http://dev.domain.com/api/patterns/traits.raml

To simplify the API definition, and because the parsing context of the included file is not shared between the file and its parent, an included file SHALL NOT use a YAML reference to an anchor in a separate file. Likewise, a reference made from a parent file SHALL NOT reference an anchor defined in an included file.

The !include tag argument must be static: namely, it MUST NOT contain any resource type parameters or trait parameters.

#### Typed Fragments

A file to be included MAY begin with a RAML fragment identifier line, which consists of the text _#%RAML_ followed left-to-right by a single space, the text 1.0, a single space, and one of the following fragment identifiers:

|Fragment Identifier | Description | Relevant RAML Specification Section |
|:-------------------|:------------| :-----------------------------------|
| DocumentationItem | An item in the collection of items that is the value of the root-level documentation node | [User Documentation](#user-documentation)
| DataType | A data type declaration where the type node may be used | [Types](#defining-types)
| NamedExample | A declaration of the examples facet, whose key is a name of an example and whose value describes the example | [Examples](#defining-examples-in-raml)
| ResourceType | A single resource type declaration | [Resource Types and Traits](#resource-types-and-traits)
| Trait | A single trait declaration | [Resource Types and Traits](#resource-types-and-traits)
| AnnotationTypeDeclaration | A single annotation type declaration | [Annotations](#annotations)
| Library | A RAML library | [Libraries](#libraries)
| Overlay | An overlay file | [Overlays](#overlays)
| Extension | An extension file | [Extensions](#extensions)
| SecurityScheme | A definition of a security scheme | [Security Schemes](#security-schemes)

If a file begins with a RAML fragment identifier line, and the fragment identifier is not Library, Overlay, or Extension, the contents of the file after removal of the RAML fragment identifier line MUST be valid structurally according to the relevant RAML specification. For example, a RAML file beginning with `#%RAML 1.0 Trait` must have the structure of a RAML trait declaration as defined in the [Resource Types and Traits](#resource-types-and-traits) section. Including the file in a correct location for a trait declaration results in a valid RAML file.

The following example shows a RAML fragment file that defines a resource type and a file that includes this fragment file.

```yaml
#%RAML 1.0 ResourceType

#This file is located at resourceTypes/collection.raml

description: A collection resource
usage: Use this to describe a resource that lists items
get:
  description: Retrieve all items
post:
  description: Add an item
  responses:
    201:
      headers:
        Location:
```

```yaml
#%RAML 1.0
title: Products API
resourceTypes:
  collection: !include resourceTypes/collection.raml
/products:
  type: collection
  description: All products

```

The resulting API definition is equivalent to the following single document:

```yaml
#%RAML 1.0
title: Products API
resourceTypes:
  collection:
    description: A collection resource
    usage: Use this to describe a resource that lists items
    get:
      description: Retrieve all items
    post:
      description: Add an item
      responses:
        201:
          headers:
            Location:
/products:
  type: collection
  description: All products
```

#### Resolving Includes

When RAML or YAML files are included, RAML parsers MUST NOT only read the content, but must also parse it and add the content to the declaring structure as if the content were declared inline. RAML parsers MUST parse the content of the file as RAML content and append the parsed structures to the RAML document node if the included file has a .raml, .yml, or .yaml extension or one of the following media types:

* application/raml+yaml
* text/yaml
* text/x-yaml
* application/yaml
* application/x-yaml

Otherwise, if RAML parsers fail to parse the content and append structures, the contents of the file are included as a scalar.

Because the parsing context of the included files is not shared between the included file and its parent, an included file SHALL NOT use a YAML reference to an anchor in a separate file. Likewise, a reference made from a parent file SHALL NOT reference a structure anchor defined in an included file. These rules simplify RAML definitions.

In the example below, the API root document includes two files from the patterns folder, one containing resource type declarations and the other containing trait declarations.

```yaml
#%RAML 1.0
title: Example API
version: v1
resourceTypes: !include patterns/resourceTypes.raml
traits: !include patterns/traits.raml
```

```yaml
# This file is located at patterns/resourceTypes.raml

collection:
  get:
    is: paged
  post:
member:
  get:
  patch:
  delete:
```

```yaml
# This file is located at patterns/traits.raml

chargeable:
  headers:
    dept_code:
paged:
  queryParameters:
    start:
      type: number
```

The resulting API definition is equivalent to the following single document.

```yaml
#%RAML 1.0
title: Example API
version: v1
resourceTypes:
  collection:
    get:
      is: paged
    post:
  member:
    get:
    patch:
    delete:
traits:
  chargeable:
    headers:
      dept_code:
  paged:
    queryParameters:
      start:
        type: number
```

### Libraries

RAML libraries are used to combine any collection of data type declarations, resource type declarations, trait declarations, and security scheme declarations into modular, externalized, reusable groups. While libraries are intended to define common declarations in external documents, which are then included where needed, libraries can also be defined inline.

The following table describes the nodes, which are optional, of a library node.

|Name | Description |
|:--------|:------------|
| types?<br>schemas?<br>resourceTypes?<br>traits?<br>securitySchemes?<br>annotationTypes?<br>(&lt;annotationName&gt;)?<br>uses? | The definition of each node is the same as that of the corresponding node at the root of a RAML document. A library supports annotation node like any other RAML document.
| usage | Describes the content or purpose of a specific library. The value is a string and MAY be formatted using [markdown](#markdown).

The following example shows a simple library as a standalone, reusable RAML fragment document.

```yaml
#%RAML 1.0 Library
usage: |
  Use to define some basic file-related constructs.
types:
  File:
    properties:
      name:
      length:
        type: integer
traits:
  drm:
    headers:
      drm-key:
resourceTypes:
  file:
    get:
      is: [ drm ]
    put:
      is: [ drm ]
```

#### Applying Libraries

Any number of libraries can be applied by using the OPTIONAL **uses** node ONLY at the root of a ["master"] RAML or RAML fragment file. The value of the uses node is a map of key-value pairs. The keys are treated as library names, or namespaces, and the value MUST be the location of a RAML library file, usually an external RAML library fragment document.

In a document that applies a library, the data types, resource types, traits, security schemes, and annotation types in the library become available within the document. The assets in the library are referenced within the document using dot notation as follows: concatenate the library name followed by a period ("."), followed by the name of the data type, resource type, trait, security scheme, or annotation type. The library name defines a namespace for the library nodes within the context in which the library is applied. Namespaces defined in a **uses** statement in a specific file are only consumable within that file and serve only to disambiguate the included libraries from each other. Therefore, any processor MUST NOT allow any composition of namespaces using "." across multiple libraries.

Using **uses** does NOT automatically import the remote library assets into the local file, but the local file can import those assets by referring to the assets from its contents. For example, a RAML type fragment file that uses a library of remote types can import one of those types by referring to it. The remote type is included as if it were defined locally within the RAML type fragment file.

The following examples demonstrate the use of a library in a second library, a second library in a resource type fragment, and a second library in a RAML API definition.

```yaml
#%RAML 1.0 Library
# This file is located at libraries/file-type.raml
types:
  File:
    properties:
      name:
      length:
        type: integer
```

```yaml
#%RAML 1.0 Library
# This file is located at libraries/files.raml
usage: |
  Use to define some basic file-related constructs.
uses:
  file-type: file-type.raml
traits:
  drm:
    headers:
      drm-key:
resourceTypes:
  file:
    get:
      is: [ drm ]
      responses:
        201:
          body:
            application/json:
              type: file-type.File
    put:
      is: [ drm ]

```

```yaml
#%RAML 1.0 ResourceType
# This file is located at files-resource.raml
uses:
  files: libraries/files.raml
get:
  is: [ files.drm ]
```

The following example is not valid because chaining namespaces is not allowed.

```yaml
#%RAML 1.0 ResourceType
# Invalid RAML Fragment
uses:
  files: libraries/files.raml
get:
  is: [ files.drm ]
  responses:
    200:
      body:
        application/json:
          type: files.file-type.File # invalid - no chaining allowed
```
