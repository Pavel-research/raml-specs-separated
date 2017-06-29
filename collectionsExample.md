
# RAML Collections Language: 

## Abstract

This is an example of how one may define a language by adding new a builtin types in RAML. 

## Introduction 

### Summary

 * Alternative to describing garage in the natural language
 * "Feels like" describing stuff in yaml
 * Allows you to describe your garage fomally
 * Concise syntax
 * Can be serialized as JSON and YAML
 * Can be inlined in JSON and YAML documents
 * Designed with convenience in mind

Vehicles allows you to describe known vehicles kinds, and then describe vehicles in your garage using them.

## Status of this Document

This document constitutes the Vehicles 1.0 specification. 

## Terminology and Conformance Language

Normative text describes one or both of the following kinds of elements:

* Vital elements of the specification
* Elements that contain the conformance language key words as defined by [IETF RFC 2119](https://www.ietf.org/rfc/rfc2119.txt) "Key words for use in RFCs to Indicate Requirement Levels"

Informative text is potentially helpful to the user, but dispensable. Informative text can be changed, added, or deleted editorially without negatively affecting the implementation of the specification. Informative text does not contain conformance keywords.

All text in this document is, by default, normative.

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [IETF RFC 2119](https://www.ietf.org/rfc/rfc2119.txt) "Key words for use in RFCs to Indicate Requirement Levels".

## Definitions and Terminology

Vehicle (from Latin: vehiculum[1]) is a mobile machine that transports people or cargo. Typical vehicles include wagons, bicycles, motor vehicles (motorcycles, trucks, buses), railed vehicles (trains, trams), watercraft (ships, boats), aircraft and spacecraft.[2]

Land vehicles are classified broadly by what is used to apply steering and drive forces against the ground: wheeled, tracked, railed or skied. ISO 3833-1977 is the standard, also internationally used in legislation, for road vehicles types, terms and definitions.[3]

### Markdown

Throughout this specification, **Markdown** means [GitHub-Flavored Markdown](https://help.github.com/articles/github-flavored-markdown/).

## Table of Content

<!-- TOC -->

- [Introduction](#introduction)
- [Markup Language](#markup-language)
- [Defining a garage](#garage)
- [Vehicle Types](#raml-data-types)
- [Modularization](#modularization)
- [Annotations](#annotations)

<!-- /TOC -->

## Introduction

This specification describes the Vehicle Specification Language (VSL). VSL is a human- and machine-readable language for the definition of your garage.

## Markup Language

This specification uses default [RAML dialects markup](markup.md) 

## Defining a garage

The following table enumerates the possible nodes at the root of the Garage:

| Name  | Description |
|:----------|:----------|
| title | A short, plain-text label for the garage. Its value is a string.
| description? | A substantial, human-friendly description of the garage. Its value is a string and MAY be formatted using [markdown](#markdown).
| location? | The locatoin of the garage, for example "Colorado". Its value is a string.
| vehicleTypes? | Declarations of [vehicle types](#resource-types-and-traits) for use within the garage.
| vehicles? | Declarations of [vehicles](#vechicles) existing in the garage.

| annotationTypes? | Declarations of [annotation types](annotations.md#declaring-annotation-types) for use by annotations.
| (&lt;annotationName&gt;)? | [Annotations](annotations.md#annotations) to be applied to this API. An annotation is a map having a key that begins with "(" and ends with ")" where the text enclosed in parentheses is the annotation name, and the value is an instance of that annotation.
| uses? | Imported external [libraries](#libraries) for use within the garage.

## Vehicle types

The following table enumerates the possible nodes defining a vehicle type:


| Name  | Description |
|:----------|:----------|
| title | A short, plain-text label for the vehicle type. Its value is a string.
| description? | A substantial, human-friendly description of the vehicle type. Its value is a string and MAY be formatted using [markdown](#markdown).
| producer? | The producer of the vehicle type.
| (&lt;annotationName&gt;)? | [Annotations](annotationd.md#annotations) to be applied to this API. An annotation is a map having a key that begins with "(" and ends with ")" where the text enclosed in parentheses is the annotation name, and the value is an instance of that annotation.

## Vehicles

The following table enumerates the possible nodes defining a vehicle :


| Name  | Description |
|:----------|:----------|
| title | A short, plain-text label for the vehicle.
| description? | A substantial, human-friendly description of the vehicle).
| type | The reference to vehicle type.
| (&lt;annotationName&gt;)? | [Annotations](annotationd.md#annotations) to be applied to this API. An annotation is a map having a key that begins with "(" and ends with ")" where the text enclosed in parentheses is the annotation name, and the value is an instance of that annotation.


## Modularization

### Structural includes

Garage raml dialect supports using structural includes to modularize your garage specification, syntax and semantic of them is defined in [Structural includes](includes.md).

### Libraries

Garage raml dialect supports Libraries to define reusable collections of Vehicle Types. Garage raml dialect uses following specification for declaring libraries [library definitions](libraries.md). 

## Annotations

Garage raml dialect allows you to annotate your garage and vehicle types with additional metadata, to do it you may use annotations and annotation types properties defined in [Annotations definition specification](annotations.md).


## Public fragments

Garage specification declares following public fragments:

* Garage - for defining garage
* Vehicle - for defining vehicle 
* VehicleType - for defining vehicle type
* Library - for defining library of vehicle types

Garage fragment is a default fragment when parsing garage
