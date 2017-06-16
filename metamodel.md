# Metamodel

This documents describe the terms which we use to build RAML dialects. The one way to build RAML dialect is to define and specify set of `Fragments`(Classes/Types/Shapes) which compose the language. 

Each `Fragment` has a set of the `properties` which are allowed to be met in its definition. Some of this properties may be required or pattern properties. Each property has its `domain` (the fragment where it's defined) and `range` - the type describing class and shape of allowed property value. 

We are splitting properties in following conceptual classes: 
 * `object properties`  - properties whose range is a type describing a `Fragment`.
 * `scalar properties`  - properties whose range is a scalar data type.
 
values can have annotations associated with it. Annotations are not considered to be part of the value shape and are stored separately. **Describe what annotations are**
 
We use RAML Types to define shape(structure) of the `Fragments`. Some of fragments and properties may have additional semantic validation associated with them. 

`Named` or `public` fragments, some of the fragments defining the language can be used at the root of RAML document, this fragments should have a public name and their corresponding classes should inherit from a `PublicFragment` class.

We are differentiating two forms of the fragment usage - internal fragment and root role (when the fragment is used to define  a root node of the YAML document). 

Your dialect may define a set of `top level` properties, with a domain referencing to `PublicFragment` this properties should be optional and may only be used when `Fragment` is used in the root role. 

## Fragment resolution
When fragment is used in the root role. Content of the file defining a fragment instance may start from fragment identifier line, which consists of the text `#%(DIALECT SHORT NAME)` followed left-to-right by a single space, the version of the dialect, a single space, and one of the names of the `public fragments` defined in the language.

If file begins with a fragment identifier line, the contents of the file after removal of the fragment identifier line MUST be valid structurally according to the relevant fragment definition. 

For example, a RAML file beginning with `#%RAML 1.0 Trait` must have the structure of a RAML trait declaration as defined in the Resource Types and Traits section.

**Do we need global namespaces and language resolution mechanism, I beleive yes**

## Highlevel parsing process

We split parsing of the document in the following stages:
 * `fragment resolution` - at this moment parser should decide what kind of the fragment is being parsed.
 * `structure resolution`  - on this stage initial object structure is built.
 * `template resolution` - on this stage parser should resolve and apply all template fragments relevant to the parsed document
 * `meta compression` - at this moment scalars encoded as objects should be converted to plain scalars.
 * `extension` - if parsed fragment is a sub type of `ExtensionFragment` on this stage parser should transform object structure one more time, by merging it on top of extended document object structure.
 * `shape validation` - finally parser should perform structural and semantic validation of resulting object structure according to the rules of the fragment being parsed
 * `annotation validation` - at this moment all annotations associated with all nodes and scalar property values are validated for their structural and semantical correctness


