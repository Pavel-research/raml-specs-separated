# RAML Types Version 1.0: 

## Abstract

RAML Types is a concise standard to describe data structures. It is a replacement for, and interoperates with, JSON Schema and XSD.

It was initially developed in the context of RAML 1.0 by MuleSoft, but given its general applicability it was extracted into its own separate specification.


## Introduction 

### Summary

 * Alternative to JSON Schema and XML Schema
 * "Feels like" object oriented programming
 * Allows you to test the validity of XML, YAML and JSON documents
 * Concise syntax
 * Can be serialized as JSON and YAML
 * Can be inlined in JSON and YAML documents
 * Designed with convenience in mind


SDS lets you define Types ( aka Interfaces, Structs, Classes ) that describe the structure ( properties ) of your data. SDS-aware tooling can then use this information to verify that your data is structurally valid.

## Status of this Document

This document constitutes the RAML 1.0 specification. The consensus of specification authors and RAML 0.8 users determines the contents of this document. We strongly recommend that implementers and users of the RAML 0.8 specification update their software and API definitions to the RAML 1.0 specification.

## Terminology and Conformance Language

Normative text describes one or both of the following kinds of elements:

* Vital elements of the specification
* Elements that contain the conformance language key words as defined by [IETF RFC 2119](https://www.ietf.org/rfc/rfc2119.txt) "Key words for use in RFCs to Indicate Requirement Levels"

Informative text is potentially helpful to the user, but dispensable. Informative text can be changed, added, or deleted editorially without negatively affecting the implementation of the specification. Informative text does not contain conformance keywords.

All text in this document is, by default, normative.

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [IETF RFC 2119](https://www.ietf.org/rfc/rfc2119.txt) "Key words for use in RFCs to Indicate Requirement Levels".

## Definitions and Terminology

### General

In this specification, **API definition** means an API using this specification.

**RAML Specification** refers to this document.

**REST** is used in the context of an API implemented using some or all of the principles of REST (Representational State Transfer), which was introduced and first defined in 2000 in Chapter 5, [REST](http://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm), of the doctoral dissertation *"Architectural Styles and the Design of Network-based Software Architecture"* by Roy Fielding.

A **resource** is the conceptual mapping to an entity or set of entities.

A trailing question mark, for example **description?**, indicates an optional property.

### Markdown

Throughout this specification, **Markdown** means [GitHub-Flavored Markdown](https://help.github.com/articles/github-flavored-markdown/).

## Table of Content

<!-- TOC -->

- [Introduction](#introduction)
- [Markup Language](#markup-language)
- [RAML Data Types](#raml-data-types)
	- [Introduction](#introduction)
	- [Overview](#overview)
	- [Defining Types](#defining-types)
	- [Type Declarations](#type-declarations)
	- [Built-in Types](#built-in-types)
		- [The "Any" Type](#the-any-type)
		- [Object Type](#object-type)
			- [Property Declarations](#property-declarations)
			- [Additional Properties](#additional-properties)
			- [Object Type Specialization](#object-type-specialization)
			- [Using Discriminator](#using-discriminator)
		- [Array Type](#array-type)
		- [Scalar Types](#scalar-types)
			- [String](#string)
			- [Number](#number)
			- [Integer](#integer)
			- [Boolean](#boolean)
			- [Date](#date)
			- [File](#file)
			- [Nil Type](#nil-type)
		- [Union Type](#union-type)
		- [Using XML and JSON Schema](#using-xml-and-json-schema)
	- [User-defined Facets](#user-defined-facets)
	- [Determine Default Types](#determine-default-types)
	- [Type Expressions](#type-expressions)
	- [Multiple Inheritance](#multiple-inheritance)
	- [Inline Type Declarations](#inline-type-declarations)
	- [Defining Examples in RAML](#defining-examples-in-raml)
		- [Multiple Examples](#multiple-examples)
		- [Single Example](#single-example)
		- [Example of how to define example/examples in RAML](#example-of-how-to-define-exampleexamples-in-raml)
	- [XML Serialization of Type Instances](#xml-serialization-of-type-instances)
	- [Using Types in RAML](#using-types-in-raml)
- [Annotations](#annotations)
	- [Declaring Annotation Types](#declaring-annotation-types)
	- [Applying Annotations](#applying-annotations)
		- [Annotating Scalar-valued Nodes](#annotating-scalar-valued-nodes)
		- [Annotation Targets](#annotation-targets)
- [Modularization](#modularization)
	- [Includes](#includes)
		- [Typed Fragments](#typed-fragments)
		- [Resolving Includes](#resolving-includes)
	- [Libraries](#libraries)

- [References](#references)

<!-- /TOC -->

## Introduction

This specification describes the Raml Datatypes Language (RDL). RAML is a human- and machine-readable language for the definition of a RESTful application programming interface (API). RAML is designed to improve the specification of the API by providing a format that the API provider and API consumers can use as a mutual contract. RAML can, for example, facilitate providing user documentation and source code stubs for client and server implementations. Such provisions streamline and enhance the definition and development of interoperable applications that utilize RESTful APIs.

RAML introduces the innovative concept of resource types and traits for characterizing and reusing patterns of resources and associated methods. Using resource types and traits minimizes the repetition in a RESTful API design and promotes consistency within and across APIs.

## Markup Language

This specification uses [YAML 1.2](http://www.yaml.org/spec/1.2/spec.html) as its underlying format. YAML is a human-readable data format that aligns well with the design goals of this specification. As in YAML, all nodes such as keys, values, and tags, are case-sensitive.

RAML API definitions are YAML 1.2-compliant documents that begin with a REQUIRED YAML-comment line that indicates the RAML version, as follows:

```yaml
#%RAML 1.0
title: My API
```

The first line of a RAML API definition document MUST begin with the text _#%RAML_ followed by a single space followed by the text _1.0_ and nothing else before the end of the line. RAML fragment documents begin similarly with the RAML version comment and a [fragment identifier](#typed-fragments), but are not in themselves RAML API definition documents.

The media type _application/raml+yaml_ and its associated file extension _.raml_ SHALL be used to designate files containing RAML API definitions, RAML fragments, and files that contain RAML markup. RAML is also capable of including documents of other media types, such as “application/schema+json” and “application/yaml”.

To facilitate the automated processing of RAML documents, RAML imposes the following restrictions and requirements in addition to the core YAML 1.2 specification:

* The first line of a RAML file consists of a YAML comment that specifies the RAML version. Therefore, RAML processors cannot completely ignore all YAML comments.
* The order of some properties at certain levels within a RAML document is significant. Therefore, processors are expected to preserve this ordering.

## RAML Data Types

### Introduction

RAML 1.0 introduces the notion of **data types**, which provide a concise and powerful way of describing the data in an API. Data types add rules for validating data against a type declaration. Valid data adheres to all rules for the type. Data types can describe a base or resource URI parameter, a query parameter, a request or response header, or a request or response body. Data types are built-in or custom. A built-in type can be used anywhere the API expects data. Custom types can be defined by extending the built-in types as well as named and used like built-in type. Extending types MUST NOT create any cyclic dependencies. A type can be extended inline.

The following RAML example defines a User type that includes type declarations for the firstname, lastname, and age properties. The example declares the properties to be of built-in types string and number. Later, the User type serves to describe the type (schema) for a payload.

```yaml
#%RAML 1.0
title: API with Types
types:
  User:
    type: object
    properties:
      firstname: string
      lastname:  string
      age:       number
/users/{id}:
  get:
    responses:
      200:
        body:
          application/json:
            type: User
```

A RAML type declaration resembles a JSON schema definition. In fact, RAML types can be used instead of JSON and XML schemas, or coexist with them. The RAML type syntax, however, is designed to be considerably easier and more succinct than JSON and XML schemas while retaining their flexibility and expressiveness. The following snippet shows a number of examples of type declarations:

```yaml
#%RAML 1.0
title: My API with Types
mediaType: application/json
types:
  Org:
    type: object
    properties:
      onCall: AlertableAdmin
      Head: Manager
  Person:
    type: object
    properties:
      firstname: string
      lastname:  string
      title?:    string
  Phone:
    type: string
    pattern: "[0-9|-]+"
  Manager:
    type: Person
    properties:
      reports: Person[]
      phone:  Phone
  Admin:
    type: Person
    properties:
      clearanceLevel:
        enum: [ low, high ]
  AlertableAdmin:
    type: Admin
    properties:
      phone: Phone
  Alertable: Manager | AlertableAdmin
/orgs/{orgId}:
  get:
    responses:
      200:
        body:
          application/json:
            type: Org
```

### Overview

This section is informative.

The RAML type system borrows from object oriented programming languages such as Java, as well as from XSD and JSON Schemas.

RAML Types in a nutshell:

- Types are similar to Java classes.
  - Types borrow additional features from JSON Schema, XSD, and more expressive object oriented languages.
- You can define types that inherit from other types.
  - Multiple inheritance is allowed.
- Types are split into four families: external, object, array, and scalar.
- Types can define two types of members: **properties** and **facets**. Both are inherited.
  - **Properties** are regular, object oriented properties.
  - **Facets** are special _configurations_. You specialize types based on characteristics of facet values.
    Examples: minLength, maxLength
- Only object types can declare properties. All types can declare facets.
- To specialize a scalar type, you implement facets, giving already defined facets a concrete value.
- To specialize an object type, you define properties.

### Defining Types

Types can be declared inline where the API expects data, in an OPTIONAL **types** node at the root of the API, or in an included library. To declare a type, you use a map where the key represents the name of the type and its value is a [type declaration](#type-declarations).

```yaml
types:
  Person: # key name
    # value is a type declaration
```

### Type Declarations

A type declaration references another type, or wraps or extends another type by adding functional facets (e.g. properties) or non-functional facets (e.g. a description), or is a type expression that uses other types. Here are the facets that all type declarations can have; certain type declarations might have other facets:

| Facet  | Description |
|:----------|:----------|
| default? | A default value for a type. When an API request is completely missing the instance of a type, for example when a query parameter described by a type is entirely missing from the request, then the API must act as if the API client had sent an instance of that type with the instance value being the value in the default facet. Similarly, when the API response is completely missing the instance of a type, the client must act as if the API server had returned an instance of that type with the instance value being the value in the default facet. A special case is made for URI parameters: for these, the client MUST substitute the value in the default facet if no instance of the URI parameter was given.
| schema? | An alias for the equivalent "type" facet for compatibility with RAML 0.8. Deprecated - API definitions should use the "type" facet because the "schema" alias for that facet name might be removed in a future RAML version. The "type" facet supports XML and JSON schemas.
| type? | The type which the current type extends or just wraps. The value of a type node MUST be either a) the name of a user-defined type or b) the name of a built-in RAML data type (object, array, or one of the scalar types) or c) an inline type declaration.
| example? | An example of an instance of this type that can be used, for example, by documentation generators to generate sample values for an object of this type. The "example" facet MUST NOT be available when the "examples" facet is already defined. See section [Examples](#defining-examples-in-raml) for more information.
| examples? |  Examples of instances of this type. This can be used, for example, by documentation generators to generate sample values for an object of this type. The "examples" facet MUST NOT be available when the "example" facet is already defined. See section [Examples](#defining-examples-in-raml) for more information.
| displayName? | An alternate, human-friendly name for the type
| description? | A substantial, human-friendly description of the type. Its value is a string and MAY be formatted using [markdown](#markdown).
| (&lt;annotationName&gt;)? | [Annotations](#annotations) to be applied to this API. An annotation is a map having a key that begins with "(" and ends with ")" where the text enclosed in parentheses is the annotation name, and the value is an instance of that annotation.
| facets? | A map of additional, user-defined restrictions that will be inherited and applied by any extending subtype. See section [User-defined Facets](#user-defined-facets) for more information.
| xml? | The capability to configure [XML serialization of this type instance](#xml-serialization-of-type-instances).
| enum? | An enumeration of all the possible values of instances of this type. The value is an array containing representations of these possible values; an instance of this type MUST be equal to one of these values.

The "schema" and "type" facets are mutually exclusive and synonymous: processors MUST NOT allow both to be specified, explicitly or implicitly, inside the same type declaration. Therefore, the following examples are invalid:

```yaml
types:
  Person:
    schema: # invalid as mutually exclusive with `type`
    type: # invalid as mutually exclusive with `schema`
```

```yaml
/resource:
  get:
    responses:
      200:
        body:
          application/json: # start type declaration
            schema: # invalid as mutually exclusive with `type`
            type: # invalid as mutually exclusive with `schema`
```

We recommended using the "type" facet instead of "schema" because the schema alias is deprecated and might be removed in a future RAML version. Also, the "type" facet supports XML and JSON schemas.

### Built-in Types

The RAML type system defines the following built-in types:

* [any](#the-any-type)
* [object](#object-type)
* [array](#array-type)
* [union](#union-type) via type expression
* one of the following [scalar types](#scalar-types): number, boolean, string, date-only, time-only, datetime-only, datetime, file, integer, or nil

Additional to the built-in types, the RAML type system also allows to define [JSON or XML schema](#using-xml-and-json-schema).

The following diagram shows the inheritance tree, starting at the root-level with `any`.

![Types Hierarchy](images/typesHierarchy.png)

#### The "Any" Type

Every type, whether built-in or user-defined, has the `any` type at the root of its inheritance tree. By definition, the `any` type is a type which imposes no restrictions, i.e. any instance of data is valid against it.

The "base" type of any type is the type in its inheritance tree that directly extends the `any` type at the root; thus, for example, if a custom type `status` extends the built-in type `integer` which extends the built-in type `number` which extends the `any` type, then the base type of `status` is `number`. Note that a type may have more than one base type.

The `any` type has no facets.

#### Object Type

All types that have the built-in object base type in its inheritance tree can use the following facets in their type declarations:

| Facet  | Description |
|:----------|:----------|
| properties? | The [properties](#property-declarations) that instances of this type can or must have.
| minProperties? | The minimum number of properties allowed for instances of this type.
| maxProperties? | The maximum number of properties allowed for instances of this type.
| additionalProperties? | A Boolean that indicates if an object instance has [additional properties](#additional-properties).<br/><br/>**Default:** `true`
| discriminator? | Determines the concrete type of an individual object at runtime when, for example, payloads contain ambiguous types due to unions or inheritance. The value must match the name of one of the declared `properties` of a type. Unsupported practices are inline type declarations and [using `discriminator`](#using-discriminator) with non-scalar properties.
| discriminatorValue? | Identifies the declaring type. Requires including a `discriminator` facet in the type declaration. A valid value is an actual value that might identify the type of an individual object and is unique in the hierarchy of the type. Inline type declarations are not supported.<br/><br/>**Default:** The name of the type

An object type is created by explicit inheritance from the built-in type object:

```yaml
#%RAML 1.0
title: My API With Types
types:
  Person:
    type: object
    properties:
      name:
        required: true
        type: string
```

##### Property Declarations

Properties of object types are defined using the OPTIONAL **properties** facet. The RAML Specification calls the value of the "properties" facet a "properties declaration". The properties declaration is a map of keys and values. The keys are valid property names for declaring a type instance. The values are either a name of a type or an inline type declaration.

The properties declaration can specify whether a property is required or optional.

| Facet  | Description |
|:----------|:----------|
| required? | Specifies that the property is required or not.<br /><br />**Default:** `true`.

The following example declares an object type having two properties:

```yaml
types:
  Person:
    properties:
      name:
        required: true
        type: string
      age:
        required: false
        type: number
```

The following example shows a common idiom:

```yaml
types:
  Person:
    properties:
      name: string # equivalent to ->
                   # name:
                   #  type: string
      age?: number # optional property; equivalent to ->
                   # age:
                   #  type: number
                   #  required: false
```

When the `required` facet on a property is specified explicitly in a type declaration, any question mark in its property name is treated as part of the property name rather than as an indicator that the property is optional.

For example, in

```yaml
types:
  profile:
    properties:
      preference?:
        required: true
```

The `profile` type has a property named `preference?` that includes the trailing question mark. The following snippets show two ways of making `preference?` optional:

```yaml
types:
  profile:
    properties:
      preference?:
        required: false
```

or

```yaml
types:
  profile:
    properties:
      preference??:
```

Note:

When an object type does not contain the "properties" facet, the object is assumed to be unconstrained and therefore capable of containing any properties of any type.

##### Additional Properties

By default, any instance of an object can have additional properties beyond those specified in its data type `properties` facet. Assume the following code is an instance of the data type `Person` that is described in the previous section.

```yaml
Person:
  name: "John"
  age: 35
  note: "US" # valid additional property `note`
```

The property `note` is not explicitly declared in the `Person` data type, but is valid because all additional properties are valid by default.

To restrict the addition of properties, you can set the value of the `additionalProperties` facet to `false`, or you can specify regular expression patterns that match sets of keys and restrict their values. The latter are called "pattern properties". The patterns are delineated by pairs of opening and closing `/` characters, as follows:

```yaml
#%RAML 1.0
title: My API With Types
types:
  Person:
    properties:
      name:
        required: true
        type: string
      age:
        required: false
        type: number
      /^note\d+$/: # restrict any properties whose keys start with "note"
                   # followed by a string of one or more digits
        type: string
```

This pattern property restricts any additional properties whose keys start with "note" followed by a string of one or more digits. Consequently, the example of an object instance that declares an additional `note` property with the value "US" is valid, but the same property is invalid with a non-string value:

```yaml
Person:
  name: "John"
  age: 35
  note: 123 # not valid as it is not a string
  address: "US" # valid as it does not match the pattern
```

To force all additional properties to be strings, regardless of their keys, use:

```yaml
#%RAML 1.0
title: My API With Types
types:
  Person:
    properties:
      name:
        required: true
        type: string
      age:
        required: false
        type: number
      //: # force all additional properties to be a string
        type: string
```

If a pattern property regular expression also matches an explicitly declared property, the explicitly declared property definition prevails. If two or more pattern property regular expressions match a property name in an instance of the data type, the first one prevails.

Moreover, if `additionalProperties` is `false` (explicitly or by inheritance) in a given type definition, then explicitly setting pattern properties in that definition is not allowed. If `additionalProperties` is `true` (or omitted) in a given type definition, then pattern properties are allowed and further restrict the additional properties allowed in that type.

##### Object Type Specialization

You can declare object types that inherit from other object types. A sub-type inherits all the properties of its parent type. In the following example, the type `Employee` inherits all properties of its parent type `Person`.

```yaml
#%RAML 1.0
title: My API With Types
types:
  Person:
    type: object
    properties:
      name:
        type: string
  Employee:
    type: Person
    properties:
      id:
        type: string
```

A sub-type can override properties of its parent type with the following restrictions: 1) a required property in the parent type cannot be changed to optional in the sub-type, and 2) the type declaration of a defined property in the parent type can only be changed to a narrower type (a specialization of the parent type) in the sub-type.

##### Using Discriminator

When payloads contain ambiguous types due to unions or inheritance, it is often impossible to discriminate the concrete type of an individual object at runtime. For example, when deserializing the payload into a statically typed language, this problem can occur.

A RAML processor might provide an implementation that automatically selects a concrete type from a set of possible types, but a simpler alternative is to store some unique value associated with the type inside the object.

You set the name of an object property using the `discriminator` facet. The name of the object property is used to discriminate the concrete type. The `discriminatorValue` stores the actual value that might identify the type of an individual object. By default, the value of `discriminatorValue` is the name of the type.   

Here's an example that illustrates how to use `discriminator`:

```yaml
#%RAML 1.0
title: My API With Types
types:
  Person:
    type: object
    discriminator: kind # refers to the `kind` property of object `Person`
    properties:
      kind: string # contains name of the kind of a `Person` instance
      name: string
  Employee: # kind can equal `Employee`; default value for `discriminatorValue`
    type: Person
    properties:
      employeeId: integer
  User: # kind can equal `User`; default value for `discriminatorValue`
    type: Person
    properties:
      userId: integer
```

```yaml
data:
  - name: A User
    userId: 111
    kind: User
  - name: An Employee
    employeeId: 222
    kind: Employee
```

You can also override the default for `discriminatorValue` for each individual concrete class. The following example replaces the default value of `discriminatorValue` in initial caps with lowercase:

```yaml
#%RAML 1.0
title: My API With Types
types:
  Person:
    type: object
    discriminator: kind
    properties:
      name: string
      kind: string
  Employee:
    type: Person
    discriminatorValue: employee # override default
    properties:
      employeeId: string
  User:
    type: Person
    discriminatorValue: user # override default
    properties:
      userId: string
```

```yaml
data:
  - name: A User
    userId: 111
    kind: user
  - name: An Employee
    employeeId: 222
    kind: employee
```

Neither `discriminator` nor `discriminatorValue` can be defined for any inline type declarations or union types.

```yaml
# valid whenever there is a key name that can identify a type
types:
  Device:
    discriminator: kind
    properties:
      kind: string
```

```yaml
# invalid in any inline type declaration
application/json:
   discriminator: kind
   properties:
     kind: string
```

```yaml
# invalid for union types
PersonOrDog:
   type: Person | Dog
   discriminator: hasTail
```

#### Array Type

Array types are declared by using either the array qualifier `[]` at the end of a [type expression](#type-expressions) or `array` as the value of a `type` facet. If you are defining a top-level array type, such as the `Emails` in the examples below, you can declare the following facets in addition to those previously described to further restrict the behavior of the array type.

| Facet  | Description |
|:----------|:----------|
| uniqueItems? | Boolean value that indicates if items in the array MUST be unique.
| items? | Indicates the type all items in the array are inherited from. Can be a reference to an existing type or an inline [type declaration](#type-declaration).
| minItems? | Minimum amount of items in array. Value MUST be equal to or greater than 0.<br /><br />**Default:** `0`.
| maxItems? | Maximum amount of items in array. Value MUST be equal to or greater than 0.<br /><br />**Default:** `2147483647`.

Both of the following examples are valid:

```yaml
types:
  Email:
    type: object
    properties:
      subject: string
      body: string
  Emails:
    type: Email[]
    minItems: 1
    uniqueItems: true
    example: # example that contains array
      - # start item 1
        subject: My Email 1
        body: This is the text for email 1.
      - # start item 2
        subject: My Email 2
        body: This is the text for email 2.  
```

```yaml
types:
  Email:
    type: object
    properties:
      name:
        type: string
  Emails:
    type: array
    items: Email
    minItems: 1
    uniqueItems: true
```

Using `Email[]` is equivalent to using `type: array`.  The `items` facet defines the `Email` type as the one each array item inherits from.

#### Scalar Types

RAML defines a set of built-in scalar types, each of which has a predefined set of restrictions.

##### String

A JSON string with the following additional facets:

| Facet | Description |
|:--------|:------------|
| pattern? | Regular expression that this string should match.
| minLength? | Minimum length of the string. Value MUST be equal to or greater than 0.<br /><br />**Default:** `0`
| maxLength? | Maximum length of the string. Value MUST be equal to or greater than 0.<br /><br />**Default:** `2147483647`

Example:

```yaml
types:
  Email:
    type: string
    minLength: 2
    maxLength: 6
    pattern: ^note\d+$
```

##### Number

Any JSON number including [integer](#integer) with the following additional facets:

| Facet | Description |
|:--------|:------------|
|minimum? | The minimum value of the parameter. Applicable only to parameters of type number or integer.
|maximum? | The maximum value of the parameter. Applicable only to parameters of type number or integer.
|format? | The format of the value. The value MUST be one of the following: int32, int64, int, long, float, double, int16, int8
|multipleOf? | A numeric instance is valid against "multipleOf" if the result of dividing the instance by this keyword's value is an integer.

Example:

```yaml
types:
  Weight:
    type: number
    minimum: 3
    maximum: 5
    format: int64
    multipleOf: 4
```

##### Integer

A subset of JSON numbers that are positive and negative multiples of 1. The integer type inherits its facets from the [number type](#number).

```yaml
types:
  Age:
    type: integer
    minimum: 3
    maximum: 5
    format: int8
    multipleOf: 1
```

##### Boolean

A JSON boolean without any additional facets.

```yaml
types:
  IsMarried:
    type: boolean
```

##### Date

The following date type representations MUST be supported:

| Type | Description |
|:-----|:------------|
| date-only | The "full-date" notation of [RFC3339](http://xml2rfc.ietf.org/public/rfc/html/rfc3339.html#anchor14), namely `yyyy-mm-dd`. Does not support time or time zone-offset notation.
| time-only | The "partial-time" notation of [RFC3339](http://xml2rfc.ietf.org/public/rfc/html/rfc3339.html#anchor14), namely hh:mm:ss\[.ff...\]. Does not support date or time zone-offset notation.
| datetime-only | Combined date-only and time-only with a separator of "T", namely yyyy-mm-ddThh:mm:ss\[.ff...\]. Does not support a time zone offset.
| datetime | A timestamp in one of the following formats:  if the _format_ is omitted or set to `rfc3339`, uses the "date-time" notation of [RFC3339](http://xml2rfc.ietf.org/public/rfc/html/rfc3339.html#anchor14); if _format_ is set to `rfc2616`, uses the format defined in [RFC2616](https://www.ietf.org/rfc/rfc2616.txt).

The additional facet _format_ MUST be available only when the type equals _datetime_, and the value MUST be either `rfc3339` or `rfc2616`. Any other values are invalid.

```yaml
types:
  birthday:
    type: date-only # no implications about time or offset
    example: 2015-05-23
  lunchtime:
    type: time-only # no implications about date or offset
    example: 12:30:00
  fireworks:
    type: datetime-only # no implications about offset
    example: 2015-07-04T21:00:00
  created:
    type: datetime
    example: 2016-02-28T16:41:41.090Z
    format: rfc3339 # the default, so no need to specify
  If-Modified-Since:
    type: datetime
    example: Sun, 28 Feb 2016 16:41:41 GMT
    format: rfc2616 # this time it's required, otherwise, the example format is invalid
```

##### File

The ​**file**​ type can constrain the content to send through forms. When this type is used in the context of web forms it SHOULD be represented as a valid file upload in JSON format. File content SHOULD be a base64-encoded string.

| Facet | Description |
|:--------|:------------|
| fileTypes? | A list of valid content-type strings for the file. The file type `*/*` MUST be a valid value.
| minLength? | Specifies the minimum number of bytes for a parameter value. The value MUST be equal to or greater than 0.<br /><br />**Default:** `0`
| maxLength? | Specifies the maximum number of bytes for a parameter value. The value MUST be equal to or greater than 0.<br /><br />**Default:** `2147483647`

```yaml
types:
  userPicture:
    type: file
    fileTypes: ['image/jpeg', 'image/png']
    maxLength: 307200
  customFile:
    type: file
    fileTypes: ['*/*'] # any file type allowed
    maxLength: 1048576
```

##### Nil Type
​
In RAML, the type `nil` is a scalar type that allows only nil data values. Specifically, in YAML it allows only YAML's `null` (or its equivalent representations, such as `~`), in JSON it allows only JSON's `null`, and in XML it allows only XML's `xsi:nil`. In headers, URI parameters, and query parameters, the `nil` type only allows the string value "nil" (case-sensitive); and in turn an instance having the string value "nil" (case-sensitive), when described with the `nil` type, deserializes to a nil value.

In the following example, the type of an object and has two required properties, `name` and `comment`, both defaulting to type `string`. In `example`, `name` is assigned a string value, but comment is nil and this is _not_ allowed because RAML expects a string.

```yaml
types:
  NilValue:
    type: object
    properties:
      name:
      comment:
    example:
      name: Fred
      comment: # Providing no value here is not allowed.
```

The following example shows the assignment of the `nil` type to `comment`:

​
```yaml
types:
  NilValue:
    type: object
    properties:
      name:
      comment: nil
    example:
      name: Fred
      comment: # Providing a value here is not allowed.
```

The following example shows how to represent nilable properties using a union:
​
```yaml
types:
  NilValue:
    type: object
    properties:
      name:
      comment: nil | string # equivalent to ->
                             # comment: string?
    example:
      name: Fred
      comment: # Providing a value or not providing a value here is allowed.
```

Declaring the type of a property to be `nil` represents the lack of a value in a type instance. In a RAML context that requires *values* of type `nil` (vs just type declarations), the usual YAML `null` is used, e.g. when the type is `nil | number` you may use `enum: [ 1, 2, ~ ]` or more explicitly/verbosely `enum: [ 1, 2, !!null "" ]`; in non-inline notation you can just omit the value completely, of course.

#### Union Type

A union type is used to allow instances of data to be described by any of several types. A union type is declared via a type expression that combines 2 or more types delimited by pipe (`|`) symbols; these combined types are referred to as the union type's super types. In the following example, instances of the `Device` type may be described by either the `Phone` type or the `Notebook` type:

```yaml
#%RAML 1.0
title: My API With Types
types:
  Phone:
    type: object
    properties:
      manufacturer:
        type: string
      numberOfSIMCards:
        type: number
      kind: string
  Notebook:
    type: object
    properties:
      manufacturer:
        type: string
      numberOfUSBPorts:
        type: number
      kind: string
  Device:
    type: Phone | Notebook
```

An instance of a union type is valid if and only if it meets all restrictions associated with at least one of the super types. More generally, an instance of a type that has a union type in its type hierarchy is valid if and only if it is a valid instance of at least one of the super types obtained by expanding all unions in that type hierarchy. Such an instance is deserialized by performing this expansion and then matching the instance against all the super types, starting from the left-most and proceeding to the right; the first successfully-matching base type is used to deserialize the instance.

The following example defines two types and a third type which is a union of those two types.

```yaml
types:
  CatOrDog:
    type: Cat | Dog # elements: Cat or Dog
  Cat:
    type: object
    properties:
      name: string
      color: string
  Dog:
    type: object
    properties:
      name: string
      fangs: string
```

The following example of an instance of type `CatOrDog` is valid:

```yaml
CatOrDog: # follows restrictions applied to the type 'Cat'
  name: Musia,
  color: brown
```

<a name="union-multiple-inheritance"></a>
Imagine a more complex example of a union type used in a multiple inheritance type expression:

```yaml
types:
   HasHome:
     type: object
     properties:
       homeAddress: string
   Cat:
     type: object
     properties:
       name: string
       color: string
   Dog:
     type: object
     properties:
       name: string
       fangs: string       
   HomeAnimal: [ HasHome ,  Dog | Cat ]
```

In this case, type `HomeAnimal` has two super types, `HasHome` and an anonymous union type, defined by the following type expression: `Dog | Cat`.

Validating the `HomeAnimal` type involves validating the types derived from each of the super types and the types of each element in the union type. In this particular case, the processor MUST test that types `[HasHome, Dog]` and `[HasHome, Cat]` are valid types.

If you are extending from two union types a processor MUST perform validations for every possible combination. For example, to validate the `HomeAnimal` type shown below, the processor MUST test the six possible combinations: `[HasHome, Dog ]`, `[HasHome, Cat ]`, `[HasHome, Parrot]`, `[IsOnFarm, Dog ]`, `[IsOnFarm, Cat ]`, and `[IsOnFarm, Parrot]`.

```yaml
types:
   HomeAnimal: [ HasHome | IsOnFarm ,  Dog | Cat | Parrot ]
```

#### Using XML and JSON Schema

RAML allows the use of XML and JSON schemas to describe the body of an API request or response by integrating the schemas into its data type system.

The following examples show how to include an external JSON schema into a root-level type definition and a body declaration.

```yaml
types:
  Person: !include person.json
```

```yaml
/person:
  get:
    responses:
      200:
        body:
          application/json:
            type: !include person.json
```

A RAML processor MUST NOT allow types that define an XML or JSON schema to participate in type inheritance or specialization, or effectively in any [type expression](#type-expressions). Therefore, you cannot define sub-types of these types to declare new properties, add restrictions, set facets, or declare facets. You can, however, create simple type wrappers that add annotations, examples, display name, or a description.

The following example shows a valid declaration.

```yaml
types:
  Person:
    type: !include person.json
    description: this is a schema describing person
```

The following example shows an invalid declaration of a type that inherits the characteristics of a JSON schema and adds additional properties.

```yaml
types:
  Person:
    type: !include person.json
    properties: # invalid
      single: boolean
```

Another invalid case is shown in the following example of the type `Person` being used as a property type.

```yaml
types:
  Person:
    type: !include person.json
    description: this is a schema describing person
  Board:
    properties:
      members: Person[] # invalid use of type expression '[]' and as a property type
```

A RAML processor MUST be able to interpret and apply JSON Schema and XML Schema.

An XML schema, or JSON schema, MUST NOT be used where the media type does not allow XML-formatted data, or JSON-formatted data, respectively. XML and JSON schemas are also forbidden in any declaration of query parameters, query string, URI parameters, and headers.

The nodes "schemas" and "types", as well as "schema" and "type", are mutually exclusive and synonymous for compatibility with RAML 0.8. API definitions should use "types" and "type", as "schemas" and "schema" are deprecated and might be removed in a future RAML version.

##### References to Inner Elements

Sometimes it is necessary to refer to an element defined in a schema. RAML supports that by using URL fragments as shown in the example below.

```yaml
type: !include elements.xsd#Foo
```

When referencing an inner element of a schema, a RAML processor MUST validate an instance against that particular element. The RAML specification supports referencing any inner elements in JSON schemas that are valid schemas, any globally defined elements, and complex types in XML schemas. There are only a few restrictions:

* Validation of any XML or JSON instance against inner elements follows the same restrictions as the validation against a regular XML or JSON schema.
* Referencing complex types inside an XSD is valid to determine the structure of an XML instance, but since complex types do not define a name for the top-level XML element, these types cannot be used for serializing an XML instance.

### User-defined Facets

Facets express various additional restrictions beyond those which types impose on their instances, such as the optional `minimum` and `maximum` facets for numbers, or the `enum` facet for scalars. In addition to the built-in facets, RAML provides a way to declare user-defined facets for any data type.

The user-defined facet is declared using the OPTIONAL `facets` facet in a type declaration. The value of the `facets` facet is a map. The key names the user-defined facet. The corresponding value defines the concrete value that the respective facet can take. The syntax of a [property declaration](#property-declarations) and user-defined facet declaration are the same. A facet restricts instances of a subtype, not its type, based on the concrete value defined in the facet declaration.

Facet names MUST NOT begin with open parenthesis, to disambiguate the names from annotations. User-defined facet names on a type MUST NOT match built-in facets on that type, nor facet names of any ancestor type in the inheritance chain of the type.

If a facet of a type is declared as required, then any subtype of that type MUST define a value for that facet.

Here is an example that defines the capability to restrict dates to those that do not fall on holidays:

```yaml
#%RAML 1.0
title: API with Types
types:
  CustomDate:
    type: date-only
    facets:
      onlyFutureDates?: boolean # optional  in `PossibleMeetingDate`
      noHolidays: boolean # required in `PossibleMeetingDate`
  PossibleMeetingDate:
    type: CustomDate
    noHolidays: true
```

In this example, declaring the `noHolidays` facet and defining its values to be boolean makes it possible to restrict date instances that fall on holidays. Instances of any inheriting type, such as the `PossibleMeetingDate` type, must have values that do not fall on holidays.

User-defined facets by definition are not built into this RAML specification, and hence their semantic might not be understood by all RAML processors. Consequently, a RAML processor may or may not choose to use user-defined facets on a type in validating instances of that type. In the example above, a RAML processor may or may not assign a meaning to `noHolidays`, and therefore, may choose to ignore the `noHolidays: true` value in validating instances of `PossibleMeetingDate`.

### Determine Default Types

A RAML processor must be able to determine the default type of a type declaration by using the following rules:

* If, and only if, a type declaration contains a `properties` facet, then the default type is `object`. The following snippet exemplifies this rule:

  ```yaml
  types:
    Person:
      type: object
      properties:
  ```

  This rule can also be written as follows:

  ```yaml
  types:
    Person:
      # default type is `object`, no need to explicitly define it
      properties:
  ```

* If, and only if, a type declaration contains neither a `properties` facet nor a `type` or `schema` facet, then the default type is `string`. The following snippet exemplifies this rule:

  ```yaml
  types:
    Person:
      properties:
        name: # no type or schema necessary since the default type is `string`
  ```

* The default type `any` is applied to any `body` node that does not contain `properties`, `type`, or `schema`. For example:

  ```yaml
  body:
    application/json:
      # default type is `any`
  ```

  Or, if a default media type has been defined, no need to declare it here:

  ```yaml
  body:
    # default type is `any`
  ```

  Of course, each rule can be overridden by explicitly defining a type. For example:

  ```yaml
  types:
    Person:
      properties:
        name:
          type: number
  ```

### Type Expressions

Type expressions provide a powerful way of referring to, and even defining, types. Type expressions can be used wherever a type is expected. The simplest type expression is just the name of a type. Using type expressions, you can devise type unions, arrays, maps, and other things.

|Expression | Description |
|:--------|:------------|
| `Person` | The simplest type expression: A single type
| `Person[]` | An array of Person objects
| `string[]` | An array of string scalars
| `string[][]` | A bi-dimensional array of string scalars
| `string \| Person` | A union type made of members of string OR Person
| `(string \| Person)[]` | An array of the type shown above

Type expressions can be used wherever a type is expected:

```yaml
#%RAML 1.0
title: My API With Types

types:
  Phone:
    type: object
    properties:
      manufacturer:
        type: string
      numberOfSIMCards:
        type: number
  Notebook:
    type: object
    properties:
      manufacturer:
        type: string
      numberOfUSBPorts:
        type: number
  Person:
    type: object
    properties:
      devices: ( Phone | Notebook )[]
      reports: Person[]
```

You can even "extend" from a type expression. For example:

```yaml
#%RAML 1.0
title: My API With Types
types:
  Phone:
    type: object
    properties:
      manufacturer:
        type: string
      numberOfSIMCards:
        type: number
  Notebook:
    type: object
    properties:
      manufacturer:
        type: string
      numberOfUSBPorts:
        type: number
  Devices:
    type:  ( Phone | Notebook )[]
```

This example is actually declaring a "type alias", which gives a more readable name ( Devices ) to a type defined using a complex type expression. In this case, the type expression consists of an array of a union of the types Phone and Notebook. You can use this technique to give simple names to complex types. Type aliases can also hold extra properties, for example a description and annotations.

#### Grammar

Type expressions are composed of names of built-in or custom types and certain symbols, as follows:

| Expression Components | Description | Examples
|:--------------------------|:------------|:---------|
| type name | A type name, the basic building block of a type expression, used alone creates the simplest expression. | `number:` a built-in type<br><br>`Person:` a custom type
| (type expression) | Parentheses disambiguate the expression to which an operator applies. | `Person | Animal[]`<br><br>`( Person | Animal )[]`
| (type expression)[] | The array, a unary, postfix operator placed after another type expression, enclosed in parentheses as needed, indicates the resulting type is an array of instances of that type expression. | `string[]:` an array of strings<br><br>`Person[][]:` an array of arrays of Person instances
| (type expression 1) &#124; (type expression 2) | An infix union operator indicates the resulting type might be either of type expression 1 or of type expression 2. Multiple union operators can be combined between pairs of type expressions. | `string | number:` either a string or a number <br><br> `X | Y | Z`: either an X or a Y or a Z <br><br>`(Manager | Admin)[]:` an array whose members consist of Manager or Admin instances<br><br>`Manager[] | Admin[]:` an array of Manager instances or an array of Admin instances.

### Multiple Inheritance

RAML Types support multiple inheritance. This is achieved by passing a sequence of types:

```yaml
types:
  Person:
    type: object
    properties:
      name: string
  Employee:
    type: object
    properties:
      employeeNr: integer
  Teacher:
    type: [ Person, Employee ]
```

In the example above, the type `Teacher` inherits all restrictions from `Person` and `Employee`.

Multiple inheritance is allowed only if the sub-type is still a valid type declaration after inheriting all restrictions from its parent types. Also, it is not allowed to inherit from different kind of primitive types, for example `[ number, string ]`.

In the following example, the sub-type `Number3` is fully valid:

```yaml
types:
  Number1:
    type: number
    minimum: 4
  Number2:
    type: number
    maximum: 10
  Number3: [ Number1, Number2]
```

Whereas using the same example and only changing the maximum value of type `Number2` from 10 to 2 would result in an invalid type `Number3`.

```yaml
types:
  Number1:
    type: number
    minimum: 4
  Number2:
    type: number
    maximum: 2
  Number3: [ Number1, Number2] # invalid, maximum value cannot be less than minimum value
```

Section [union types](#union-multiple-inheritance) illustrates another example of how to validate types that use multiple inheritance and union types.

If a sub-type inherits properties having the same name from at least two of its parent types, the sub-type keeps all restrictions applied to those properties with two exceptions: 1) a "pattern" facet when a parent type already declares a "pattern" facet 2) a user-defined facet when another user-defined facet has the same value. In these cases, an invalid type declaration occurs.

### Inline Type Declarations

You can declare inline/anonymous types everywhere a type can be referenced except in a Type Expression.

```yaml
#%RAML 1.0
title: My API With Types
/users/{id}:
  get:
    responses:
      200:
        body:
          application/json:
            type: object
            properties:
              firstname:
                type: string
              lastname:
                type: string
              age:
                type: number
```

### Defining Examples in RAML

It is highly RECOMMENDED that API documentation include a rich selection of examples. RAML supports either the definition of multiple examples or a single one for any given instance of a type declaration.

#### Multiple Examples

The OPTIONAL **examples** facet can be used to attach multiple examples to a type declaration. Its value is a map of key-value pairs, where each key represents a unique identifier for an example and the value is a [single example](#single-example).

The following example shows the value of an **examples** facet:

```yaml
message: # {key} - unique id
  # example declaration
  title: Attention needed
  body: You have been added to group 274
record: # {key} - unique id
  # example declaration
  name: log item
  comment: permission check
```

#### Single Example

The OPTIONAL **example** facet can be used to attach an example of a type instance to the type declaration. There are two ways to represent the example facet value: as an explicit description of a specific type instance and as a map that contains additional facets.

##### As an explicit description of a specific type instance

For example:

```yaml
title: Attention needed
body: You have been added to group 274
```

##### As a map that contains additional facets

The map can contain the following additional facets:

| Facet | Description |
|:--------|:------------|
| displayName? | An alternate, human-friendly name for the example. If the example is part of an examples node, the default value is the unique identifier that is defined for this example.
| description? | A substantial, human-friendly description for an example. Its value is a string and MAY be formatted using [markdown](#markdown).
| (&lt;annotationName&gt;)? | [Annotations](#annotations) to be applied to this API. An annotation is a map having a key that begins with "(" and ends with ")" where the text enclosed in parentheses is the annotation name, and the value is an instance of that annotation.
| value | The actual example of a type instance.
| strict? | Validates the example against any type declaration (the default), or not. Set this to false avoid validation.

For example:

```yaml
(pii): true
strict: false
value:
  title: Attention needed
  body: You have been added to group 274
```

#### Example of how to define example/examples in RAML

The following snippet illustrates the usage of example and examples properties at different levels of a RAML API:

```yaml
#%RAML 1.0
title: API with Examples

types:
  User:
    type: object
    properties:
      name: string
      lastname: string
    example:
      name: Bob
      lastname: Marley
  Org:
    type: object
    properties:
      name: string
      address?: string
      value?: string
/organization:
  post:
    headers:
      UserID:
        description: the identifier for the user who posts a new organization
        type: string
        example: SWED-123 # single scalar example
    body:
      application/json:
        type: Org
        example: # single request body example
          value: # needs to be declared since instance contains a 'value' property
            name: Doe Enterprise
            value: Silver
  get:
    description: Returns an organization entity.
    responses:
      201:
        body:
          application/json:
            type: Org
            examples:
              acme:
                name: Acme
              softwareCorp:
                value: # validate against the available facets for the map value of an example
                  name: Software Corp
                  address: 35 Central Street
                  value: Gold # validate against an instance of the `value` property
```

### XML Serialization of Type Instances

To facilitate the potentially complex process of serialization to XML, RAML introduces an additional `xml` node for [type declarations](#type-declarations). This node is used to configure how type instances should be serialized to XML. The value of the `xml` node is a map that contains the following nodes:

| Name | Type | Description |
|:---------|:------:|:-----------------|
| attribute? | `boolean` | `true` serializes a type instance as an XML attribute. Can be `true` only for scalar types.<br/><br/>**Default:** `false`
| wrapped? | `boolean` | `true` wraps a type instance in its own XML element. Cannot be `true` for scalar types or `true` at the same moment `attribute` is `true`. <br/><br/>**Default:** `false`
| name? | `string` | Overrides the name of the XML element or XML attribute.<br/><br/>**Default:** name of either a type or property
| namespace? | `string` | Configures the name of the XML namespace.
| prefix? | `string` |  Configures the prefix used during serialization to XML.

The following type declaration shows an example of using the `xml` node:

```yaml
types:
  Person:
    properties:
      name:
        type: string
        xml:
          attribute: true # serialize it as an XML attribute
          name: "fullname" # attribute should be called fullname
      addresses:
        type: Address[]
        xml:
          wrapped: true # serialize it into its own <addresses>...</addresses> XML element
  Address:
    properties:
      street: string
      city: string
```

The example above can be serialized into the following XML:

```xml
<Person fullname="John Doe">
  <addresses>
     <Address>…</Address>
     ...
  </addresses>
</Person>
```

### Using Types in RAML

Types can be used in several positions:
  * Body ( JSON )
  * Body ( XML )
  * Body ( Web Form )
  * Headers
  * Query Parameters
  * URI Parameters

Key points about serialization are:

* Serialization rules depend on the type and the position in which the type is used.
* A "string" is the default serialization target of a custom value type, which is an extended "value" of a built-in type.
* An extended built-in type inherits its serialization target.

## Annotations

Annotations provide a mechanism to extend the API specification with metadata beyond the metadata already defined in this RAML 1.0 specification. Annotations can also be used to add properties to the built-in RAML nodes in certain locations within the RAML specification. Processors MAY support certain annotations to add additional specificity to the API description, enable tooling such as testing, support API repositories and API discovery, and so on. Processors MAY ignore any and all annotations.

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

|Target | Description |
|:--------|:------------|
| API | The root of a RAML document
| DocumentationItem | An item in the collection of items that is the value of the root-level documentation node
| Resource | A resource (relative URI) node, root-level or nested
| Method | A method node
| Response | A declaration of the responses node, whose key is an HTTP status code
| RequestBody | The body node of a method
| ResponseBody | The body node of a response
| TypeDeclaration | A data type declaration (inline or in a global types collection), header declaration, query parameter declaration, URI parameter declaration, or a property within any of these declarations, where the type property can be used
| Example | Either an example or examples node
| ResourceType | A resource type node
| Trait | A trait node
| SecurityScheme | A security scheme declaration
| SecuritySchemeSettings | The settings node of a security scheme declaration
| AnnotationType | A declaration of the annotationTypes node, whose key is a name of an annotation type and whose value describes the annotation
| Library | The root of a library
| Overlay | The root of an overlay
| Extension | The root of an extension

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
