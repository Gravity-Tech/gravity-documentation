# \[DEPRECATED\] Gravity Extractor Architecture

### Abstract

This document describes the UML architecture of the Gravity built-in data extractor. The architecture and implementation of extractors is developed by the **Gravity Core Team**. The implementation described in this document is the earliest initial version, so it is considered as _built-in_.

### Foreword

The architecture provided in this document is offered as _**best practice**_ guidance for Gravity protocol node operators and contributors.

Violating the specification and object relations can lead to unexpected behaviour of some system parts of the Gravity Node.

### Scheme Overview

![Gravity data extractor scheme](https://camo.githubusercontent.com/6ee229b79ca94af372aa55af313e01393499182b/68747470733a2f2f692e696d6775722e636f6d2f786b6b467372552e6a7067)

### Main concepts

The main concepts behind the implementation of extractors are:

1. Providing a stateless system & avoiding data mutability
2. Conforming to _**any**_ kind of data \(reusability\)
3. Manifesting available operations

#### Stateless vs stateful

In designing modern systems, it is necessary to achieve a certain balance in how objects are manipulated.

The vast majority of applications combine both stateless and stateful system parts.

As regards to the _Gravity protocol_, it's not an exception. Such parts of the system that are responsible for data mutability and storage are considered stateful.

By design, extractors are aimed to only perform data aggregation and mapping procedures, which is the reason for also introducing a stateless approach. In addition, _extractors are isolated_, meaning that they are not "aware" of any particular data consumers.

#### Conformance to any data & Reusability

The current architecture gives an ability to _**transform**_ and _**transport**_ data in any way that is necessary for a particular use-case. There are two interfaces that implement this functionality:

1. Extractor&lt;T, R&gt; - This interface declares methods on how the data is _**transformed**_,
2. IDataBridge&lt;T, R&gt; - This interface declares methods on how the data is _**transported**_.

Generic types provided in declarations represent:

1. T - raw data type,
2. R - transformed response data type.

Furthermore, such approach gives us an ability to represent any kind of data and deliver it in different ways.

The scheme below shows possible implementations:

![Gravity data extractor response controller examples](https://camo.githubusercontent.com/ff4ecbc579dd4aaee200e0012aa4df57d15eab27/68747470733a2f2f692e696d6775722e636f6d2f526e5069314b772e706e67)

The _**reusability of every distinct system part**_ translates into the _**reusability of the whole system**_.

#### Available operations

The design requires three accessible endpoints to be implemented. These are needed for:

1. Fetching raw data.
2. Fetching mapped/transformed data.
3. Fetching extractor info in JSON format, containing the data feed tag and description.

