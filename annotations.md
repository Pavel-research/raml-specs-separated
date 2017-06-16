# Annotations

Annotations provide a mechanism to extend the specification with metadata beyond the metadata already defined in this RAML 1.0 specification. Annotations can also be used to add properties to the built-in RAML nodes in certain locations within the RAML specification. Processors MAY support certain annotations to add additional specificity to the API description, enable tooling such as testing, support API repositories and API discovery, and so on. Processors MAY ignore any and all annotations.

Annotations used in an API specification MUST be declared in a root-level annotationTypes node. Annotations can have values, which are defined and constrained in annotation type declarations. Processors can then rely on the declarations to ensure annotation values meet expectations.

The following example shows various annotation type declarations and the application of the annotations to an API definition.

```yaml
#%RAML 1.0
title: Illustrating annotations
mediaType: application/json
annotationTypes:
  deprecated: nil
  experimental: nil | string
  feedbackRequested: string?
  testHarness:
    type: string # This line can be omitted as it's the default type
  badge:         # This annotation type allows string values, too
  clearanceLevel:
    properties:
      level:
        enum: [ low, medium, high ]
        required: true
      signature:
        pattern: "\\d{3}-\\w{12}"
        required: true
/groups:
  (experimental):
  (feedbackRequested):
/users:
  (testHarness): usersTest
  (badge): tested.gif
  (clearanceLevel):
    level: high
    signature: 230-ghtwvfrs1itr
  get:
    (deprecated):
    (experimental):
    (feedbackRequested): Feedback committed!
    responses:
      200:
```

Annotations applied to a data type are not inherited when that data type is inherited. However, processors SHOULD make the information about the annotations in the data type hierarchy available. Annotations applied to, or within, a resource type or trait are also applied to the resource type, resource, or method that inherits the resource type or trait. In particular, if a trait is applied to a resource type or resource, all annotations on or within that trait are applied implicitly to all methods of that resource. If the inheriting resource type, resource, or method explicitly applies an annotation of a given type, then this annotation overrides all applications of that annotation type which would otherwise have been inherited and implicitly applied. In particular, if a trait is applied to a resource type or resource, and the resource type or resource applies an annotation of some type, then any and all applications of annotations of that type to that trait are overridden.

### Declaring Annotation Types

Annotation types are declared using the OPTIONAL root-level **annotationTypes** node. The value of the annotationsType node is a map whose keys define annotation type names, also referred to as annotations, and whose values are key-value pairs called annotation type declarations. An annotation type declaration has the same syntax as a data type declaration, and its facets have the same syntax as the corresponding ones for data types, but with the addition of the allowedTargets facet. An annotation type declaration constrains the value of an annotation of that type just as a data type declaration constrains the value of a URI parameter, query parameter, header, or body of that type. The allowedTargets node restricts the kinds of locations where the annotation can be applied. Annotation types, like data types, can extend other data types, but annotation types themselves can neither be extended nor used anywhere data types can be used.

|Name |Description |
|:--------|:------------|
| displayName? | A friendly name used only for display or documentation purposes. The default is the element key, the name of the annotation itself.
| description? | The intended use or meaning of an annotation. A string that MAY be formatted using [markdown](#markdown).
| (&lt;annotationName&gt;)? | [Annotations](#annotations) to be applied to this API. An annotation is a map having a key that begins with "(" and ends with ")" where the text enclosed in parentheses is the annotation name, and the value is an instance of that annotation.
| allowedTargets? | The locations to which annotations are restricted. If this node is specified, annotations of this type may be applied only on a node corresponding to one of the locations. The value MUST be one or more of the options described in the [Target Locations](#annotation-target-location).

If an annotation type declaration specifies neither a type facet nor a properties facet, the default annotationName type is string.

All annotations used in an API specification MUST be declared in its annotationTypes node. Any value of an annotation MUST be valid according to its annotation type.

If the allowedTargets node is not present, the annotation can be applied in any of the target locations listed in the Target Locations table. If the allowedTargets node is present, it restricts where the annotation can be applied, as described in [Annotation Targets](#annotation-targets).


### Applying Annotations

To be applied in an API specification, the annotation MUST be declared in an annotation type.

A declared annotation can be applied to a node in the specification by adding an annotation node on that whose key is the name of the annotation type enclosed in parentheses. The annotation value MUST be valid according to the corresponding annotation type.

The example below, a small subset of the previous example, shows an explicit declaration and use of a testHarness annotation that should be a string value.

```yaml
#%RAML 1.0
title: Testing annotations
mediaType: application/json
annotationTypes:
  testHarness:
    type: string
/users:
  (testHarness): usersTest
```

The following example is semantically equivalent to the previous one, but relies on the implicit, default declaration of the value type when there is no explicit type declaration.

```yaml
#%RAML 1.0
title: Testing annotations
mediaType: application/json
annotationTypes:
  testHarness:
/users:
  (testHarness): usersTest
```

#### Annotating Scalar-valued Nodes

It is often useful to annotate scalar-valued nodes, for example `baseUri`. Annotations are typically applied as extra key-value pairs to map-valued nodes that inherently accept key-value pairs. Annotations cannot be easily applied to scalar-valued nodes. To apply annotations to any scalar-valued node, a RAML processor MUST also support scalar-valued nodes expressed as a map that allow a single key `value` as an alternative to the normal syntax.

The following example shows a scalar-valued node which is normally expressed as:

```yaml
baseUri: http://www.example.com/api
```

The alternative map syntax with `value` as the key is added to the example:

```yaml
baseUri:
  value: http://www.example.com/api
```

Now, annotations can be applied normally, as shown in this example:

```yaml
baseUri:
  value: http://www.example.com/api
  (redirectable): true
```

#### Annotation Targets

The location within an API specification where annotations can be applied MUST be one of the target locations in the following Target Locations table. The targets are the locations themselves, not sub-properties within the locations; for example, the Method target refers to the method node, not to the method display name, description, and so on.

<a name='annotation-target-location'></a>

**Target Locations**

Each RAML dialect language may have a set of allowed annotation target names associated with them, annotation target 
should point to the fragment name or alternatively to the property declaration.

The following example illustrates applying some restrictions on the allowed targets of annotations.

```yaml
#%RAML 1.0
title: Illustrating allowed targets
mediaType: application/json
annotationTypes:
  meta-resource-method:
    allowedTargets: [ Resource, Method ]
  meta-data:
    allowedTargets: TypeDeclaration
types:
  User:
    type: object
    (meta-data): on an object; on a data type declaration
    properties:
      name:
        type: string
        (meta-data): on a string property
/users:
  (meta-resource-method): on a resource
  get:
    (meta-resource-method): on a method
    responses:
      200:
        body:
          type: User[]
          (meta-data): on a body
```


