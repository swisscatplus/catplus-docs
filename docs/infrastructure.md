# Infrastructure overview

The different components documented here (ontology, converter and front-end), tie into a bigger Research Data Infrastructure. This RDI is deployed on kubernetes and also includes Databases and a Workflow Orchestrator (Argo), which runs the conversion automatically on new raw data and then uploads them to a Graph Database for RDF metadata.

Here is a brief overview of the infrastructure flow.

## Backend Data Ingestion

```mermaid
sequenceDiagram
    participant InternalDB as SwissCat+ Internal DB
    participant S3 as S3 Storage (SWITCH)
    participant Argo as Argo Workflow
    participant Converter as JSON→RDF Converter
    participant SHACL as SHACL Validator
    participant QLever as QLever RDF DB
    InternalDB->>S3: Export Data & JSON Metadata
    S3->>Argo: Trigger Weekly Sync
    Argo->>Converter: Transform to RDF (Using Ontology)
    Converter->>SHACL: Validate RDF (Conformance check)
    alt RDF is Valid  
        SHACL->>QLever: ✅ Store metadata triples
    else RDF is Invalid 
        SHACL->>Argo: Report validation failure
        Argo-->>Argo: ❌ Abort upload
    end
```

## User Search Flow

```mermaid
sequenceDiagram
    actor User
    participant UI as User Interface
    participant QLever as QLever RDF DB
    User->>UI: Search for data of interest
    UI->>QLever: Send SPARQL query
    QLever->>UI: Return query results (metadata)
    UI->>User: Display data and provide download links
```

For more information, please contact SwissCat+ team or SDSC.