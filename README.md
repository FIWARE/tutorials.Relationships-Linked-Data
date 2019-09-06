[![FIWARE Banner](https://fiware.github.io/tutorials.Relationships-Linked-Data/img/fiware.png)](https://www.fiware.org/developers)

[![FIWARE Core Context Management](https://nexus.lab.fiware.org/repository/raw/public/badges/chapters/core.svg)](https://github.com/FIWARE/catalogue/blob/master/core/README.md)
[![License: MIT](https://img.shields.io/github/license/fiware/tutorials.Relationships-Linked-Data.svg)](https://opensource.org/licenses/MIT)
[![Support badge](https://nexus.lab.fiware.org/repository/raw/public/badges/stackoverflow/fiware.svg)](https://stackoverflow.com/questions/tagged/fiware)
[![NGSI LD](https://img.shields.io/badge/NGSI-linked_data-red.svg)](https://www.etsi.org/deliver/etsi_gs/CIM/001_099/009/01.01.01_60/gs_CIM009v010101p.pdf)
<br/> [![Documentation](https://img.shields.io/readthedocs/fiware-tutorials.svg)](https://fiware-tutorials.rtfd.io)

This tutorial discusses relationships between linked data entities and how the
concepts of **JSON-LD** and **NGSI-LD** can be used to interrogate entities and navigate from one entity to another. The tutorial discusses a series of simple linked-data data models based around the supermarket chain’s store finder application, and demonstrates how to design models holding one-to-one, one-to-many and many-to-many relationships. This **NGSI-LD** tutorial is a direct analogue to the earlier _Understanding Entities and Relationships_ tutorial (which was based on the **NGSI v2** interface). The differences in
relationships created using **NSGI v2** and **NGSI-LD** are highlighted
and discussed in detail.

The tutorial uses [cUrl](https://ec.haxx.se/) commands throughout, but is also available as
[Postman documentation](https://fiware.github.io/tutorials.Relationships-Linked-Data/)

[![Run in Postman](https://run.pstmn.io/button.svg)](https://app.getpostman.com/run-collection/125db8d3a1ea3dab8e3f)


# Relationships in Linked Data

All NGSI data entity attributes can be divided into one of two types.

- _Property_ attributes
- _Relationship_ attributes

For each entity, the _Property_ attributes (including various subtypes such as _GeoProperty_ , _TemporalProperty_ and time values) define the current state something in the real world. As the state of the entity changes the `value`  of each _Property_ is updated to align with the last real world reading of the
the attribute. All _Property_ attributes relate to the state of a single entity.

_Relationship_ attributes correspond to the interactions **between** entities (which are expected to change over time). They effectively provide the graph linking the nodes of the data entities together.
Each _Relationship_ attribute holds an `object` in the form of a URN - effectively a pointer to another object. _Relationship_ attributes do not hold data themselves,

Both properties and relationships may in turn have a linked
embedded structure (of _properties-of-properties_ or _properties-of-relationships or relationships-of-properties_ or
_relationships-of-relationships_ etc.) which lead  a full complex knowledge graph.


## Designing Data Models

In order for computers to be able to navigate linked data structures, a full context must be present.






ithin the FIWARE platform, the context of an entity represents the state of a physical or conceptural object which
exists in the real world.

## Entities within a stock management system

For a simple stock management system, we will only need four types of entity. The relationship between our entities is
defined as shown:

![](https://fiware.github.io/tutorials.Entity-Relationships/img/entities.png)

-   A store is a real world bricks and mortar building. **Store** entities would have properties such as:
    -   A name of the store e.g. "Checkpoint Markt"
    -   An address "Friedrichstraße 44, 10969 Kreuzberg, Berlin"
    -   A phyiscal location e.g. _52.5075 N, 13.3903 E_
-   A shelf is a real world device to hold objects which we wish to sell. Each **Shelf** entity would have properties
    such as:
    -   A name of the shelf e.g. "Wall Unit"
    -   A phyiscal location e.g. _52.5075 N, 13.3903 E_
    -   A maximum capacity
    -   An association to the store in which the shelf is present
-   A product is defined as something that we sell - it is conceptural object. **Product** entities would have
    properties such as:
    -   A name of the product e.g. "Vodka"
    -   A price e.g. 13.99 Euros
    -   A size e.g. Small
-   An inventory item is another conceptural entity, used to assocate products, stores, shelves and physical objects.
    **Inventory Item** entities would have properties such as:
    -   An association to the product being sold
    -   An association to the store in which the product is being sold
    -   An association to the shelf where the product is being displayed
    -   A stock count of the quantity of the product available in the warehouse
    -   A stock count of the quantity of the product available on the shelf

As you can see, each of the entities defined above contain some properties which are liable to change. A product could
change its price, stock could be sold and the shelf count of stock could be reduced and so on.



An NGSI LD Data Entity (e.g. a supermarket):

-   Has an `id` which must be unique. For example `urn:ngsi-ld:Building:store001`,
-   Has `type` which should be a fully qualified URI of a well defined data model. For example
    `https://uri.fiware.org/ns/datamodels#Building`. Authors can also use type names, as short hand strings for types,
    mapped to fully qualified URIs through the JSON-LD `@context`.
-   Has _property_ of the entity, for example, an `address` attribute which holds the address of the store. This can be
    expanded into `http://schema.org/address`, which is known as a fully qualified name
    ([FQN](https://en.wikipedia.org/wiki/Fully_qualified_name)).
-   The `address`, like any _property_ will have a _value_ corresponding to the _property_ `address` (e.g. _Bornholmer
    Straße 65, 10439 Prenzlauer Berg, Berlin_
-   Has a _property-of-a-property_ of the entity, for example a `verified` field for the `address`.
-   Has a _relationship_ of the entity, for example, a `managedBy` field where the relationship `managedBy` corresponds
    to another data entity : `urn:ngsi-ld:Person:bob-the-manager`
-   The relationship `managedBy`, may itself have a _property-of-a-relationship_ (e.g. `since`), this holds the date Bob
    started working the store
-   The relationship `managedBy`, may itself have a _relationship-of-a-relationship_ (e.g. `subordinateTo`), this holds
    the URN of the area manager above Bob in the hierarchy.

As you can see the knowledge graph is well defined and can be expanded indefinitely.






Relationships will be dealt with in more detail in a subsequent tutorial.

# Prerequisites

## Docker

To keep things simple all components will be run using [Docker](https://www.docker.com). **Docker** is a container
technology which allows to different components isolated into their respective environments.

-   To install Docker on Windows follow the instructions [here](https://docs.docker.com/docker-for-windows/)
-   To install Docker on Mac follow the instructions [here](https://docs.docker.com/docker-for-mac/)
-   To install Docker on Linux follow the instructions [here](https://docs.docker.com/install/)

**Docker Compose** is a tool for defining and running multi-container Docker applications. A
[YAML file](https://raw.githubusercontent.com/Fiware/tutorials.Identity-Management/master/docker-compose.yml) is used
configure the required services for the application. This means all container services can be brought up in a single
command. Docker Compose is installed by default as part of Docker for Windows and Docker for Mac, however Linux users
will need to follow the instructions found [here](https://docs.docker.com/compose/install/)

## Cygwin

We will start up our services using a simple bash script. Windows users should download [cygwin](http://www.cygwin.com/)
to provide a command-line functionality similar to a Linux distribution on Windows.

# Architecture

The demo application will send and receive NGSI-LD calls to a compliant context broker. Since both NSGI v2 and NSGI-LD
interfaces are available to an experimental version fo the
[Orion Context Broker](https://fiware-orion.readthedocs.io/en/latest/), our demo application will only make use of one
FIWARE component.

Currently, the Orion Context Broker relies on open source [MongoDB](https://www.mongodb.com/) technology to keep
persistence of the context data it holds. Therefore, the architecture will consist of two elements:

-   The [Orion Context Broker](https://fiware-orion.readthedocs.io/en/latest/) which will receive requests using
    [NGSI-LD](https://forge.etsi.org/swagger/ui/?url=https://forge.etsi.org/gitlab/NGSI-LD/NGSI-LD/raw/master/spec/updated/full_api.json)
-   The underlying [MongoDB](https://www.mongodb.com/) database :
    -   Used by the Orion Context Broker to hold context data information such as data entities, subscriptions and
        registrations

Since all interactions between the two elements are initiated by HTTP requests, the elements can be containerized and
run from exposed ports.

![](https://fiware.github.io/tutorials.Relationships-Linked-Data/img/architecture.png)

The necessary configuration information can be seen in the services section of the associated `docker-compose.yml` file:

```yaml
orion:
    image: fiware/orion-ld
    hostname: orion
    container_name: fiware-orion
    depends_on:
        - mongo-db
    networks:
        - default
    ports:
        - "1026:1026"
    command: -dbhost mongo-db -logLevel DEBUG
    healthcheck:
        test: curl --fail -s http://orion:1026/version || exit 1
```

```yaml
mongo-db:
    image: mongo:3.6
    hostname: mongo-db
    container_name: db-mongo
    expose:
        - "27017"
    ports:
        - "27017:27017"
    networks:
        - default
    command: --nojournal
```

Both containers are residing on the same network - the Orion Context Broker is listening on Port `1026` and MongoDB is
listening on the default port `27071`. Both containers are also exposing the same ports externally - this is purely for
the tutorial access - so that cUrl or Postman can access them without being part of the same network. The command-line
initialization should be self explanatory.

The only notable difference to the introductory tutorials is that the required image name is currently
`fiware/orion-ld`.

# Start Up

All services can be initialised from the command-line by running the
[services](https://github.com/FIWARE/tutorials.Relationships-Linked-Data/blob/master/services) Bash script provided within the
repository. Please clone the repository and create the necessary images by running the commands as shown:

```bash
git clone git@github.com:FIWARE/tutorials.Relationships-Linked-Data.git
cd tutorials.Relationships-Linked-Data

./services start
```

> **Note:** If you want to clean up and start over again you can do so with the following command:
>
> ```
> ./services stop
> ```

---







---

## License

[MIT](LICENSE) © 2019 FIWARE Foundation e.V.
