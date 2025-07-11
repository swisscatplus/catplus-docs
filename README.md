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

This project uses uv for dependency and environment management.

```
git clone git@github.com:sdsc-ordes/catplus-docs.git
cd catplus-docs
uv sync
```

Run the docs locally:

```
uv run mkdocs serve
```

The docs are then visible on [your local port 8888](http://127.0.0.1:8888/).
