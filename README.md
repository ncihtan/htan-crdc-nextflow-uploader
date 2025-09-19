# htan-to-crdc-nextflow-uploader

A Nextflow workflow for uploading Human Tumor Atlas Network (HTAN) data to the Cancer Research Data Commons (CRDC).

---

## Table of Contents

* [Overview](#overview)
* [Features](#features)
* [Repository Structure](#repository-structure)
* [Prerequisites](#prerequisites)
* [Configuration](#configuration)
* [Input Data Format](#input-data-format)
* [Running the Workflow](#running-the-workflow)
* [Outputs](#outputs)
* [Error Handling & Logging](#error-handling--logging)
* [Contact & Contribution](#contact--contribution)

---

## Overview

This pipeline facilitates the preparation and upload of HTAN data into the CRDC system. It is implemented using [Nextflow](https://www.nextflow.io/), and handles the required formatting, schema validation, metadata, and data transfer steps to ensure compatibility with CRDC submission requirements.

---

## Features

* Validates metadata against a predefined schema.
* Manages a sample sheet (TSV) for tracking inputs.
* Supports configurable parameters via `nextflow.config`.
* Includes a structured directory layout (e.g. `conf/`) for configuration.
* Integration options for monitoring (e.g., Tower) via `tower.yaml`.

---

## Repository Structure

Here’s a breakdown of the key files and directories:

| Path                   | Description                                                                             |
| ---------------------- | --------------------------------------------------------------------------------------- |
| `main.nf`              | The main Nextflow workflow script.                                                      |
| `nextflow.config`      | Configuration file for runtime settings, resource specification, containerization, etc. |
| `conf/`                | Directory for configuration templates or schema files.                                  |
| `nextflow_schema.json` | JSON schema (or related spec) that metadata must adhere to.                             |
| `samplesheet.tsv`      | Sample sheet for listing input data files and related metadata fields.                  |
| `assets/`              | Supporting scripts, utilities, or static assets used during the upload process.         |
| `tower.yaml`           | Spec for launching / integrating with Nextflow Tower (if used).                         |

---

## Prerequisites

To use this workflow, you’ll need:

* **Nextflow** installed on your system.
* Docker (or another container engine compatible with Nextflow) if using containerized steps.
* Access credentials / permissions for CRDC / HTAN as required.
  
---

## Configuration

Configuration is handled via `nextflow.config` and related files. Some key settings you’ll likely need to customize:

* Paths to input data (e.g. raw HTAN data).
* Output directories.
* Schema validation options (ensuring metadata complies with CRDC expected formats).
* Container / environment settings (Docker images, volumes, memory, CPUs, etc.).
* Optional monitoring / logging (e.g. via Nextflow Tower).

---

## Input Data Format

Data should be provided via a **samplesheet** in TSV format (`samplesheet.tsv`), which lists all files to be uploaded along with their associated metadata fields. The metadata must conform to the `nextflow_schema.json` schema.

Ensure that all required metadata fields (as defined by CRDC / HTAN) are present and valid. Missing or malformed metadata will likely cause validation or upload failures.

---

## Running the Workflow

Here is an example of how to run the pipeline:

```bash
nextflow run main.nf \
  --samplesheet path/to/samplesheet.tsv \
  --outdir path/to/output_directory \
  [other parameters as configured]
```

You may also override configuration values:

```bash
nextflow run main.nf \
  -c path/to/your_config_file \
  --samplesheet samplesheet.tsv \
  --outdir output/
```

If using Nextflow Tower or similar monitoring tools, use the `tower.yaml` spec as appropriate.

---

## Outputs

After a successful run, you should expect:

* Metadata files formatted and validated against CRDC schema.
* Data files (images, sequencing, etc.) organized in the directory structure expected by CRDC / HTAN.
* Logs / reports of validation, transformation, or upload steps.
* Any manifest or submission files needed for CRDC ingestion.

---

## Error Handling & Logging

* The workflow logs each step; errors in schema validation or upload are reported in the logs.
* Nextflow’s built-in retry/resume functionality can be used to resume after partial failures.
* Keep an eye on resource usage — memory, CPU, disk — especially when handling large files.

---

## Contact & Contribution

If you encounter issues, have feature requests, or wish to contribute:

* Open an issue in this repository.
* Submit pull requests with clear descriptions of changes and tests where applicable.
* Contact the maintainers for access or credential questions.

