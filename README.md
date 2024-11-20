Graph Database Data Import Web App
==================================

[![Apache License Badge]][Apache License, Version 2.0]

A web tool to make importing csv files into Neo4j super easy.

Overview
--------

### Using the web based tool

![](img/csvimport.gif)

Dependencies
------------

`graph-db-import-web` is a node.js web application, therefore node.js (and npm, the node package manager which is
bundled with node) is required. Installation instructions are available [here](http://nodejs.org)

Installation
------------

1. `git clone git@github.com:QubitPi/graph-db-import-web.git`
2. `cd graph-db-import-web`
3. `npm install`
4. `npm start`
5. Open web browser at url `http://localhost:3000`

Test
----

Unit tests are written using mocha.js and can be run with `npm test`.

Development
-----------

There are essentially two components to this project: a web application that provides a UI to allow the user select
files for import and configure the data model and a small Javascript library that holds the logic for building Cypher
import queries from the configuration object defined in the web application.
 
### Web application overview
 
The main goal of the web application is to allow the user to select csv files for import, handle parsing of the csv
files (this is currently done using the [PapaParse library](https://github.com/mholt/PapaParse), and guide the user through configuring the data model.
The product of this process is two JavaScript objects: one of the content of the parsed files and one that defines the
configured data model (how the parsed csv files are mapped to nodes and relationships). These two objects are passed to
`CypherBuilder`, a small JavaScript library to create Cypher import scripts.
 
Additionally, the web application includes functionality to connect to an existing Neo4j instance to execute the import
queries or simply display the generated Cypher queries for the user.
 
### CypherBuilder.js

This library provides a class `CypherBuilder` whose constructor takes two objects: `parsedFilesData` and `configData`.
The library is designed such that it can be run in a node.js environment or in the client for an alternative
architecture.
 
#### `parsedFilesData`
 
This object contains data from the parsed csv files for import in the format returned by Papaparse. The data for a
parsed CSV file with headers looks like this:
 
```json
{
    "committee-members.csv":{
        "data":[
            {
                "committeeID":"HSAG",
                "legislatorID":136,
                "rank":1
            },
            {
                "committeeID":"HSAG",
                "legislatorID":172,
                "rank":2
            },
            {
                "committeeID":"HSAP",
                "legislatorID":172,
                "rank":3
            },
            {
                "committeeID":"HSAP",
                "legislatorID":179,
                "rank":2
            },
            {
                "committeeID":"HSAS",
                "legislatorID":179,
                "rank":2
            },
            {
                "committeeID":"HSAS",
                "legislatorID":1828,
                "rank":1
            },
            {
                "committeeID":"HSAS",
                "legislatorID":136,
                "rank":2
            }
        ],
        "errors":[

        ],
        "meta":{
            "aborted":false,
            "cursor":1100,
            "delimiter":",",
            "fields":[
                "committeeID",
                "legislatorID",
                "rank"
            ],
            "linkbreak":"\n",
            "truncated":true
        }
    }
}
```
 
#### `configData`
 
This object contains the user-defined configuration/mappings for data import. This object defines the nodes,
relationships, and properties of the user-defined data model. 
  
```json
{
    "nodes": [
        {
            "filename": "legislators.csv",
            "labels": ["Legislator"],
            "properties": [
                {
                    "headerKey": "thomasID",
                    "neoKey": "thomasID",
                    "dataType": "int",
                    "index": true,
                    "primaryKey": true,
                    "foreignKey": false,
                    "skip": false
                },
                {
                    "headerKey": "firstName",
                    "neoKey": "firstName",
                    "dataType": "string",
                    "index": false,
                    "primaryKey": false,
                    "foreignKey": false,
                    "skip": false
                },
                {
                    "headerKey": "lastName",
                    "neoKey": "lastName",
                    "dataType": "string",
                    "index": false,
                    "primaryKey": false,
                    "foreignKey": false,
                    "skip": false
                },
                {
                    "headerKey": "type",
                    "neoKey": "body",
                    "dataType": "string",
                    "index": false,
                    "primaryKey": false,
                    "foreignKey": false,
                    "skip": false
                },
                {
                    "headerKey": "party",
                    "neoKey": "party",
                    "dataType": "string",
                    "primaryKey": false,
                    "foreignKey": false,
                    "skip": false
                }
            ]
        },
        {
            "filename": "committees.csv",
            "labels": ["Committee"],
            "properties": [
                {
                    "headerKey": "thomasID",
                    "neoKey": "thomasID",
                    "dataType": "string",
                    "index": true,
                    "primaryKey": true,
                    "foreignKey": false,
                    "skip": false
                },
                {
                    "headerKey": "jurisdiction",
                    "neoKey": "jurisdiction",
                    "dataType": "string",
                    "index": false,
                    "primaryKey": false,
                    "foreignKey": false,
                    "skip": false
                },
                {
                    "headerKey": "name",
                    "neoKey": "name",
                    "dataType": "string",
                    "index": false,
                    "primaryKey": false,
                    "foreignKey": false,
                    "skip": false
                },
                {
                    "headerKey": "type",
                    "neoKey": "body",
                    "dataType": "string",
                    "index": false,
                    "primaryKey": false,
                    "foreignKey": false,
                    "skip": false
                },
                {
                    "headerKey": "url",
                    "neoKey": "url",
                    "dataType": "string",
                    "index": false,
                    "primaryKey": false,
                    "foreignKey": false,
                    "skip": false
                }
            ]
        }
    ],
    "relationships": [
        {
            "filename": "committee-members.csv",
            "from": {
                "filename": "legislators.csv",
                "neoKey": "thomasID",
                "fileKey": "legislatorID",
                "label": "Legislator"
            },
            "to": {
                "filename": "committees.csv",
                "neoKey": "thomasID",
                "fileKey": "committeeID",
                "label": "Committee"
            },
            "name": "SERVES_ON"
        }
    ]

}
```

License
-------

The use and distribution terms for [graph-db-import-web]() are covered by the [Apache License, Version 2.0].

[Apache License Badge]: https://img.shields.io/badge/Apache%202.0-F25910.svg?style=for-the-badge&logo=Apache&logoColor=white
[Apache License, Version 2.0]: https://www.apache.org/licenses/LICENSE-2.0
