# Manual: How to reuse Gravity data extractor

### Table Of Contents

1. Introduction
2. Initialization
3. First overview
4. Things to override
5. Extending the logic

### Introduction

This manual provides steps that will help you to build your own extractor tied to a specific endpoint. The example described in this manual uses a Binance price extractor. The data bridge used is HTTP. The main repository for the extractor source code is: [https://github.com/Gravity-Hub-Org/gravity-node-data-extractor](https://github.com/Gravity-Hub-Org/gravity-node-data-extractor)

### Initialization

First, clone the [repo](https://github.com/Gravity-Hub-Org/gravity-node-data-extractor) and build Swagger meta data:

> bash ./bootstrap.sh

Enter info about your extractor. It will be visible in the Swagger UI spec.

> Enter extractor host: explorer.gravityhub.org Enter contact website: gravityhub.org Enter contact email: [oracles@gravityhub.org](mailto:oracles@gravityhub.org)

This command will add Swagger metadata to your extractor.

### First overview

The file directory is described below.

[![Things to override](https://camo.githubusercontent.com/aeddb3fa6b9be196781bcddd001ea29a4f6878ed/68747470733a2f2f692e696d6775722e636f6d2f596e4a755070662e706e67)](https://camo.githubusercontent.com/aeddb3fa6b9be196781bcddd001ea29a4f6878ed/68747470733a2f2f692e696d6775722e636f6d2f596e4a755070662e706e67)

1. Controller - implements data transition
2. Docs - a directory for documentation
3. Model - model definitions, both internal and public
4. Router - entities responsible for routing

Let us take a look at the main.go file, specifically at the _**init**_ and _**main**_ functions:

Init function takes care of CLI parameters that can be crucial depending on the type of extractor that you are implementing.

[![Init fn](https://camo.githubusercontent.com/d40e04057de866c42fa31870f4d27cd4307f19e9/68747470733a2f2f692e696d6775722e636f6d2f5a436c48614f452e706e67)](https://camo.githubusercontent.com/d40e04057de866c42fa31870f4d27cd4307f19e9/68747470733a2f2f692e696d6775722e636f6d2f5a436c48614f452e706e67)

For example:

1. Almost _any_ price extractor _will_ process the pair name parameter from the CLI. Multiple extractors can be run for different price pairs, just by handling the params. \(pair param\)
2. Multiple extractors can be combined in one repo. It gives an ability to choose an implementation _**at runtime**_, just by passing an extractor type. \(type param\)
3. It is also necessary to provide an appropriate extractor tag. \(tag param\)

The main.go file creates a concrete data transport controller instance \(_ResponseController_\).

The business logic of transported data is incapsulated in the _ResponseController_.

[![Main fn](https://camo.githubusercontent.com/a2cd78ca40275e77741899c5a51d622ce891e333/68747470733a2f2f692e696d6775722e636f6d2f7952614a5067672e706e67)](https://camo.githubusercontent.com/a2cd78ca40275e77741899c5a51d622ce891e333/68747470733a2f2f692e696d6775722e636f6d2f7952614a5067672e706e67)

"r" is a router singleton that provides reference orr routes, in this example for HTTP routes.

The extractor switching _**at runtime**_ was mentioned above. In order to achieve such a goal, an enumerator is needed. [enum.go](https://github.com/Gravity-Hub-Org/gravity-node-data-extractor/blob/boilerplate/docs/models/enum.go)

[![Extractor enumerator](https://camo.githubusercontent.com/f11d83c5a4980f21c76cad48c307a2071378ec87/68747470733a2f2f692e696d6775722e636f6d2f4b6c33426a53362e706e67)](https://camo.githubusercontent.com/f11d83c5a4980f21c76cad48c307a2071378ec87/68747470733a2f2f692e696d6775722e636f6d2f4b6c33426a53362e706e67)

Also, available extractor types must be declared internally. \(_binance_, _metal_\)

### Things to override

If the goal is to extend/mutate existing implementations, follow these steps:

1. Go into the _models directory_
2. Declare a new model or extend the existing one. You must implement all _IExtractor interface methods_ in order to declare a valid extractor. [![enter image description here](https://camo.githubusercontent.com/893832c330c9a537b7fcd55e469b7ef18bebc8ff/68747470733a2f2f692e696d6775722e636f6d2f54443646586d412e706e67)](https://camo.githubusercontent.com/893832c330c9a537b7fcd55e469b7ef18bebc8ff/68747470733a2f2f692e696d6775722e636f6d2f54443646586d412e706e67)

Do not forget to mark structs you want to expose by specifying a Swagger model declaration.

> // swagger:model

You can find more details on goswagger here

1. Provide an implementation with the name in _enum.go_
2. Open the _ResponseController_ declaration file

[![ResponseController internal extractor](https://camo.githubusercontent.com/5aa9af7dcfb5992bdfd03e2200ff9b8716bcdedc/68747470733a2f2f692e696d6775722e636f6d2f6f424a357963372e706e67)](https://camo.githubusercontent.com/5aa9af7dcfb5992bdfd03e2200ff9b8716bcdedc/68747470733a2f2f692e696d6775722e636f6d2f6f424a357963372e706e67)

Here you can provide logic for handling specific initialization data.

#### Extending the logic

It is encouraged to extend the existing system by adding more layers of abstraction. However, it is necessary to keep in mind that violating the existing interface constraints mentioned in [scheme.md](https://github.com/Gravity-Hub-Org/gravity-node-data-extractor/blob/boilerplate/docs/docs/scheme.md) is considered as a system _anti-pattern_.

