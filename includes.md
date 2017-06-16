### Includes

Includes operate on the structure resolution level. If your fragment supports includes just inherit it from 'HasIncludes' class. Only public fragments may support includes.

Specification processors MUST support the OPTIONAL **!include** tag,
which specifies the inclusion of external files into the  specification. Being a YAML tag, 
the exclamation point ("!") prefix is required. In an  specification, the !include tag is located only in 
a node value position. The !include tag MUST be the value of a node, 
which assigns the contents of the file named by the !include tag to the value of the node.

In the following example, the set of types to be used in the specification is retrieved from a file called myTypes.raml 
and used as the value of the types node.

```yaml
#%RAML 1.0
title: My API with Types
types: !include myTypes.raml
```

The !include tag accepts a single argument, the location of the content to be included, that MUST be specified explicitly. 
The value of the argument MUST be a path or URL as described in the following table:

|Argument | Description | Examples |
|:--------|:------------|:---------|
| absolute path | A path that begins with a single slash ("/") and is interpreted relative to the root specification file location. 
| /traits/pageable.raml
| relative path | A path that neither begins with a single slash ("/") nor constitutes a URL, and is interpreted relative to the location of the included file. | description.md<br>../traits/pageable.raml
| URL | An absolute URL | http://dev.domain.com/api/patterns/traits.raml

To simplify the specification definition, and because the parsing context of the included file is not shared between the file 
and its parent, an included file SHALL NOT use a YAML reference to an anchor in a separate file. Likewise, a reference made from a parent file SHALL NOT reference an anchor defined in an included file.

The !include tag argument must be static: namely, it MUST NOT contain any resource type parameters or trait parameters.

#### Typed Fragments

A file to be included MAY begin with a fragment identifier line, which consists of the text _#%RAML_ followed left-to-right by a single space, the text 1.0, a single space, and one of the following fragment identifiers:


If a file begins with a specification fragment identifier line, 
the contents of the file after removal of the RAML fragment identifier line 
MUST be valid structurally according to the relevant RAML specification. 
For example, RAML specification file beginning with `#%RAML 1.0 Trait` must have the structure of a RAML trait declaration as defined in the [Resource Types and Traits](#resource-types-and-traits) section. 
Including the file in a correct location for a trait declaration results in a valid RAML file.

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

When specification  files are included, specification parsers MUST NOT only read the content, 
but must also parse it and add the content to the declaring structure as if the content were declared inline.
specification parsers MUST parse the content of the file as specification content and append the parsed structures to the specification document node 
if the included file has a .raml, .yml, or .yaml extension or one of the following media types:

* application/raml+yaml
* text/yaml
* text/x-yaml
* application/yaml
* application/x-yaml

Otherwise, if specification parsers fail to parse the content and append structures, the contents of the file are included as a scalar.

Because the parsing context of the included files is not shared between the included file and its parent, an included file SHALL NOT use a YAML reference to an anchor in a separate file. Likewise, a reference made from a parent file SHALL NOT reference a structure anchor defined in an included file. These rules simplify RAML definitions.

In the RAML 1.0 example below, the API root document includes two files from the patterns folder, one containing resource type declarations and the other containing trait declarations.

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
