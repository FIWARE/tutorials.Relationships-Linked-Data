<h1  align="center">
    <img src="https://fiware.github.io/tutorials.Step-by-Step/img/fiware-supermarket.png" />
    <img src="https://img.shields.io/badge/NGSI-LD-d6604d.svg" width="90"/>
    <br/>
    🍇 🍉 🍊 🍎 🍐 🍌 🍍 🍏 🍐 🍑 🍒 🍓 🫐
</h1>

## Entity Relationships (NGSI-LD)

[![FIWARE Core Context Management](https://nexus.lab.fiware.org/repository/raw/public/badges/chapters/core.svg)](https://github.com/FIWARE/catalogue/blob/master/core/README.md)
[![License: MIT](https://img.shields.io/github/license/fiware/tutorials.Relationships-Linked-Data.svg)](https://opensource.org/licenses/MIT)
[![Support badge](https://img.shields.io/badge/tag-fiware-orange.svg?logo=stackoverflow)](https://stackoverflow.com/questions/tagged/fiware)
<br/> [![Documentation](https://img.shields.io/readthedocs/fiware-tutorials.svg)](https://fiware-tutorials.rtfd.io)

This tutorial discusses relationships between linked data entities and how the concepts of **JSON-LD** and **NGSI-LD**
can be used to interrogate entities and navigate from one entity to another. The tutorial discusses a series of simple
linked-data data models based around the supermarket chain’s store finder application, and demonstrates how to design
models holding one-to-one, one-to-many and many-to-many relationships. This **NGSI-LD** tutorial is a direct analogue to
the earlier _Understanding Entities and Relationships_ tutorial (which was based on the **NGSI v2** interface). The
differences in relationships created using **NGSI v2** and **NGSI-LD** are highlighted and discussed in detail.

The tutorial uses [cUrl](https://ec.haxx.se/) commands throughout, but is also available as
[Postman documentation](https://fiware.github.io/tutorials.Relationships-Linked-Data/)

[![Run in Postman](https://run.pstmn.io/button.svg)](https://app.getpostman.com/run-collection/7ae2d2d3f42bbdf59c45)
[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/FIWARE/tutorials.Relationshps-Linked-Data/tree/NGSI-v2)

-   このチュートリアルは[日本語](README.ja.md)でもご覧いただけます。

> [!NOTE]
>
> This tutorial is designed for **NGSI-v2** developers looking to switch or upgrade systems to **NGSI-LD**, if you are
> building a linked data system from scratch or you are not already familiar with **NGSI-v2** then it is recommmended
> that you look directly at the [NGSI-LD developers tutorial](https://ngsi-ld-tutorials.readthedocs.io/) documentation.

## Contents

<details>
<summary><strong>Details</strong></summary>

-   [Relationships in Linked Data](#relationships-in-linked-data)
    -   [Designing Data Models using JSON-LD](#designing-data-models-using-json-ld)
        -   [Revision: Data Models for a Stock management system as defined using NGSI-v2](#revision-data-models-for-a-stock-management-system-as-defined-using-ngsi-v2)
        -   [Data Models for a Stock management system defined using NGSI-LD](#data-models-for-a-stock-management-system-defined-using-ngsi-ld)
    -   [Comparison between Linked and Non-Linked Data Systems](#comparison-between-linked-and-non-linked-data-systems)
        -   [:arrow_forward: Video: Rich Snippets: Product Search](#arrow_forward-video-rich-snippets-product-search)
    -   [Traversing relationships](#traversing-relationships)
        -   [Relationships without Linked Data](#relationships-without-linked-data)
        -   [Relationships with Linked Data](#relationships-with-linked-data)
-   [Prerequisites](#prerequisites)
    -   [Docker](#docker)
    -   [WSL](#wsl)
-   [Architecture](#architecture)
-   [Start Up](#start-up)
-   [Creating and Associating Data Entities](#creating-and-associating-data-entities)
    -   [Reviewing existing entities](#reviewing-existing-entities)
        -   [Display all Buildings](#display-all-buildings)
        -   [Display all Products](#display-all-products)
        -   [Display all Shelves](#display-all-shelves)
        -   [Obtain Shelf Information](#obtain-shelf-information)
    -   [Creating Relationships](#creating-relationships)
        -   [Adding 1-1 Relationships](#adding-1-1-relationships)
        -   [Obtain the Updated Shelf](#obtain-the-updated-shelf)
        -   [How is the relationship's Fully Qualified Name created ?](#how-is-the-relationships-fully-qualified-name-created-)
        -   [:arrow_forward: Video: JSON-LD Compaction & Expansion](#arrow_forward-video-json-ld-compaction--expansion)
        -   [What other relationship information can be obtained from the data model?](#what-other-relationship-information-can-be-obtained-from-the-data-model)
        -   [Find the store in which a specific shelf is located](#find-the-store-in-which-a-specific-shelf-is-located)
        -   [Find the IDs of all Shelf Units in a Store](#find-the-ids-of-all-shelf-units-in-a-store)
        -   [Adding a 1-many relationship](#adding-a-1-many-relationship)
        -   [Finding all shelf units found within a Store](#finding-all-shelf-units-found-within-a-store)
        -   [Creating Complex Relationships](#creating-complex-relationships)
        -   [Find all stores in which a product is sold](#find-all-stores-in-which-a-product-is-sold)
        -   [Find all products sold in a store](#find-all-products-sold-in-a-store)
        -   [Obtain Stock Order](#obtain-stock-order)

</details>

# Relationships in Linked Data

> “It’s hard to communicate anything exactly and that’s why perfect relationships between people are difficult to find.”
>
> ― Gustave Flaubert, L'Éducation sentimentale

All NGSI data entity attributes can be divided into one of two types.

-   _Property_ attributes
-   _Relationship_ attributes

For each entity, the _Property_ attributes (including various subtypes such as _GeoProperty_ , _TemporalProperty_ and
time values) define the current state of something in the real world. As the state of the entity changes the `value` of
each _Property_ is updated to align with the last real world reading of the attribute. All _Property_ attributes relate
to the state of a single entity.

_Relationship_ attributes correspond to the associations **between** entities (which might change over time). They
effectively provide the graph linking the nodes of the data entities together. Each _Relationship_ attribute holds an
`object` in the form of a URN - effectively a pointer to another object. _Relationship_ attributes do not hold data
themselves.

Both properties and relationships may in turn have a linked embedded structure (of _properties-of-properties_ or
_properties-of-relationships or relationships-of-properties_ or _relationships-of-relationships_ etc.) which lead a full
complex knowledge graph.

## Designing Data Models using JSON-LD

In order for computers to be able to navigate linked data structures, proper ontologically correct data models must be
created and a full `@context` must be defined and made accessible. We can do this by reviewing and updating the existing
data models from the NGSI v2 [Entity Relationships](https://github.com/FIWARE/tutorials.Entity-Relationships) tutorial.

### Revision: Data Models for a Stock management system as defined using NGSI v2

As a reminder, four types of entity were created in the NGSI v2 stock management system. The relationship between the
four NGSI v2 entity models was defined as shown below:

![](https://fiware.github.io/tutorials.Relationships-Linked-Data/img/entities-v2.png)

More details can be found in the NGSI v2
[Entity Relationships](https://github.com/FIWARE/tutorials.Entity-Relationships) tutorial.

In NGSI v2 relationship attributes are just standard attributes. By convention NGSI v2 relationship attributes are given
names starting `ref` and are defined using the `type="Relationship"`. However, this is merely convention and may not be
followed in all cases. There is no infallible mechanism for detecting which attributes are associative relationships
between entities.

### Data Models for a Stock management system defined using NGSI-LD

The richer [JSON-LD](https://json-ld.org/spec/FCGS/json-ld/20130328) description language is able to define NGSI-LD
entities by linking entities directly as shown below.

![](https://fiware.github.io/tutorials.Relationships-Linked-Data/img/entities-ld.png)

The complete data model must be understandable by both developers and machines.

-   A full Human readable definition of this data model can be found
    [online](https://fiware.github.io/tutorials.Step-by-Step/schema).
-   The machine readable JSON-LD definition can be found at
    [`https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld`](https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld) -
    this file will be used to provide the `@context` to power our NGSI-LD data entities.

Four data models have been created for this NGSI-LD stock management system. The relationships between the models are
described below:

-   The [**Store** model](https://fiware.github.io/tutorials.Step-by-Step/schema/Store/) is now based on and extends the
    FIWARE [**Building** model](https://github.com/smart-data-models/dataModel.Building). This ensures that it offers
    standard properties for `name`, `address` and `category`.
    -   A Building will hold `furniture` this is a 1-many relationship.
        -   Building :arrow_right: Shelf.
-   The [**Shelf** model](https://fiware.github.io/tutorials.Step-by-Step/schema/Shelf/) is a custom data model defined
    for the tutorial
    -   Each **Shelf** is `locatedIn` a **Building**. This is a 1-1 relationship. It is the reciprocal relationship to
        `furniture` defined above.
        -   Shelf :arrow_right: Building.
    -   A **Shelf** is `installedBy` a **Person** - this is a 1-1 relationship. A shelf knows who installed it, but it
        is this knowledge is not part of the Person entity itself.
        -   Shelf :arrow_right: Person
    -   A **Shelf** `stocks` a given **Product**. This is another 1-1 relationship, and again it is not reciprocated. A
        **Product** does not know which **Shelf** it is to be found on.
        -   Shelf :arrow_right: Product
-   A [**StockOrder** model](https://fiware.github.io/tutorials.Step-by-Step/schema/StockOrder/) replaces the
    **Inventory Item** bridge table defined for NGSI v2 :
    -   A **StockOrder** is `requestedBy` a **Person** - this is a 1-1 relationship.
        -   StockOrder :arrow_right: Person.
    -   A **StockOrder** is `requestedFor` a **Building** - this is a 1-1 relationship.
        -   StockOrder :arrow_right: Building.
    -   A **StockOrder** is a request for a specific `orderedProduct` - this is a 1-1 relationship.
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

A simple example of an external system interrogating for structured data can be found in online product search. Machines
from third parties such as Google are able to read product information (encoded using a standard
[**Product** data model](https://jsonld.com/product/)) and display a rich snippet of product information with a standard
star rating.

[![](https://fiware.github.io/tutorials.Step-by-Step/img/video-logo.png)](https://www.youtube.com/watch?v=_-rRxKSm2ic 'Rich Snippets')

Click on the image above to watch an introductory video on rich snippets for product search.

Further machine readable data model examples can be found on the [Steal Our JSON-LD](https://jsonld.com/) site.

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
necessary as other systems may be interrogating **Shelf** directly.

Similarly a real **StockOrder** Entity can be created which holds a entry of which items are currently on order for each
store. This is a proper context data entity as `stockCount` describes the current state of a product in the warehouse.
Once again this describes a single, real world entity and is ontologically correct.

Unlike the NGSI v2 scenario, with linked data, it would be possible for an **external system** to discover relationships
and interrogate our Supermarket. Imagine for example, an
[Autonomous Mobile Robot](https://www.intorobotics.com/40-excellent-autonomous-mobile-robots-on-wheels-that-you-can-build-at-home/)
system which is used to move a pallet of products onto a shelf it would be possible for this **external system** to
"know" about our supermarket by navigating the relationships in the linked data the `@graph` from **StockOrder** to
**Shelf** as shown:

-   Some `product:XXX` items have been removed from `stockOrder:0001` - decrement `stockCount`.
-   Interogating the **StockOrder** is discovered that the **Product** is `requestedFor` for a specific URI e.g.
    `urn:ngsi-ld:Building:store002`

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

Through creating and using standard data models and describing the linked data properly, it would not matter to the
robot if the underlying system were to change, provided that the Properties and Relationships resolve to fully qualified
names (FQNs) and a complete `@graph`. For example the JSON short name attributes could be amended or the relationships
redesigned but their real intent (which resolves to a fixed FQN) could still be discovered and used.

# Prerequisites

## Docker

To keep things simple all components will be run using [Docker](https://www.docker.com). **Docker** is a container
technology which allows to different components isolated into their respective environments.

-   To install Docker on Windows follow the instructions [here](https://docs.docker.com/docker-for-windows/)
-   To install Docker on Mac follow the instructions [here](https://docs.docker.com/docker-for-mac/)
-   To install Docker on Linux follow the instructions [here](https://docs.docker.com/install/)

**Docker Compose** is a tool for defining and running multi-container Docker applications. A
[YAML file](https://raw.githubusercontent.com/fiware/tutorials.Relationships-Linked-Data/master/docker-compose/orion-ld.yml)
is used configure the required services for the application. This means all container services can be brought up in a
single command. Docker Compose is installed by default as part of Docker for Windows and Docker for Mac, however Linux
users will need to follow the instructions found [here](https://docs.docker.com/compose/install/)

## WSL

We will start up our services using a simple bash script. Windows users should download the
[Windows Subsystem for Linux](https://learn.microsoft.com/en-us/windows/wsl/install) to provide a command-line
functionality similar to a Linux distribution on Windows.

# Architecture

The demo application will send and receive NGSI-LD calls to a compliant context broker. Since both NGSI v2 and NGSI-LD
interfaces are available to an experimental version fo the
[Orion Context Broker](https://fiware-orion.readthedocs.io/en/latest/), our demo application will only make use of one
FIWARE component.

Currently, the Orion Context Broker relies on open source [MongoDB](https://www.mongodb.com/) technology to keep
persistence of the context data it holds. Therefore, the architecture will consist of two elements:

-   The [Orion Context Broker](https://fiware-orion.readthedocs.io/en/latest/) which will receive requests using
    [NGSI-LD](https://forge.etsi.org/swagger/ui/?url=https://forge.etsi.org/rep/NGSI-LD/NGSI-LD/raw/master/spec/updated/generated/full_api.json)
-   The underlying [MongoDB](https://www.mongodb.com/) database :
    -   Used by the Orion Context Broker to hold context data information such as data entities, subscriptions and
        registrations

Since all interactions between the two elements are initiated by HTTP requests, the elements can be containerized and
run from exposed ports.

![](https://fiware.github.io/tutorials.Relationships-Linked-Data/img/architecture.png)

The necessary configuration information can be seen in the services section of the associated `orion-ld.yml` file:

```yaml
orion:
    image: quay.io/fiware/orion-ld
    hostname: orion
    container_name: fiware-orion
    depends_on:
        - mongo-db
    networks:
        - default
    ports:
        - '1026:1026'
    command: -dbhost mongo-db -logLevel DEBUG
    healthcheck:
        test: curl --fail -s http://orion:1026/version || exit 1
```

```yaml
mongo-db:
    image: mongo:4.2
    hostname: mongo-db
    container_name: db-mongo
    expose:
        - '27017'
    ports:
        - '27017:27017'
    networks:
        - default
    command: --nojournal
```

Both containers are residing on the same network - the Orion Context Broker is listening on Port `1026` and MongoDB is
listening on the default port `27017`. Both containers are also exposing the same ports externally - this is purely for
the tutorial access - so that cUrl or Postman can access them without being part of the same network. The command-line
initialization should be self explanatory.

The only notable difference to the introductory tutorials is that the required image name is currently
`fiware/orion-ld`.

# Start Up

All services can be initialised from the command-line by running the
[services](https://github.com/FIWARE/tutorials.Relationships-Linked-Data/blob/NGSI-v2/services) Bash script provided
within the repository. Please clone the repository and create the necessary images by running the commands as shown:

```bash
git clone https://github.com/FIWARE/tutorials.Relationships-Linked-Data.git
cd tutorials.Relationships-Linked-Data
git checkout NGSI-v2

./services orion|scorpio|stellio
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
[**Building** model](https://github.com/smart-data-models/dataModel.Building) and the enumerated value of this type is
`fiware:Building` which expands to `https://uri.fiware.org/ns/data-models%23Building`. It is therefore possible to
request all building entities without supplying a known context.

#### 1️⃣ Request:

```console
curl -G -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities' \
  -H 'Accept: application/ld+json' \
  -d 'type=https%3A%2F%2Furi.fiware.org%2Fns%2Fdata-models%23Building' \
  -d 'options=keyValues'
```

#### Response:

The response returns all of the existing **Building** entities, with the attributes expanded as fully qualified names
(FQNs).

```json
[
    {
        "@context": "https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context-v1.6.jsonld",
        "id": "urn:ngsi-ld:Building:store001",
        "type": "https://uri.fiware.org/ns/data-models#Building",
        "https://schema.org/address": {
            "https://schema.org/streetAddress": "Bornholmer Straße 65",
            "https://schema.org/addressRegion": "Berlin",
            "https://schema.org/addressLocality": "Prenzlauer Berg",
            "https://schema.org/postalCode": "10439"
        },
        "https://schema.org/name": "Bösebrücke Einkauf",
        "https://uri.fiware.org/ns/data-models#category": "https://uri.fiware.org/ns/data-models#commercial",
        "location": {
            "type": "Point",
            "coordinates": [13.3986, 52.5547]
        }
    },
    {
        "@context": "https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context-v1.6.jsonld",
        "id": "urn:ngsi-ld:Building:store002",
        "type": "https://uri.fiware.org/ns/data-models#Building",
        "https://schema.org/address": {
            "https://schema.org/streetAddress": "Friedrichstraße 44",
            "https://schema.org/addressRegion": "Berlin",
            "https://schema.org/addressLocality": "Kreuzberg",
            "https://schema.org/postalCode": "10969"
        },
        "https://schema.org/name": "Checkpoint Markt",
        "https://uri.fiware.org/ns/data-models#category": "https://uri.fiware.org/ns/data-models#commercial",
        "location": {
            "type": "Point",
            "coordinates": [13.3903, 52.5075]
        }
    },
    ...etc
]
```

According to the [defined data model](https://fiware.github.io/tutorials.Step-by-Step/schema/Store/):

-   The `type` attribute has the FQN `https://uri.etsi.org/ngsi-ld/type`
-   The `name` attribute has the FQN `https://uri.etsi.org/ngsi-ld/name`
-   The `location` attribute has the FQN `https://uri.etsi.org/ngsi-ld/location`
-   The `address` attribute has the FQN `http://schema.org/address`
-   The `category` attribute has the FQN `https://uri.fiware.org/ns/data-models#category`

`type` and `location` are defined in the NGSI-LD Core Context:
[`https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context-v1.6.jsonld`](https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context-v1.6.jsonld).
The other attributes are defined using the Tutorial's own Context:
[`http://context/user-context.jsonld`](./data-models/user-context.jsonld). Both `category` and `address` are _common_
attributes the definitions of which are brought in from the FIWARE data models and `schema.org` respectively.

### Display all Products

Requesting the **Product** entities can be done by supplying the FQN of the entity `type` in the request as well.

#### 2️⃣ Request:

```console
curl -G -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities' \
  -d 'type=https://fiware.github.io/tutorials.Step-by-Step/schema/Product' \
  -d 'options=keyValues' \
  -H 'Link: <http://context/user-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

#### Response:

However since the full context has been supplied in the `Link` header, the short names are returned.

```json
[
    {
        "@context": "http://context/user-context.jsonld",
        "id": "urn:ngsi-ld:Product:001",
        "type": "Product",
        "price": 0.99,
        "size": "S",
        "name": "Apples"
    },
    {
        "@context": "http://context/user-context.jsonld",
        "id": "urn:ngsi-ld:Product:002",
        "type": "Product",
        "price": 10.99,
        "size": "M",
        "name": "Bananas"
    },
    ...etc
]
```

According to the [defined data model](https://fiware.github.io/tutorials.Step-by-Step/schema/Product/):

-   The `type` attribute has the FQN `https://uri.etsi.org/ngsi-ld/type`
-   The `name` attribute has the FQN `https://uri.etsi.org/ngsi-ld/name`
-   The `price` attribute has the FQN `https://fiware.github.io/tutorials.Step-by-Step/schema/price`
-   The `size` attribute has the FQN `https://fiware.github.io/tutorials.Step-by-Step/schema/size`
-   The `currency` attribute has the FQN `https://fiware.github.io/tutorials.Step-by-Step/schema/currency`

The programmatically the Product model and its attributes are fully described in the
[`http://context/user-context-with-graph.jsonld`](./data-models/user-context-with-graph.jsonld)

### Display all Shelves

Requesting the **Shelf** entities can be done by supplying the short of the entity `type` in the request as well,
provided the full context has been supplied in the `Link` header.

#### 3️⃣ Request:

```console
curl -G -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities' \
  -d 'type=Shelf' \
  -d 'options=keyValues' \
  -H 'Link: <http://context/user-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

#### Response:

Once again the short names are returned.

```json
[
    {
        "@context": "http://context/user-context.jsonld",
        "id": "urn:ngsi-ld:Shelf:unit001",
        "type": "Shelf",
        "maxCapacity": 50,
        "name": "Corner Unit",
        "location": {
            "type": "Point",
            "coordinates": [13.398611, 52.554699]
        }
    },
    {
        "@context": "http://context/user-context.jsonld",
        "id": "urn:ngsi-ld:Shelf:unit002",
        "type": "Shelf",
        "maxCapacity": 100,
        "name": "Wall Unit 1",
        "location": {
            "type": "Point",
            "coordinates": [13.398722, 52.554664]
        }
    },
    ...etc
]
```

According to the [defined data model](https://fiware.github.io/tutorials.Step-by-Step/schema/Shelf/):

-   The `type` attribute has the FQN `https://uri.etsi.org/ngsi-ld/type`
-   The `name` attribute has the FQN `https://schema.org/name`
-   The `location` attribute has the FQN `https://uri.etsi.org/ngsi-ld/location`
-   The `maxCapacity` attribute has the FQN `https://fiware.github.io/tutorials.Step-by-Step/schema/maxCapacity`
-   The `numberOfItems` attribute has the FQN `https://fiware.github.io/tutorials.Step-by-Step/schema/numberOfItems`

The programmatically the Shelf model and its attributes are fully described in the
[`http://context/user-context-with-graph.jsonld`](./data-models/user-context-with-graph.jsonld)

### Obtain Shelf Information

Initially each shelf is created with `name`, `maxCapacity` and `location` _Properties_ only. A sample shelf is requested
below.

#### 4️⃣ Request:

```console
curl -G -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:Shelf:unit001/' \
  -d 'options=keyValues' \
  -H 'Link: <http://context/user-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

#### Response:

The short names have been returned since the `@context` has been supplied in the `Link` header.

```json
{
    "@context": "http://context/user-context.jsonld",
    "id": "urn:ngsi-ld:Shelf:unit001",
    "type": "Shelf",
    "maxCapacity": 50,
    "name": "Corner Unit",
    "location": {
        "type": "Point",
        "coordinates": [13.398611, 52.554699]
    }
}
```

## Creating Relationships

To complete the data model within the data model, various additional _Properties_ and _Relationships_ need to be added
to the entity.

A **Shelf** holds a `numberOfItems` - this is a `Property` of the **Shelf** and contains a `value` representing the
number of Items. The `value` of this _Property_ (i.e. the number of Items will change over time). _Properties_ have been
covered in a [previous tutorial](https://github.com/FIWARE/tutorials.Linked-Data) and will not be covered in detail
here.

A **Shelf** `stocks` a given **Product** - this is a `Relationship` of the **Shelf** Only the URN of the product is
known by the **Shelf** entity - effectively it points to further information held elsewhere.

To distinguish _Relationships_, they must be given `type="Relationship"` and each _Relationship_ has must have an
`object` sub-attribute, this contrasts with _Properties_ which must a `type="Property"` have a `value` attribute. The
`object` sub-attribute holds the reference to the related entity in the form of a URN.

A **Shelf** is `locatedIn` a given **Building**. Once again this is a `Relationship` of the **Shelf**. The URN of the
**Building** is known by the **Shelf** entity, but further information is also available:

-   `locatedIn[requestedBy]` is a _Relationship-of-a-Relationship_, this sub-attribute in turn holds an `object`
    attribute of its own pointing to a **Person**
-   `locatedIn[installedBy]` is a _Relationship-of-a-Relationship_, this sub-attribute in turn holds an `object`
    attribute of its own pointing to a **Person**
-   `locatedIn[statusOfWork]` is a _Property-of-a-Relationship_, this sub-attribute in turn holds an `value` attribute
    holding the current status of the `locatedIn` action.

As you can see, it is possible to embed further _Properties_ (with a corresponding `value`) or _Relationships_ (with a
corresponding `object`) inside the entity structure to provide a rich graph of information

### Adding 1-1 Relationships

Within the `@context` a **Shelf** has been predefined with two relationships. (`stocks` and `locatedIn`)

To create a relationship add a new attribute with `type=Relationship` and an associated object attribute. Metadata about
the relationships (e.g. `requestedBy`, `installedBy`)can be created by adding subattributes to the relationship. The
value of object is the URN corresponding to the linked data entity.

Note that the relationship is currently unidirectional. **Shelf** :arrow_right: **Building**.

#### 5️⃣ Request:

```console
curl -X POST \
  'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:Shelf:unit001/attrs' \
  -H 'Content-Type: application/ld+json' \
  -H 'fiware-servicepath: /' \
  -d '{
    "numberOfItems": {"type": "Property","value": 50},
    "stocks": {
      "type": "Relationship",
      "object": "urn:ngsi-ld:Product:001"
    },
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
    "@context": "http://context/user-context.jsonld"
}'
```

### Obtain the Updated Shelf

Having added the additional attributes, it is possible to query for the amended entity.

This example returns the context data of the Shelf entity with the `id=urn:ngsi-ld:Shelf:unit001`.

#### 6️⃣ Request:

```console
curl -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:Shelf:unit001'
```

#### Response:

There are now two additional relationship attributes present `stocks` and `locatedIn`. Both entries have been expanded
as fully qualified names (FQNs), as defined in the
[**Shelf** Data Model](https://fiware.github.io/tutorials.Step-by-Step/schema/Shelf/) as the `Link` header was not
passed in the previous request.

```json
{
    "@context": "https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context-v1.6.jsonld",
    "id": "urn:ngsi-ld:Shelf:unit001",
    "type": "https://fiware.github.io/tutorials.Step-by-Step/schema/Shelf",
    "https://fiware.github.io/tutorials.Step-by-Step/schema/locatedIn": {
        "type": "Relationship",
        "object": "urn:ngsi-ld:Building:store001",
        "https://fiware.github.io/tutorials.Step-by-Step/schema/installedBy": {
            "type": "Relationship",
            "object": "urn:ngsi-ld:Person:employee001"
        },
        "https://fiware.github.io/tutorials.Step-by-Step/schema/requestedBy": {
            "type": "Relationship",
            "object": "urn:ngsi-ld:Person:bob-the-manager"
        },
        "https://fiware.github.io/tutorials.Step-by-Step/schema/statusOfWork": {
            "type": "Property",
            "value": "https://fiware.github.io/tutorials.Step-by-Step/schema/completed"
        }
    },
    "https://fiware.github.io/tutorials.Step-by-Step/schema/maxCapacity": {
        "type": "Property",
        "value": 50
    },
    "https://fiware.github.io/tutorials.Step-by-Step/schema/numberOfItems": {
        "type": "Property",
        "value": 50
    },
    "https://fiware.github.io/tutorials.Step-by-Step/schema/stocks": {
        "type": "Relationship",
        "object": "urn:ngsi-ld:Product:001"
    },
    "name": {
        "type": "Property",
        "value": "Corner Unit"
    },
    "location": {
        "type": "GeoProperty",
        "value": {
            "type": "Point",
            "coordinates": [13.398611, 52.554699]
        }
    }
}
```

For example, this means that `https://fiware.github.io/tutorials.Step-by-Step/schema/locatedIn` is a well-defined
relationship within our linked data JSON-LD schema.

### How is the relationship's Fully Qualified Name created ?

One of the central motivations of JSON-LD is making it easy to translate between different representations of what are
fundamentally the same data types. In this case, the short hand `locatedIn` refers to the unique and computer readable
`https://fiware.github.io/tutorials.Step-by-Step/schema/locatedIn`

To do this NGSI-LD uses the two core expansion and compaction algorithms of the underlying JSON-LD model.

Looking at the relevant lines in the JSON-LD `@context`:

```json
    "tutorial": "https://fiware.github.io/tutorials.Step-by-Step/schema/",

    "Shelf": "tutorial:Shelf",

    "locatedIn": {
      "@id": "tutorial:locatedIn",
      "@type": "@id"
    },
```

You can see that `tutorial` has been mapped to the string `https://fiware.github.io/tutorials.Step-by-Step/schema/` and
`locatedIn` has been mapped to `tutorial:locatedIn` which using

Furthermore, `locatedIn` has an `@type="@id"` which indicates to a computer that its underlying value is a URN.

### :arrow_forward: Video: JSON-LD Compaction & Expansion

[![](https://fiware.github.io/tutorials.Step-by-Step/img/video-logo.png)](https://www.youtube.com/watch?v=Tm3fD89dqRE 'JSON-LD Compaction & Expansion')

Click on the image above to watch a video JSON-LD expansion and compaction with reference to the `@context`.

### What other relationship information can be obtained from the data model?

More information about `Relationships` can be obtained from the `@graph` of the linked data model had it been supplied.
For `locatedIn` the relevant section definition is as follows:

```json
    {
      "@id": "tutorial:locatedIn",
      "@type": "https://uri.etsi.org/ngsi-ld/Relationship",
      "schema:domainIncludes": [{"@id": "tutorial:Shelf"}],
      "schema:rangeIncludes": [{"@id": "fiware:Building"}],
      "rdfs:comment": "Building in which an item is found",
      "rdfs:label": "located In"
    },
```

This indicates a lot of additional information about the `locatedIn` _Relationship_ in a computer readable fashion:

-   `locatedIn` is really an NGSI-LD relationship (i.e. it has the FQN `https://uri.etsi.org/ngsi-ld/Relationship`)
-   `locatedIn` is only used on **Shelf** entities
-   `locatedIn` only points to **Building** entities
-   `locatedIn` can be defined for humans as _"Building in which an item is found"_
-   `locatedIn` can be labelled as _"located In"_ when labelling the _Relationship_.

Through reading the NGSI-LD data entity and its associated data model, a computer can obtain as much information as a
human can from reading the human-readable equivalent data specification:

![](https://fiware.github.io/tutorials.Relationships-Linked-Data/img/shelf-specification.png)

### Find the store in which a specific shelf is located

This example returns the `locatedIn` value associated with a given `Shelf` unit.

If the `id` and `type` of a data entity are known, a specific field can be requested by using the `attrs` parameter.

#### 7️⃣ Request:

```console
curl -G -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:Shelf:unit001/' \
  -d 'attrs=locatedIn' \
  -d 'options=keyValues' \
  -H 'Link: <http://context/user-context-with-graph.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

#### Response:

```json
{
    "@context": "http://context/user-context-with-graph.jsonld",
    "id": "urn:ngsi-ld:Shelf:unit001",
    "type": "Shelf",
    "locatedIn": "urn:ngsi-ld:Building:store001"
}
```

### Find the IDs of all Shelf Units in a Store

This example returns the `locatedIn` URNs of all **Shelf** entities found within `urn:ngsi-ld:Building:store001`. This
is purely an instance of using the `q` parameter to filter on attribute value

#### 8️⃣ Request:

```console
curl -G -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities/' \
  -d 'type=Shelf' \
  -d 'options=keyValues' \
  -d 'attrs=locatedIn' \
  -H 'Accept: application/json' \
  -H 'Link: <http://context/user-context-with-graph.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

#### Response:

The response contains an array displaying

```json
[
    {
        "id": "urn:ngsi-ld:Shelf:unit001",
        "type": "Shelf",
        "locatedIn": "urn:ngsi-ld:Building:store001"
    }
]
```

### Adding a 1-many relationship

To add a 1-many relationship, add an array of Relationship items as the attribute. This can be used for simple links
without additional data. This method is used to add **Shelf** entities as `furniture` in the **Store**.

This is the reciprocal relationship to the `locatedIn` attribute on **Shelf**

#### 9️⃣ Request:

```console
curl -L -X POST 'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:Building:store001/attrs' \
-H 'Content-Type: application/ld+json' \
--data-raw '{
    "furniture": {
        "type": "Relationship",
        "object":  ["urn:ngsi-ld:Shelf:001", "urn:ngsi-ld:Shelf:002"]
    },
    "@context": "http://context/user-context-with-graph.jsonld"
}'
```

### Finding all shelf units found within a Store

To find all the `furniture` within a **Building**, simply make a request to retrieve the `furniture` attribute.

Because the reicprocal relationship already exists, Additional information can be obtained from the **Shelf** entities
themselves.

#### 1️⃣0️⃣ Request:

```console
curl -G -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:Building:store001' \
  -d 'options=keyValues' \
  -d 'attrs=furniture' \
  -H 'Accept: application/json' \
  -H 'Link: <http://context/user-context-with-graph.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

#### Response:

```json
{
    "id": "urn:ngsi-ld:Building:store001",
    "type": "Building",
    "furniture": ["urn:ngsi-ld:Shelf:001", "urn:ngsi-ld:Shelf:002"]
}
```

### Creating Complex Relationships

To create a more complex relationship, and additional data entity must be created which holds the current state of the
links between real world items. In the case of the NGSI-LD data model we have already created, a **StockOrder** can be
used to link **Product**, **Building** and **Person** entities and the state of the relationships between them. As well
as _Relationship_ attributes, a **StockOrder** can hold _Property_ attributes (such as the `stockCount`) and other more
complex metadata such as _Properties-of-Properties_ or _Properties-of-Relationships_

The **StockOrder** is created as a standard NGSI-LD data entity.

#### 1️⃣1️⃣ Request:

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
  "@context": "http://context/user-context-with-graph.jsonld"
}'
```

### Find all stores in which a product is sold

Since _Relationship_ attributes are just like any other attribute, standard `q` parameter queries can be made on the
**StockOrder** to obtain which entity relates to it. For example the query below returns an array of stores in which a
given product is sold.

The query `q==orderedProduct="urn:ngsi-ld:Product:001"` is used to filter the entities.

#### 1️⃣2️⃣ Request:

```console
curl -G -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities/' \
  -d 'type=StockOrder' \
  -d 'q=orderedProduct==%22urn:ngsi-ld:Product:001%22' \
  -d 'attrs=requestedFor' \
  -d 'options=keyValues' \
  -H 'Accept: application/json' \
  -H 'Link: <http://context/user-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

#### Response:

The response returns an array of `requestedFor` attributes in the response.

```json
[
    {
        "id": "urn:ngsi-ld:StockOrder:001",
        "type": "StockOrder",
        "requestedFor": "urn:ngsi-ld:Building:store001"
    }
]
```

### Find all products sold in a store

The query below returns an array of stores in which a given product is sold.

The query `q==requestedFor="urn:ngsi-ld:Building:store001"` is used to filter the entities.

#### 1️⃣3️⃣ Request:

```console
curl -G -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities/' \
  -d 'type=StockOrder' \
  -d 'q=requestedFor==%22urn:ngsi-ld:Building:store001%22' \
  -d 'options=keyValues' \
  -d 'attrs=orderedProduct' \
  -H 'Accept: application/json' \
  -H 'Link: <http://context/user-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

#### Response:

The response returns an array of `orderedProduct` attributes in the response. This is the reciprocal of the previous
request.

```json
[
    {
        "id": "urn:ngsi-ld:StockOrder:001",
        "type": "StockOrder",
        "orderedProduct": "urn:ngsi-ld:Product:001"
    }
]
```

### Obtain Stock Order

A complete stock order can be obtained by making a standard GET request to the `/ngsi-ld/v1/entities/` endpoint and
adding the appropriate URN.

#### 1️⃣4️⃣ Request:

```console
curl -G -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:StockOrder:001' \
  -d 'options=keyValues'
```

#### Response:

The response returns the fully expanded entity.

```json
{
    "@context": "https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context-v1.6.jsonld",
    "id": "urn:ngsi-ld:StockOrder:001",
    "type": "https://fiware.github.io/tutorials.Step-by-Step/schema/StockOrder",
    "https://fiware.github.io/tutorials.Step-by-Step/schema/orderDate": {
        "@type": "DateTime",
        "@value": "2018-08-07T12:00:00Z"
    },
    "https://fiware.github.io/tutorials.Step-by-Step/schema/orderedProduct": "urn:ngsi-ld:Product:001",
    "https://fiware.github.io/tutorials.Step-by-Step/schema/requestedBy": "urn:ngsi-ld:Person:bob-the-manager",
    "https://fiware.github.io/tutorials.Step-by-Step/schema/requestedFor": "urn:ngsi-ld:Building:store001",
    "https://fiware.github.io/tutorials.Step-by-Step/schema/stockCount": 10000
}
```

---

## License

[MIT](LICENSE) © 2019-2024 FIWARE Foundation e.V.
