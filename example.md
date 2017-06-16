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
- [Vehicle Types](#raml-data-types)
- [Defining a garage](#garage)
- [Modularization](#modularizatoin)
- [References](#references)

<!-- /TOC -->

## Introduction

This specification describes the Raml Datatypes Language (RDL). RAML is a human- and machine-readable language for the definition of a RESTful application programming interface (API). RAML is designed to improve the specification of the API by providing a format that the API provider and API consumers can use as a mutual contract. RAML can, for example, facilitate providing user documentation and source code stubs for client and server implementations. Such provisions streamline and enhance the definition and development of interoperable applications that utilize RESTful APIs.

RAML introduces the innovative concept of resource types and traits for characterizing and reusing patterns of resources and associated methods. Using resource types and traits minimizes the repetition in a RESTful API design and promotes consistency within and across APIs.

## Markup Language

This specification uses default [RAML dialects markup](markup.md) 
## Vehicle specification language

Vehicle specification language is a RAML dialect which may be used to specify `Vehicles`

Toys specification language defines following `Fragments`

`Library` - which may be used to declare library of vehicle kinds.

Library fragment extends an abstract [library definition](libraries.md) and itroduces following declarations.

`vehicleModels` - map of model name to the `Vehicle` descriptions

`vehicleProducers` - map of vehicle producer name to `VehicleProducerDescription`


`Garage` - which describes a garage of vehicles.

`Overlay` - which describes an overlay on the `Garage`.
