[![FIWARE Banner](https://fiware.github.io/tutorials.Relationships-Linked-Data/img/fiware.png)](https://www.fiware.org/developers)

[![FIWARE Core Context Management](https://nexus.lab.fiware.org/repository/raw/public/badges/chapters/core.svg)](https://github.com/FIWARE/catalogue/blob/master/core/README.md)
[![License: MIT](https://img.shields.io/github/license/fiware/tutorials.Relationships-Linked-Data.svg)](https://opensource.org/licenses/MIT)
[![Support badge](https://img.shields.io/badge/tag-fiware-orange.svg?logo=stackoverflow)](https://stackoverflow.com/questions/tagged/fiware)
[![JSON LD](https://img.shields.io/badge/JSON--LD-1.1-f06f38.svg)](https://w3c.github.io/json-ld-syntax/)

This tutorial discusses relationships between linked data entities and how the concepts of **JSON-LD** and **NGSI-LD**
can be used to interrogate entities and navigate from one entity to another. The tutorial discusses a series of simple
linked-data data models based around the supermarket chain’s store finder application, and demonstrates how to design
models holding one-to-one, one-to-many and many-to-many relationships. This **NGSI-LD** tutorial is a direct analogue to
the earlier _Understanding Entities and Relationships_ tutorial (which was based on the **NGSI v2** interface). The
differences in relationships created using **NGSI v2** and **NGSI-LD** are highlighted and discussed in detail.

The tutorial uses [cUrl](https://ec.haxx.se/) commands throughout, but is also available as
[Postman documentation](https://www.postman.com/downloads/).

# Start-Up

## NGSI-LD Smart Supermarket

**NGSI-LD** offers JSON-LD based interoperability used for Federations and Data Spaces. To run this **NGSI-LD** tutorial for **NGSI-v2** developers, use the `NGSI-v2` branch.

```console
git clone https://github.com/FIWARE/tutorials.Relationships-Linked-Data.git
cd tutorials.Relationships-Linked-Data
git checkout NGSI-v2

./services create
./services start
```

| [![NGSI LD](https://img.shields.io/badge/NGSI-LD-d6604d.svg)](https://www.etsi.org/deliver/etsi_gs/CIM/001_099/009/01.08.01_60/gs_cim009v010801p.pdf) | :books: [Documentation](https://github.com/FIWARE/tutorials.Relationships-Linked-Data/tree/NGSI-v2) | <img src="https://cdn.jsdelivr.net/npm/simple-icons@v3/icons/postman.svg" height="15" width="15"> [Postman Collection](https://fiware.github.io/tutorials.Relationships-Linked-Data/) |
| --- | --- | --- |

---

## License

[MIT](LICENSE) © 2019-2024 FIWARE Foundation e.V.
