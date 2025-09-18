# HTAN CRDC Data Uploader

## Overview

This Nextflow workflow automates the transfer of HTAN data files from Synapse into the CRDC Data Hub.
It processes a sample sheet, downloads files from Synapse, generates metadata, and uploads the files to CRDC using the official uploader.

The workflow is modular, enabling reproducible submission pipelines for HTAN centers.

### Example Usage

```bash
nextflow run ncihtan/htan-crdc-nextflow-uploader --input samplesheet.csv
```

## Input

### Parameters

| Parameter           | Type   | Description                                                                                   |
| ------------------- | ------ | --------------------------------------------------------------------------------------------- |
| input               | `str`  | Path to a samplesheet TSV containing entityId, project\_id, and file metadata. Required.  |
| dryrun              | `bool` | If true, runs uploader in dryrun mode (no files uploaded). Default: `false`                   |
| crdc\_config        | `str`  | Path to YAML configuration for CRDC uploader. Required.                                       |

## Workflow Details

### Overview

The workflow executes the following stages:

1. **Parse samplesheet** – Reads Synapse entityIds and metadata.
2. **synapse\_get** – Downloads files from Synapse.
3. **prepare\_metadata** – Builds CRDC metadata TSVs/YAML from sample rows.
4. **crdc\_upload** – Uploads data + metadata to CRDC S3 via the CLI uploader.
5. **generate\_report** – Summarizes transfer success/failures.

### Secrets

This workflow uses Nextflow secrets to store credentials securely:

* `SYNAPSE_AUTH_TOKEN`: Synapse auth token.
* `CRDC_TOKEN`: Authentication for CRDC uploader.

### Input Samplesheet Requirements

| Field              | Required | Pattern    | Description                |                             |
| ------------------ | -------- | ---------- | -------------------------- | --------------------------- |
| entityId           | Yes      | `^syn\d+$` | Synapse entity ID          | `syn123456`                 |
| file\_url\_in\_cds | Yes      | `^s3://.+` | Target CRDC S3 destination | `s3://cds-project/file.bam` |
| project\_id        | Yes      | String     | CRDC project ID            | `phs002371`                 |

Notes:

* Additional metadata columns (e.g. `Filename`, `HTAN_Data_File_ID`) are allowed.
* These are used to construct CRDC manifests.

## Default Settings

The included `nextflow.config` defines profiles:

| Profile | Description                                                         |
| ------- | ------------------------------------------------------------------- |
| test    | Runs with bundled test samplesheet and dryrun enabled               |
| crdc    | Runs full CRDC submission with required credentials                 |
| local   | Executes on local machine (no Docker)                               |
| docker  | Executes in Docker with containers for Synapse and CRDC uploader    |
| tower   | Configures retries, memory, and resource scaling for Nextflow Tower |

### Process 1: `synapse_get`

* **Input:** `entityId`, metadata row
* **Output:** Local file path
* **Dependencies:** Synapse Python client + `SYNAPSE_AUTH_TOKEN`

### Process 2: `prepare_metadata`

* **Input:** Samplesheet row + downloaded file
* **Output:** Metadata TSV/YAML for CRDC

### Process 3: `crdc_upload`

* **Input:** File path + metadata
* **Output:** Upload success/failure status
* **Dependencies:** AWS + CRDC credentials

### Reports

The workflow produces:

* Transfer logs in `reports/`
* Trace CSV for task monitoring

## Running the Workflow

### Prerequisites

* Nextflow installed
* Docker/Singularity (for containers)
* Valid Synapse + AWS + CRDC credentials

### Example

```bash
nextflow run ncihtan/htan-crdc-nextflow-uploader --input samplesheet.csv --bucket s3://crdc-bucket
```

Use `-profile test` to run a dryrun with provided test data:

```bash
nextflow run ncihtan/htan-crdc-nextflow-uploader -profile test
```

To run with a credential prefix:

```bash
nextflow secrets set CRDC_AWS_ACCESS_KEY_ID <key>
nextflow secrets set CRDC_AWS_SECRET_ACCESS_KEY <secret>
nextflow run ncihtan/htan-crdc-nextflow-uploader --aws_secret_prefix CRDC
```

---

Do you want me to also generate a **side-by-side comparison table** between `nf-cdstransfer` and `htan-crdc-nextflow-uploader` so you can quickly see what’s different (extra steps, parameters, secrets)?
