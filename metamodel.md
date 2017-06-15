# Metamodel

This documents describe the terms which we use to build RAML dialects. The one way to build RAML dialect is to define and specify set of `Fragments`(Classes/Types/Shapes) which compose the language. 

Each `Fragment` has a set of the `properties` which are allowed to be met in its definition. Some of this properties may be required or pattern properties. Each property has its `domain` (the fragment where it's defined) and `range` - the type describing class and shape of allowed property value. 

We are splitting properties in following conceptual classes: 
 * `object properties`  - properties whose range is a type describing a RAML fragment.
 * `scalar properties`  - properties whose range is a scalar data type.

We use RAML Types to define shape(structure) of the `Fragments`. Some of fragments and properties may have additional semantic validation associated with them. 

`Named` or `public` fragments, some of the fragments defining the language can be used at the root of RAML document, this fragments should have a public name and their corresponding classes should inherit from a `PublicFragment` class.

We are differentiating two forms of the fragment usage - internal fragment and root role (when the fragment is used to define  a root node of the YAML document). 

Your dialect may define a set of `top level` properties, with a domain referencing to `PublicFragment` this properties should be optional and may only be used when `Fragment` is used in the root role. 

## Fragment resolution

## Highlevel parsing process

Node.

###

Nodes.
