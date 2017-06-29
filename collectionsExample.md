
# RAML ActiveObject Language: 

## Abstract

This is an example of how one may define a language by adding new a builtin types in RAML Type System. 

## Introduction 

### Summary

This language allows you to define APIs in terms of Entities, Resources/Collections/Subcollections/Actions

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

This specification describes an alternative approach to API definition. It is based on RAML types and extends it by adding following [Active Objects library](activeObjects.raml) as a built-in type definitions. 


