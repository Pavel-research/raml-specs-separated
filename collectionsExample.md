
# RAML ActiveObject Language: 

## Abstract

This is an example of how one may define a language by adding new a builtin types in RAML Type System. 

## Definition  

This specification describes an alternative approach to API definition. It is based on RAML types and extends it by adding following [Active Objects library](activeObjects.raml) as a built-in types definitions. 

## Example
```raml
#%RAML 1.0 ActiveObjects
types:
  Task:
    type: Object
    properties:
      title: string
      done: boolean

  Person:
    type: Object
    properties:
      name: string
      lastName: string
    memberCollections:
      tasks: Task
  Team:
    type: Object
    properties:
      name: string
      lastName: string
    memberCollections:
      people: Person
  TeamApi:
    type: Entry
    baseUri: http://teams.org
    memberCollections:
      teams: Team
```
alternatively same structure may be defined as a valid RAML 1.0 library [Example](activeObjectsExample.raml) 

##Fragment Name

This specification uses ActiveObjects as a fragment name


