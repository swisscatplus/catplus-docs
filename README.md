# SwissCAT+ CHEMBORD

## Context

All the code presented in this documentation comes from the fruitful collaboration between [SwissCAT+](https://swisscatplus.ch) and [Swiss Data Science Center (SDSC)](https://www.datascience.ch).

## Research Data Infrastructure (RDI) Documentation

Ontology: [catplus-ontology](https://github.com/sdsc-ordes/catplus-ontology)

Converters: [catplus-converters](https://github.com/sdsc-ordes/catplus-converters)

Front-end (UI) : [catplus-chemboard](https://github.com/sdsc-ordes/catplus-chemboard)

Kubernetes Deployment (private, contact project leads): [catplus-manifests](https://github.com/sdsc-ordes/catplus-manifests) 
(includes argo workflows, qlever RDF database, UI, s3 integration)

## Development

### Development environment

The project is set-up with a uv virtual environment: `catplus-docs`.
To create the same environment on your machine:

1. Create a uv venv with `uv venv catplus-docs python=3.10.16`
2. Activate the virtual env, you must do: `source catplus-docs/bin/activate`
3. Install dependencies with `uv pip install -r requirements.txt`

### Local Deployment of mkdocs

Local deployment of the docs is with the command `mkdocs serve` and the docs are then visible on [your local port 8888](http://127.0.0.1:8888/).
