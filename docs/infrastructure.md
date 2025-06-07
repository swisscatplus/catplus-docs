```mermaid
sequenceDiagram
    participant InternalDB as SwissCat+ Internal DB
    participant S3 as S3 Storage (SWITCH)
    participant Argo as Argo Workflow
    participant Converter as JSON→RDF Converter
    participant SHACL as SHACL Validator
    participant QLever as QLever RDF DB
    participant UI as User Interface

    InternalDB->>S3: Export Data & JSON Metdata
    S3->>Argo: Trigger Weekly Sync
    Argo->>Converter: Transform to RDF (Using Ontology)
    Converter->>SHACL: Validate RDF (Conformance check)
    SHACL-->>QLever: ✅ Valid → Store Metadata
    SHACL-->>Argo: ❌ Invalid → Abort Upload
    QLever->>UI: Serve Metadata (Searchable via SPARQL)
    User->>UI: Search Metadata for data of interest
    UI->>User: Display Data + Allow Downloads

    Note right of QLever: Metadata stored in RDF triples
    Note right of SHACL: SHACL ensures structure matches ontology
```