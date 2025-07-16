# Converter

The Cat+ converter is at this repository [catplus-converters](https://github.com/sdsc-ordes/catplus-converters).

## Architecture

The repository contains 3 crates:

* catplus-common: mappings from json to rdf into the [catplus-ontology](https://github.com/sdsc-ordes/catplus-ontology)
* converter: command line interface for the converter.
* validation: command line helper for SHACL validation of RDF graphs.

### catplus-common

The catplus-common crate implements the mapping from the raw JSON objects to the RDF graph.

catplus-common has 3 modules: 

* `graph`: contains namespaces and utilities
* `models`: implements the transformation from `json` to `rdf` and constructs the rdf graph
* `rdf`: implements the serialisation of the result graph and currently supports `turtle` and `jsonld`


The `models` module splits the representation of instance data by file type (with list-like concepts such as units under `enums` and overarching terms repeating over file types in `core`). Objects are defined as structs, with each one implementing the trait `InsertIntoGraph`. If a unique URI is required for the object (as for devices, chemicals, products), then the function `get_uri` must be defined within the trait (see existing examples in Agilent and Synth). The [data linkage documentation](data-linkage.md) can help understand how the different models come together.

The `graph` module contains the `graph::namespaces` child module where each prefix and its terms are defined. The `graph::insert_into` module defines how different types of objects get inserted into the graph to produce valid RDF. The `graph::graph_builder` exposes a Builder interface wrapping the insert_into methods. It is also where the `schema:contentURL` field gets inserted into the graph. The `schema:contentURL` is the path to the raw data json input file. Currently, the raw data is linked to the metadata at the level of the HCI file, onto the Campaign object, as well as at the Agilent level, onto the Liquid Chromatography Document object. New contentURL objects can be defined in the `link_content` function following the existing examples. The raw data file path of the file being parsing will be automatically attached under this concept: hence this concept needs to be unique to the file being parsed for their to be a unique link to the raw data.

### Converter Crate

The converter can process file one by one or an entire folder.

Example for a file:

```just
just convert /examples/2-Agilent.json turtle
```

Example for a folder (multifiles):

```just
just convert /examples/complex_model/Bravo2/ turtle
```

Example for a folder (multifiles) where the output folder is specified:

```just
just convert /examples/complex_model/Bravo2/ turtle --output-folder=examples/rdf
```

`main` defines the CLI processing for the converter. The converter infers automatically from the raw data filename which type of conversion it needs to perform.
`converter` takes care of instantiating a graph, building the graph by parsing the JSON, and finally linking in the raw data file path if relevant (under `contentURL`).

### Tests

The command `just test` runs all tests. The raw data used for tests is in `data/tests/` and needs to be updated if the structure of the input `json` data changes. The validating turtle instance data is within the test scripts (in `converter/tests/`) and need to be adapted based on the changes made to the converter.

## SHACL Validation

The instance data produced by the converter follow the definitions in the [SwissCAT+ ontology](https://github.com/sdsc-ordes/catplus-ontology). SHACL validation can be done with the following commands: 

1. Activate the Nix shell: `just dev`
2. Start a local shacl validator docker image based on the [SHACL-API service](ghcr.io/sdsc-ordes/shacl-api:develop) (the ontology shapes are loaded by default): `just shacl-start`
3. Ping the SHACL-API service to know if the instance data in RDF is valid with the ontology: `just validate YOUR_TURTLE_FILE`
4. Turn off the SHACL-API image: `just shacl-stop`

## Binary and Image

A binary and image (docker) is created via Nix derivatives (code is in `tools/image` with commands in `tools/just/image.just`, same for the package).

```just
just dev
just image build
just image push
```

The [resulting images can be found here](https://github.com/orgs/sdsc-ordes/packages/container/package/catplus-converters).
The image is extremely minimal and contains only the converter.

## Contribution guidelines

1. Open an issue for the change you would like to make
2. Make a new branch for the change. Use conventional commit nomenclature.
3. Ensure tests are still functional with `just test`
4. Ensure SHACL is still valid (see SHACL Validation section)
5. Ensure code is formatted with `just format`
6. Open a PR for your changes and asign a reviewer.
