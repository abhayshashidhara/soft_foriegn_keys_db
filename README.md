# Soft Foreign Keys for Probabilistic Entity Linking

This project implements a **soft foreign-key mechanism** for probabilistic entity linking across relational tables using **text normalization, similarity matching, and confidence scoring**.  
The system integrates a **LLaMA-based language model** to support interactive querying and explanation of inferred relationships, enabling meaningful joins without hard database constraints.

---

## Project Overview

In many real-world datasets, especially those derived from user-generated text, strict foreign key constraints are impractical. Entity names are often noisy, inconsistent, abbreviated, or misspelled, making exact string matching unreliable.

This project addresses that challenge by treating referential integrity as a **probabilistic inference problem** rather than an exact constraint. Instead of enforcing a traditional foreign key, the system infers the most likely link between entities and records the association along with a confidence score. These inferred links behave like foreign keys when confidence is high, while still preserving uncertainty when mappings are ambiguous.

The approach is demonstrated using healthcare data, where noisy drug names from patient reviews are linked to a standardized generic-drug catalog.

---

## Key Idea: Soft Foreign Keys

A **soft foreign key** is an approximate, confidence-weighted link between two relational tables.

- Traditional foreign keys require exact matches  
- Soft foreign keys allow **semantic matching**  
- Each inferred link includes a **confidence score**  
- High-confidence links can be safely used in joins  
- Low-confidence links are left unmapped to avoid errors  

This preserves SQL-style querying (joins, aggregation, indexing) while acknowledging uncertainty in real-world data.

---

## Methodology

The system follows a multi-stage pipeline:

1. **Data Loading and Cleaning**  
   Noisy free-text identifiers are extracted from a review dataset and normalized for processing.

2. **Initial Name Inference (LLM-Assisted)**  
   A **LLaMA-based language model** is used to infer a standardized drug name from each noisy input string, along with a short justification.

3. **Similarity-Based Refinement**  
   The inferred name is refined using **vector embeddings** and semantic similarity search against a standardized drug catalog to retrieve the closest matches.

4. **Validation and Confidence Scoring (LLM-Assisted)**  
   The LLaMA model evaluates the proposed match and produces a **confidence score** between 0 and 1 indicating the likelihood of correctness.

5. **Database Integration**  
   High-confidence mappings are stored in PostgreSQL as soft foreign keys, enabling reliable downstream joins and analysis.

---

## Models Used

### Language Model
A **LLaMA-based instruction-following language model** is used to:
- Infer standardized entity names from noisy inputs  
- Justify mapping decisions  
- Validate final matches and assign confidence scores  

The model is used as a reasoning and validation component rather than as a standalone source of truth.

### Embedding & Similarity Models
Text embeddings are used to represent drug names in a semantic vector space.  
Similarity search identifies the closest standardized entities for refinement and validation.

### Design Rationale
Combining symbolic SQL workflows with probabilistic AI-based matching allows the system to:
- Retain relational database structure  
- Handle noisy, real-world identifiers  
- Provide transparent and explainable mappings  

---



