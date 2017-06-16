## Vehicle specification language

Vehicle specification language is a RAML dialect which may be used to specify `Vehicles`

Toys specification language defines following `Fragments`

`Library` - which may be used to declare library of vehicle kinds.

Library fragment extends an abstract [library definition](libraries.md) and itroduces following declarations.

`vehicleModels` - map of model name to the `Vehicle` descriptions

`vehicleProducers` - map of vehicle producer name to `VehicleProducerDescription`


`Garage` - which describes a garage of vehicles.

`Overlay` - which describes an overlay on the `Garage`.
