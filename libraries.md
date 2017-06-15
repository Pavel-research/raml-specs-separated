### Libraries

Libraries are used to combine any collection of declarations into modular, externalized, reusable groups. While libraries are intended to define common declarations in external documents, which are then included where needed, libraries can also be defined inline.

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
