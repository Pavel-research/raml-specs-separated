# RAML Vehicles Version 1.0: 

## Abstract

Sometimes you need to describe vehicles in your garage. 

## Introduction 

### Summary

 * Alternative to describing vehicles in the natural language
 * "Feels like" describing stuff in yaml
 * Allows you to describe your vehicles fomally
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
- [Vehicle Types](#raml-data-types)
- [Defining a garage](#garage)
- [Modularization](#modularization)
- [Annotations](#annotations)

<!-- /TOC -->

## Introduction

This specification describes the Vehicle Specification Language (VSL). VSL is a human- and machine-readable language for the definition of your garage.

## Markup Language

This specification uses default [RAML dialects markup](markup.md) 

## Vehicle types

Vehicle specification language is a RAML dialect which may be used to specify `Vehicles`

Toys specification language defines following `Fragments`

`Library` - which may be used to declare library of vehicle kinds.

Library fragment extends an abstract [library definition](libraries.md) and itroduces following declarations.

`vehicleModels` - map of model name to the `Vehicle` descriptions

`vehicleProducers` - map of vehicle producer name to `VehicleProducerDescription`


`Garage` - which describes a garage of vehicles.

`Overlay` - which describes an overlay on the `Garage`.
