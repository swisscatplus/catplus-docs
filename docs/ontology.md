# 🧬 Editing the catplus Ontology

This document provides guidance for contributors who want to modify the ontology file directly.

---

## 📌 File location

All ontology definitions are located in the ontology repository:

```
[ontology/catplus_ontology.ttl](https://github.com/sdsc-ordes/catplus-ontology/tree/main/ontology)
```

It contains:

- Class definitions (e.g. `cat:SomeClass`)
- SHACL NodeShapes (often doubletyped with the rdfs:Class)
- Property shapes (often as implicit blank nodes)
- Metadata (authors, version info, etc.)

---

## ✏️ Editing Classes and Labels

### 🔄 Change a label

To update the human-readable label for a class:

**Before:**
```ttl
cat:MyClass a rdfs:Class ;
  skos:prefLabel "My Class" ;
  ...
```

**After:**
```ttl
cat:MyClass a rdfs:Class ;
  skos:prefLabel "My-Class" ;
```

---

### 📝 Update the definition

Use `skos:definition` to describe the term. Multiple definitions are allowed but should be semantically distinct.

**Example:**
```ttl
skos:definition "My class is used to give examples in documentation" ;
```

To edit it, simply modify or replace the string.

---

## ➕➖ Adding or Removing Properties from NodeShapes

NodeShapes declare the expected properties for each class. These are usually inline (blank node) property shapes.

### ✅ To **add a property**:

```ttl
cat:MyClass a rdfs:Class, sh:NodeShape ;
  sh:property [
    sh:path cat:myProperty ;
    sh:datatype xsd:decimal ;
    sh:minCount 1 ;
  ] ;
```

Add another `sh:property [ ... ]` block to the NodeShape.

---

### ❌ To **remove a property**, delete its `sh:property [ ... ]` block:

```ttl
# Remove this block if no longer needed
sh:property [
  sh:path cat:hasOldProperty ;
  ...
] ;
```

Make sure to preserve the surrounding punctuation (`;`) in the shape.

---

## ⚙️ Editing Constraints of Inline Property Shapes

Most property constraints are written using blank nodes. You can discover all property constraints (and all other SHACL documentation) in the [SHACL Spec](https://www.w3.org/TR/shacl/) Here's how to modify them:

### Example: Add or change constraints

**Before:**
```ttl
sh:property [
  sh:path cat:myProperty ;
  sh:datatype xsd:decimal ;
] ;
```

**To enforce a property being mandatory:**
```ttl
sh:property [
  sh:path cat:myProperty ;
  sh:datatype xsd:decimal ;
  sh:minInclusive 0 ;
] ;
```

**To make it mandatory:**
```ttl
sh:minCount 1 ;
```

**To enforce using an instance of a class as a value for a property (e.g. relationships):**
```ttl
sh:class cat:MyClass ;
```

**To enforce a datatype:**
```ttl
sh:datatype xsd:integer ;
```
Note: Combining sh:datatype and sh:class is not possible - sh:class is linking two instances of classes together, and sh:datatype is for literals (strings, integers etc.).

---

## ✅ Tips

- Use consistent indentation and ordering: `sh:path`, `sh:datatype`, `sh:minCount`, etc.
- Always re-run SHACL validation locally before committing (see main README).
- Use `skos:example` to clarify usage when appropriate.
- Keep class and NodeShape definitions bundled together (same subject).

---

## 🧪 Validate Before Committing

Run local validation to catch syntax or logic errors:

```bash
cd tools/python/
pip install -r requirements.txt
python checks/shacl.py
```

Inspect the validation report to catch logical errors, or the stack trace for syntax errors, which should point to the line where the error is made. The spec for [Turtle syntax](https://www.w3.org/TR/turtle/) which is used can also be helpful.

