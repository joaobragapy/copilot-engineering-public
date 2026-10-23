# Apache Ossie 0.1.1 — pinned specification reference

## Version policy

This skill targets Apache Ossie **0.1.1**, the version selected for this project. Its status is a release candidate: the only evidence located is the candidate tag `osi-0.1.1-rc1` and the `release/0.1.1` branch, and no final tag was found. Do not copy the schema or examples from the repository's `main` branch unless they are verified to match 0.1.1. The current `main` documentation describes `0.2.0.dev0` and documents a breaking change to the document shape.

Official project resources:

- Repository: https://github.com/apache/ossie
- Published specification/project documentation: https://ossie.apache.org/
- Current main-branch specification (for comparison only, not as the 0.1.1 authority): https://github.com/apache/ossie/blob/main/core-spec/spec.md
- Current main-branch schema (for comparison only): https://github.com/apache/ossie/blob/main/core-spec/ossie-schema.json

## Important version distinction

The current `0.2.0.dev0` specification uses a single model document with `version`, `name`, and `datasets` at the root. The project documentation indicates that earlier released shapes use a `semantic_model` array. Do not mix these formats.

## Schema provenance

The local machine-readable schema is `references/osi-schema.json`.

- Upstream repository: `apache/ossie`.
- Specification version: 0.1.1 (`core-spec/spec.md` declares `Version: 0.1.1` on the release branch).
- Branch: `release/0.1.1`.
- Commit: `faf581054dcf7964d5fe0ceae7d6f415c8ce32a5`.
- Candidate tag: `osi-0.1.1-rc1` (annotated, unsigned, points to the same commit). This is a release candidate.
- Upstream path: `core-spec/osi-schema.json`.
- Upstream Git blob: `a4a6f92af51544a6500040c5e62d5e278113f838`.
- Raw SHA-256 of the local working-tree copy (`references/osi-schema.json`, read as bytes): `c1e9adec39562786aa78809665fba568797b15f4c53a0847d9cbcf2dead1bc94`, 9797 bytes.
- Raw SHA-256 of the upstream file at the commit above: `c1e9adec39562786aa78809665fba568797b15f4c53a0847d9cbcf2dead1bc94`, 9797 bytes.
- The Git index holds a different blob (`e3484c82181b4a1b10aaee9f489d6e2e31c7a81f`, 9796 bytes), which lacks the trailing LF. The working-tree copy matches the upstream blob `a4a6f92af51544a6500040c5e62d5e278113f838`.

Comparison result: the raw SHA-256 of the local working-tree copy and of the upstream file are identical. A semantic JSON comparison also reported no differences. Not verified: that the schema passes the upstream examples, validators, or tests. No upstream validation was run.

Naming: the `$id` and `title` in the schema use OSI names (`open-semantic-interchange/OSI`, "OSI Core Metadata Specification"), and the upstream file contains the same values. The relationship between the OSI and Ossie names was not verified, so this file does not state that one was renamed from the other.

Limitation: no final tag for 0.1.1 was located. The `osi-0.1.1-rc1` tag and the `release/0.1.1` branch are the only evidence found for this version. Do not describe either as a final release tag.

## Verification status

**Partially verified:** the schema source and content are traced to upstream `release/0.1.1` at the commit above, as described in [Schema provenance](#schema-provenance). Before using the starting template or claiming that a candidate is valid for 0.1.1, verify the examples and validator used for this release. Record the immutable tag/commit or artifact URL used. Do not use `main` as a substitute.

## Supported concepts to investigate against the pinned schema

The Ossie project describes semantic models in terms of datasets, fields, relationships, metrics, expressions/dialects, AI context, and vendor extensions. The exact fields, required properties, allowed enum values, and validation behavior must be confirmed against 0.1.1 before the skill emits them.

## Validation reporting

A validation report must state:

- Ossie specification version.
- Exact schema or validator source/version.
- Exact command executed, if any.
- Actual exit status and output summary.
- Whether validation covered parsing, schema structure, expression checks, referential integrity, or runtime behavior.
- Any checks not performed.
