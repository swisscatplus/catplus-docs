# Tutorial Converter

## Prerequisites

- Install just: https://github.com/casey/just: just is a command runner
- Install Nix: https://nixos.org/download/: Nix is a tool for package management that uses declarative language to provide reproducible systems
- Install Direnv: https://direnv.net/docs/installation.html: direnv is an open source environment management tool that allows setting unique environment variables per directory in your file system

## Basic setup

Step 1: Install Rust

The converter is coded in Rust. Installing Rust (and it's toolstack like cargo, the package manager): https://www.rust-lang.org/tools/install

The main packages used in the converter are (this is for your information, they are already in the dependencies in the Cargo.toml):

- `serde_json` for serializing the JSON files: https://crates.io/crates/serde_json
- `sophia` (and annex packages) for RDF manipulation: https://crates.io/crates/sophia

For the crates created by the project, please read `docs/converters.md`, `Architecture` section.

Step 2: `Just` commands

For all commands, you will want to be in the Nix shell (you might need to grant that by accepting direnv allow), to have a reproducible environment with dependencies preconfigured: run `just dev`.

All conversion commands are available in the `README`. We encourage you to try them out. For checking the output of the conversion, please see the `SHACL Validation` section in `docs/converters.md` to see how to send the output RDF to a SHACL validator.

## Going over components

- catplus-commons
- converter
- docker image

## Checking understanding questions

### HCI / Synth / Agilent or Bravo change

The mapping from JSON to RDF is specified in `catplus-common/src/models/` where common entities (chemicals, peaks, products) are defined in `core`, but all other entities specific to each data type are in their respective file `hic`, `synth`, `agilent`, `bravo`.

Let's look at how this mapping works. For Agilent, there is a Device Document structure created (shortened version of the struct presented here): 

```rust
pub struct DeviceDocument {
    #[serde(rename = "device identifier")]
    pub device_identifier: String,
    #[serde(rename = "device type")]
    pub device_type: String,
}
```

The rename fields match the name of the key in the JSON files. The struct allows to parse the JSON into Rust objects. To every struct, we couple an InsertIntoGraph function, which does just that.

```rust
impl InsertIntoGraph for DeviceDocument {
    fn get_uri(&self) -> SimpleTerm<'static> {
        // build URI based on self.device_identifier
        generate_resource_identifier_uri(self.device_identifier.clone())
    }

    fn insert_into(&self, graph: &mut LightGraph, iri: SimpleTerm) -> anyhow::Result<()> {
        for (pred, value) in [
            (rdf::type_, &allores::AFR_0002567.as_simple() as &dyn InsertIntoGraph),
            (allores::AFR_0002018, &self.device_identifier.as_simple()),
            (allores::AFR_0002568, &self.device_type.as_simple()),
        ] {
            value.attach_into(
                graph,
                Link { source_iri: iri.clone(), pred: pred.as_simple(), target_iri: None },
            )?;
        }
        Ok(())
    }
}
```

The `get_uri` function is defined for the objects such as the chemicals and devices that are recurrent across Campaigns (as they are always used to synthesize or process). Let's break down the `insert_into` function.

The first line is to define the type of the new RDF object we are inserting into the graph. Here we are saying it is of type `allores::AFR_0002567` (which if you look into the ontology is the Device Document type). This is where the matching to RDF is happening.

```rust
(rdf::type_, &allores::AFR_0002567.as_simple() as &dyn InsertIntoGraph),
```

The function then proceeds to add the properties of the device document (the same as listed in the ontology). The string `device_identifier` is added as the property `allores::AFR_0002018` and the string `device_type` as the property `allores::AFR_0002568`, matching what is defined in the ontology.

```rust
(allores::AFR_0002018, &self.device_identifier.as_simple()),
(allores::AFR_0002568, &self.device_type.as_simple()),
```

Here both properties are are strings so it's the end of the story. However, if the property points to another object, then in the Rust code, it would point to another structure. Let's see an example. A Batch has several actions (objects), so we can see that the struct points to a Vector of other structs (SynthAction).

```rust
pub struct SynthBatch {
    #[serde(rename = "batchID")]
    pub batch_id: String,
    #[serde(rename = "Actions")]
    pub actions: Vec<SynthAction>,
}
```

```rust
pub struct SynthAction {
    pub action_name: ActionName,
    pub start_time: String,
    pub ending_time: String,
    pub method_name: String,
}
```

### Managing units and other enums

The units are defined in the `catplus-common/src/models/enums.rs` script. All units are defined at the same level in the enum `pub enum Unit`, following how they appear in the JSON file. The train `impl Unit` defines the function `iri` which takes care of matching the unit to the correct ontology in its `namespace` function, and to the ontology name in its `display_name` function.

### Generating unique URIs

In `catplus-common/src/graph/utils` there is a key function that has been mentioned above already: the `generate_resource_identifier_uri`. This is the function that gives a unique URI to an object in the graph. It allows for a chemicals or a devices to be referred to as the same one across camapigns, because the URI is based on their identifiers. It also allows a Product or a Peak to be referenced as a same object across different experimental steps (synth, agilent, bravo).

### Adding contentURL

Another important aspect is adding a `contentURL` (i.e. the S3 or local URL of the file being parsed) to a graph object. Please read `catplus-common` section in `docs/converters.md` to fully grasp this concept.

### Managing the converter containers

There are 2 containers for the converter (one is in the repository, the other is under catplus-deployment / catplus-manifests, both are private infrastructure repositories)

1. The minimal converter image in the converter repository. - See `docs/converters.md` and `Binary and Image` section

2. The other converter image is the one built in the deployment repositories. This is the one running in the Argo workflows.
