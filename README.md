[![FIWARE Banner](https://fiware.github.io/tutorials.Relationships-Linked-Data/img/fiware.png)](https://www.fiware.org/developers)

[![FIWARE Core Context Management](https://nexus.lab.fiware.org/repository/raw/public/badges/chapters/core.svg)](https://github.com/FIWARE/catalogue/blob/master/core/README.md)
[![License: MIT](https://img.shields.io/github/license/fiware/tutorials.Relationships-Linked-Data.svg)](https://opensource.org/licenses/MIT)
[![Support badge](https://nexus.lab.fiware.org/repository/raw/public/badges/stackoverflow/fiware.svg)](https://stackoverflow.com/questions/tagged/fiware)
[![NGSI LD](https://img.shields.io/badge/NGSI-linked_data-red.svg)](https://www.etsi.org/deliver/etsi_gs/CIM/001_099/009/01.01.01_60/gs_CIM009v010101p.pdf)
<br/> [![Documentation](https://img.shields.io/readthedocs/fiware-tutorials.svg)](https://fiware-tutorials.rtfd.io)

This tutorial discusses relationships between linked data entities and how the concepts of **JSON-LD** and **NGSI-LD**
can be used to interrogate entities and navigate from one entity to another. The tutorial discusses a series of simple
linked-data data models based around the supermarket chain’s store finder application, and demonstrates how to design
models holding one-to-one, one-to-many and many-to-many relationships. This **NGSI-LD** tutorial is a direct analogue to
the earlier _Understanding Entities and Relationships_ tutorial (which was based on the **NGSI v2** interface). The
differences in relationships created using **NSGI v2** and **NGSI-LD** are highlighted and discussed in detail.

The tutorial uses [cUrl](https://ec.haxx.se/) commands throughout, but is also available as
[Postman documentation](https://fiware.github.io/tutorials.Relationships-Linked-Data/)

[![Run in Postman](https://run.pstmn.io/button.svg)](https://app.getpostman.com/run-collection/125db8d3a1ea3dab8e3f)

# Relationships in Linked Data

All NGSI data entity attributes can be divided into one of two types.

-   _Property_ attributes
-   _Relationship_ attributes

For each entity, the _Property_ attributes (including various subtypes such as _GeoProperty_ , _TemporalProperty_ and
time values) define the current state something in the real world. As the state of the entity changes the `value` of
each _Property_ is updated to align with the last real world reading of the the attribute. All _Property_ attributes
relate to the state of a single entity.

_Relationship_ attributes correspond to the interactions **between** entities (which are expected to change over time).
They effectively provide the graph linking the nodes of the data entities together. Each _Relationship_ attribute holds
an `object` in the form of a URN - effectively a pointer to another object. _Relationship_ attributes do not hold data
themselves.

Both properties and relationships may in turn have a linked embedded structure (of _properties-of-properties_ or
_properties-of-relationships or relationships-of-properties_ or _relationships-of-relationships_ etc.) which lead a full
complex knowledge graph.

## Designing Data Models using JSON-LD

In order for computers to be able to navigate linked data structures, proper ontologically correct data models must be
created and a full `@context` must be defined and made accessible. We can do this by reviewing and updating the existing
data models from the NGSI v2 [Entity Relationships](https://github.com/FIWARE/tutorials.Entity-Relationships) tutorial.

### Revision: Data Models for a Stock management system as defined using NGSI-v2

As a reminder, four types of entity were created in the NGSI v2 stock management system. The relationship between the
four NGSI v2 entity models was defined as shown below:

![](https://jason-fox.github.io/tutorials.Relationships-Linked-Data/img/entities-v2.png)

More details can be found in the NGSI v2
[Entity Relationships](https://github.com/FIWARE/tutorials.Entity-Relationships) tutorial.

In NGSI v2 relationship attributes are just standard properties attributes. By convention NGSI v2 relationship
attributes are given names starting `ref` and are defined using the `type="Relationship"`. However, this is merely
convention and may not be followed in all cases. There is no infallible mechanism for detecting which attributes are
associative relationships between entities.

### Data Models for a Stock management system defined using NGSI-LD

The richer [JSON-LD](https://json-ld.org/spec/FCGS/json-ld/20130328) description language is able to define NSGI-LD
entities by linking entities directly as shown below.

![](https://jason-fox.github.io/tutorials.Relationships-Linked-Data/img/entities-ld.png)

The complete data model must be understandable by both developers and machines.

-   A full Human readable definition of this data model can be found
    [online](https://fiware.github.io/tutorials.Step-by-Step/schema).
-   The machine readable JSON-LD defintion can be found at
    [`https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld`](https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld) -
    this file will be used to provide the `@context` to power our NGSI-LD data entities.

Four data models have been created for this NGSI-LD stock management system. The relationships between the models are
described below:

-   The [**Store** model](https://fiware.github.io/tutorials.Step-by-Step/schema/Store/) is now based on and extends the
    FIWARE
    [**Building** model](https://fiware-datamodels.readthedocs.io/en/latest/Building/Building/doc/spec/index.html). This
    ensures that it offers standard properties for `name`, `address` and category.
    -   A Building will hold `furniture` this is a 1-many relationship.
        -   Building :arrow_right: Shelf.
-   The [**Shelf** model](https://fiware.github.io/tutorials.Step-by-Step/schema/Shelf/) is a custom data model defined
    for the tutorial
    -   Each **Shelf** is `locatedIn` a **Building**. This is a 1-1 relationship. It is the reciprical relationship to
        `furniture` defined above.
        -   Shelf :arrow_right: Building.
    -   A **Shelf** is `installedBy` a **Person** - this is a 1-1 relationship. A shelf knows who installed it, but it
        is this knowledge is not part of the Person entity itself.
        -   Shelf :arrow_right: Person
    -   A **Shelf** `stocks` a given **Product**. This is another 1-1 relationship, and again it is not recipricated. A
        **Product** does not know which **Shelf** it is to be found on.
        -   Shelf :arrow_right: Product
-   A [**StockOrder** model](https://fiware.github.io/tutorials.Step-by-Step/schema/StockOrder/) replaces the
    **Inventory Item** bridge table defined for NGSI v2 :
    -   A **StockOrder** is `requestedBy` a **Person** - this is a 1-1 relationship.
        -   StockOrder :arrow_right: Person.
    -   A **StockOrder** is `requestedFor` a **Building** - this is a 1-1 relationship.
        -   StockOrder :arrow_right: Building.
    -   A **StockOrder** is a request for a specific `orderedProduct` - this 1-1 relationship.
        -   StockOrder :arrow_right: Product.
-   The [**Product** model](https://fiware.github.io/tutorials.Step-by-Step/schema/Product/) remains unchanged. It has
    no relationships of its own.

Additionally some relationships have been defined to be linked to `https://schema.org/Person` entities. This could be
outlinks to a separate HR system for example.

## Comparison between Linked and Non-Linked Data Systems

Obviously within a single isolated Smart System itself, it makes no difference whether a rich, complex linked-data
architecture is used or a simpler, non-linked-data system is created. However if the data is designed to be shared, then
linked data is a requirement to avoid data silos. An external system is unable to "know" what relationships are unless
they have been provided in a machine readable form.

### :arrow_forward: Video: Rich Snippets: Product Search

A simple example of an external system interogating for structured data can be found in online product search. Machines
from third parties such as Google are able to read product information (encoded using a standard
[**Product** data model](https://jsonld.com/product/)) and display a rich snippet of product information with a standard
star rating.

[![](http://img.youtube.com/vi/_-rRxKSm2ic/0.jpg)](https://www.youtube.com/watch?v=_-rRxKSm2ic "Rich Snippets")

Click on the image above to watch an introductory video on rich snippets for product search.

Further machine readable data model examples can be found on the [Steal Our JSON-LD](https://jsonld.com/) website.

## Traversing relationships

> **Example**: Imagine the scenario where a pallet of Products are moved from stock in the warehouse (`stockCount`) onto
> the shelves of the store (`storeCount`) . How would NGSI v2 and NGSI-LD computations differ?

### Relationships without Linked Data

Without linked data, there is no machine readable way to connect entities together. Every data relationship must be
known in advanced somehow. Within an isolated Smart System this is not an issue, since the architect of the system will
know in advance _what-connects-to-what_.

For example in the simple NGSI v2 Entity Relationships tutorial, a convenience bridge table **InventoryItem** entity had
been created specifically to hold both count on the shelf and count in the warehouse in a single entity. In any
computation only the **InventoryItem** entity would be involved. The `stockCount` value would be decremented and the
`shelfCount` value would incremented. In the NGSI v2 model both the `storeCount` and the `shelfCount` have been placed
into the conceptual **InventoryItem** Entity. This is a necessary workaround for NGSI v2 and it allows for simpler data
reading and data manipulation. However technically it is ontologically incorrect, as there is no such thing as an
**InventoryItem** in the real world, it is really two separate ledgers, products bought for the store and products sold
on the shelf, which in turn have an indirect relationship.

Since the entity data is not yet machine readable externally, the programmer is free to design models as she sees fit
and can decide to update two attributes of one **InventoryItem** Entity or two separate attributes on two separate
**Shelf** and **StockOrder** entities without regards as to whether these really are real concrete items in the real
world. However this means **external systems** cannot discover information for themselves and must be pre-programmed to
know where information is held.

### Relationships with Linked Data

With a well defined data model using linked data, every relationship can be predefined in advance and is discoverable.
Using [JSON-LD](https://json-ld.org/spec/FCGS/json-ld/20130328) concepts (specifically `@graph` and `@context`) it is
much easier for computers to understand indirect relationships and navigate between linked entities. Due to hese
additional annotations it is possible to create usable models which are ontologically correct and therefore **Shelf**
can now be directly assigned a `numberOfItems` attribute and bridge table concept is no longer required. This is
necessary as other systems may be interogating **Shelf** directly.

Similarly a real **StockOrder** Entity can be created which holds a entry of which items are currently on order for each
store. This is a proper context data entity as `stockCount` describes the current state of a product in the warehouse.
Once again this describes a single, real world entity and is ontologically correct.

Unlike the NGSI v2 scenario, with linked data, it would be possible for an **external system** to discover relationships
and interogate our Supermarket. Imagine for example, an
[Autonomous Mobile Robot](https://www.intorobotics.com/40-excellent-autonomous-mobile-robots-on-wheels-that-you-can-build-at-home/)
system which is used to move a pallet of products onto a shelf it would be possible for this **external system** to
"know" about our supermarket by navigating the relationships in the linked data the `@graph` from **StockOrder** to
**Shelf** as shown:

-   Some `product:XXX` items have been removed from `stockOrder:0001` - decrement `stockCount`.
-   Interogating the **StockOrder** is discovered that the **Product** is `requestedFor` for a specific URI e.g.
    `store:002`

```json
  "@graph": [
   {
      "@id": "tutorial:orderedProduct",
      "@type": "https://uri.etsi.org/ngsi-ld/Relationship",
      "schema:domainIncludes": [{"@id": "tutorial:StockOrder"}],
      "schema:rangeIncludes": [{"@id": "tutorial:Product"}],
      "rdfs:comment": "The Product ordered for a store",
      "rdfs:label": "orderedProduct"
    },
    ...etc
]
```

-   It is also discovered from the **StockOrder** model that the `requestedFor` URI defines a **Building**

```json
  "@graph": [
    {
      "@id": "tutorial:requestedFor",
      "@type": "https://uri.etsi.org/ngsi-ld/Relationship",
      "schema:domainIncludes": [{"@id": "tutorial:StockOrder"}],
      "schema:rangeIncludes": [{"@id": "fiware:Building"}],
      "rdfs:comment": "Store for which an item is requested",
      "rdfs:label": "requestedFor"
    },
    ...etc
]
```

-   It is discovered from the **Building** model that every **Building** contains `furniture` as an array of URIs.
-   It is discovered from the **Building** model that these URIs represent **Shelf** units

```json
"@graph": [
    {
      "@id": "tutorial:furniture",
      "@type": "https://uri.etsi.org/ngsi-ld/Relationship",
      "schema:domainIncludes": [{"@id": "fiware:Building"}],
      "schema:rangeIncludes": [{"@id": "tutorial:Shelf"}],
      "rdfs:comment": "Units found within a Building",
      "rdfs:label": "furniture"
    },
    ...etc
]
```

-   It is discovered from the **Shelf** model that the `stocks` attribute holds a URI representing **Product** items.

```json
"@graph": [
    {
      "@id": "tutorial:stocks",
      "@type": "https://uri.etsi.org/ngsi-ld/Relationship",
      "schema:domainIncludes": [{"@id": "tutorial:Shelf"}],
      "schema:rangeIncludes": [{"@id": "tutorial:Product"}],
      "rdfs:comment": "The product found on a shelf",
      "rdfs:label": "stocks"
    },
    ...etc
]
```

-   A request the **Shelf** unit which holds the correct **Product** for the `stocks` attribute is made and the Shelf
    `numberOfItems` attribute can be incremented.

Through creating and using standard data models and decribing the linked data properly, it would not matter to the robot
if the underlying system were to change, provided that the Properties and Relationships resolve to fully qualified names
(FQN) and a complete `@graph`. For example the JSON short name attributes could be amended or the relationships
redesigned but their real intent (which resolves to a fixed FQN) could still be discovered and used.

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
[services](https://github.com/FIWARE/tutorials.Relationships-Linked-Data/blob/master/services) Bash script provided
within the repository. Please clone the repository and create the necessary images by running the commands as shown:

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

# Creating and Associating Data Entities

## Reviewing existing entities

On start up, the system is brought up with a series of **Building**, **Product** and **Shelf** entities already present.
You can query for them using the requests below. In each case only the _Properties_ of the entities have been created.

To avoid ambiguity, computers prefer to use unique IDs when referring to well defined concepts. For each of the NGSI-LD
entities returned, the names of the attributes received can be defined as either as a fully qualified name (FQN) or as
simple JSON attributes dependent upon whether the associated `Link` header connecting the NGSI-LD Data Entity to the
computer readable JSON-LD `@context` Data Models is included in the request.

### Display all Buildings

The Stores of the supermarket have been created using the FIWARE
[**Building** model](https://fiware-datamodels.readthedocs.io/en/latest/Building/Building/doc/spec/index.html) and the
enumerated value of this type is `fiware:Building` which expands to `https://uri.fiware.org/ns/datamodels%23Building`.
It is therefore possible to request all building entities without supplying a known context.

#### :one: Request:

```console
curl -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities?type=https://uri.fiware.org/ns/datamodels%23Building&options=keyValues'
```

#### Response:

The response returns all of the existing **Building** entities, with the attributes expanded as fully qualified names
(FQNs).

```json

```

According to the [defined data model](https://fiware.github.io/tutorials.Step-by-Step/schema/Store/):

-   The `type` attribute is an `https://uri.etsi.org/ngsi-ld/type`
-   The `name` attribute is an `https://uri.etsi.org/ngsi-ld/name`
-   The `location` attribute is an `https://uri.etsi.org/ngsi-ld/location`
-   The `address` attribute is an `http://schema.org/address`
-   The `category` attribute is an `https://uri.fiware.org/ns/datamodels#category`

`type`, `name` and `location` are defined in the NGSI-LD Core Context:
[`https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context.jsonld`](https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context.jsonld).
The other attributes are defined using the Tutorial's own Context:
[`https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld`](https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld).
Both `category` and `address` are _common_ attributes the defintions of which are brought in from the FIWARE data models
and `schema.org` respectively.

### Display all Products

Requesting the **Product** entities can be done by supplying the FQN of the entity `type` in the request as well.

#### :two: Request:

```console
curl -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities?type=https://fiware.github.io/tutorials.Step-by-Step/schema/Product&options=keyValues' \
  -H 'Link: <https://fiware.github.io/tutorials.Step-by-Step/datamodels-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

#### Response:

However since the full context has been supplied in the `Link` header, the short names are returned.

```json

```

According to the [defined data model](https://fiware.github.io/tutorials.Step-by-Step/schema/Product/):

-   The `type` attribute is an `https://uri.etsi.org/ngsi-ld/type`
-   The `name` attribute is an `https://uri.etsi.org/ngsi-ld/name`
-   The `price` attribute is an `https://fiware.github.io/tutorials.Step-by-Step/schema/price`
-   The `size` attribute is an `https://fiware.github.io/tutorials.Step-by-Step/schema/size`
-   The `currency` attribute is an `https://fiware.github.io/tutorials.Step-by-Step/schema/currency`

The programmatically the Product model is fully described in the
[`https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld`](https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld)

### Display all Shelves

Requesting the **Product** entities can be done by supplying the short of the entity `type` in the request as well,
provided the full context has been supplied in the `Link` header.

#### :three: Request:

```console
curl -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities?type=Shelf&options=keyValues' \
  -H 'Link: <https://fiware.github.io/tutorials.Step-by-Step/datamodels-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

#### Response:

Once again the short names are returned.

```json

```

According to the [defined data model](https://fiware.github.io/tutorials.Step-by-Step/schema/Shelf/):

-   The `type` attribute is an `https://uri.etsi.org/ngsi-ld/type`
-   The `name` attribute is an `https://uri.etsi.org/ngsi-ld/name`
-   The `location` attribute is an `https://uri.etsi.org/ngsi-ld/location`
-   The `maxCapacity` attribute is an `https://fiware.github.io/tutorials.Step-by-Step/schema/maxCapacity`
-   The `numberOfItems` attribute is an `https://fiware.github.io/tutorials.Step-by-Step/schema/numberOfItems`

The programmatically the Shelf model is fully described in the
[`https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld`](https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld)

### Obtain Shelf Information

#### :four: Request:

```console
curl -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:Shelf:unit001/?options=keyValues' \
  -H 'Link: <https://fiware.github.io/tutorials.Step-by-Step/datamodels-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

### Adding 1-1 Relationships

#### :five: Request:

```console
curl -X POST \
  http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:Shelf:unit001/attrs \
  -H 'Content-Type: application/ld+json' \
  -H 'fiware-servicepath: /' \
  -d '{
    "stocks": {
      "type": "Relationship",
        "object": "urn:ngsi-ld:Product:001"
    },
    "numberOfItems": {"type": "Property","value": 50},
    "locatedIn" : {
      "type": "Relationship", "object": "urn:ngsi-ld:Building:store001",
      "requestedBy": {
      "type": "Relationship",
      "object": "urn:ngsi-ld:Person:bob-the-manager"
    },
      "installedBy": {
      "type": "Relationship",
      "object": "urn:ngsi-ld:Person:employee001"
    },
    "statusOfWork": {
      "type": "Property",
      "value": "completed"
    }
    },
    "@context": [
    "https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld",
        "https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context.jsonld"
    ]
}'
```

### Obtain the Updated Shelf

#### :six: Request:

```console
curl -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:Shelf:unit001/?options=keyValues' \
  -H 'Link: <https://fiware.github.io/tutorials.Step-by-Step/datamodels-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```


### Find the store in which a specific shelf is located

#### :seven: Request:

```
curl -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:Shelf:unit001/?attrs=locatedIn&options=keyValues' \
  -H 'Link: <https://fiware.github.io/tutorials.Step-by-Step/datamodels-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

### Find the locations where shelves have been installed

#### :eight: Request:

```console
curl -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities/?type=Shelf&options=keyValues&attrs=locatedIn' \
  -H 'Accept: application/json' \
  -H 'Link: <https://fiware.github.io/tutorials.Step-by-Step/datamodels-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

### Adding a 1-many relationship

#### :nine: Request:

```console
curl -X POST \
  http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:Building:store001/attrs \
  -H 'Content-Type: application/ld+json' \
  -d '{
    "furniture": {
      "type": "Relationship",
        "object": [ "urn:ngsi-ld:Shelf:001", "urn:ngsi-ld:Shelf:002"]
    },
    "@context": [
    "https://fiware.github.io/tutorials.Step-by-Step/datamodels-context.jsonld"
    ]
}'
```

### Finding all shelf units found within a Store

#### :one::zero: Request:

```console
curl -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:Building:store001?options=keyValues&attrs=furniture' \
  -H 'Accept: application/json' \
  -H 'Link: <https://fiware.github.io/tutorials.Step-by-Step/datamodels-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```


### Creating Complex Relationships

#### :one::one: Request:

```console
curl -X POST \
  http://localhost:1026/ngsi-ld/v1/entities/ \
  -H 'Content-Type: application/ld+json' \
  -d '{
  "id": "urn:ngsi-ld:StockOrder:001",
  "type": "StockOrder",
  "requestedFor": {
    "type": "Relationship",
    "object": "urn:ngsi-ld:Building:store001"
  },
  "requestedBy": {
  "type": "Relationship",
  "object": "urn:ngsi-ld:Person:bob-the-manager"
  },
  "orderedProduct": {
    "type": "Relationship",
    "object": "urn:ngsi-ld:Product:001"
  },
  "stockCount": {
    "type": "Property",
    "value": 10000
  },
  "orderDate": {
    "type": "Property",
    "value": {
        "@type": "DateTime",
        "@value": "2018-08-07T12:00:00Z"
    }
  },
  "@context": [
    "https://fiware.github.io/tutorials.Step-by-Step/datamodels-context.jsonld"
  ]
}'
```

### Find all stores in which a product is sold

#### :one::two: Request:

```console
curl -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities/?type=StockOrder&q=orderedProduct==%22urn:ngsi-ld:Product:001%22&attrs=requestedFor&options=keyValues' \
  -H 'Accept: application/json' \
  -H 'Link: <https://fiware.github.io/tutorials.Step-by-Step/datamodels-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

### Find all products sold in a store

#### :one::three: Request:

```console
curl -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities/?type=StockOrder&q=requestedFor==%22urn:ngsi-ld:Building:store001%22&options=keyValues&attrs=orderedProduct' \
  -H 'Accept: application/json' \
  -H 'Link: <https://fiware.github.io/tutorials.Step-by-Step/datamodels-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

### Obtain Stock Order

#### :one::four: Request:

```console
curl -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:StockOrder:001?options=keyValues'
```

---

## License

[MIT](LICENSE) © 2019 FIWARE Foundation e.V.
