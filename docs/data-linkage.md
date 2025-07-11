# Data Linkage

The ontology conceptualizes numerous concepts spanning various raw data files.The converter processes these many different kinds of raw data files. this sequence diagram aims to help understand the ties between concepts and how files are linked together.

```mermaid
sequenceDiagram
    participant HCI as HCI File
    participant Synth as Synthesis File
    participant Agilent as Agilent File
    participant Bravo as Bravo File
    participant Spectro as UV/NMR/IR Files

    HCI->>Synth: Execute Batch ID (Parameters used)
    Synth->>Synth: Generate Product ID (Synth produces several products)
    Synth->>Agilent: Send Product for analysis 
    Agilent->>Agilent: Generate Peaks (Agilent analyses one product)
    Agilent->>Bravo: Transfer Peaks for further processing
    Bravo->>Spectro: Send Peaks for characterization

    Note over Synth,Agilent: Product ID links Synthesis & Agilent
    Note over Agilent,Bravo: Peak ID links Agilent & Bravo
    Note over Agilent,Spectro: Peak ID reused in UV/NMR/IR
```