# Metamodel

This documents describe the terms which we use to build RAML dialects. The one way to build RAML dialect is to define and specify set of `Fragments`(Classes/Types/Shapes) which compose the language. 

Each `Fragment` has a set of the `properties` which are allowed to be met in its definition. Some of this properties may be required or pattern properties. Each property has its `domain` (the fragment where it's defined) and `range` - the type describing class and shape of allowed property value. 

We are splitting properties in following conceptual classes: 
 * `object properties`  - properties whose range is a type describing a RAML fragment.
 * `scalar properties`  - properties whose range is a scalar data type.

We use RAML Types to define shape(structure) of the `Fragments`. Some of fragments and properties may have additional semantic validation associated with them.

## Highlevel parsing process

Node.

###

Nodes.
