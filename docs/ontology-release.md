# Ontology Release

Releases of the ontology follow semantic versioning. 

# Upon Release

## Old Data Migration 

Ensure your old data conforms to the ontology changes. Data migration must be done. 

## Redeploy Shacl server

- Delete deployment shacl on the k8 cluster
- Run `just deploy src/shacl-api`

## Redeploy Qlever DB

- Delete deployment qlever on the k8 cluster. (leave the UI)
- Run `just deploy src/qlever`