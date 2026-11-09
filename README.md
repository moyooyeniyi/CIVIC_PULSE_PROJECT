# CIVIC PULSE | Azure Data Engineering Pipeline

An end-to-end cloud data engineering portfolio project using **Microsoft Azure, Python, Polars, PostgreSQL, Azure Data Factory, and Apache Airflow**.

**Status:** In Progress  
**Cloud:** Microsoft Azure  
**Architecture:** Medallion Architecture (Bronze, Silver, Gold)  
**Domain:** Public Sector / Municipal Service Analytics

## 1. Project Overview

CIVIC PULSE is a cloud-based data engineering project designed to transform raw municipal service request data into structured, analysis-ready datasets.

Public service agencies handle large volumes of requests relating to infrastructure, sanitation, environmental concerns, and community services. Processing this data reliably can help agencies understand service demand, monitor operational performance, identify backlogs, and support resource allocation.

This project demonstrates the implementation of a Medallion Architecture using Azure Blob Storage, Python, Polars, Azure Data Factory, and Azure Database for PostgreSQL.

The initial data ingestion, transformation, and Silver-to-Gold loading stages have been implemented successfully. Apache Airflow orchestration is currently in development.

## 2. Project Objectives

- Build an end-to-end cloud data engineering pipeline.
- Ingest municipal service request data into Azure Blob Storage.
- Transform raw CSV data into Parquet using Python and Polars.
- Organise data using Bronze, Silver, and Gold layers.
- Load transformed records into Azure Database for PostgreSQL.
- Automate data movement using Azure Data Factory.
- Develop workflow orchestration using Apache Airflow.
- Validate pipeline results and document implementation evidence.
- Explore future reporting and analytical use cases.

## 3. Technology Stack

| Technology | Purpose |
|---|---|
| Microsoft Azure | Cloud infrastructure |
| Azure Blob Storage | Bronze and Silver storage |
| Python | Ingestion and transformation |
| Azure Storage SDK | Programmatic blob uploads |
| Polars | Data transformation |
| Apache Parquet | Columnar data format |
| Azure Data Factory | Silver-to-Gold pipeline |
| Azure Database for PostgreSQL | Gold-layer database |
| PostgreSQL and pgAdmin | SQL development and verification |
| Apache Airflow | Workflow orchestration (in progress) |
| Astronomer Astro CLI | Local Airflow development |
| Docker | Local Airflow containers |
| Git and GitHub | Version control |
| VS Code | Development environment |

## 4. Data Architecture

The solution follows a three-layer Medallion Architecture.

```text
Municipal Service Request Dataset (CSV)
                |
                v
       Python Ingestion Script
                |
                v
     Azure Blob Storage: BRONZE
       urban_service_requests.csv
                |
                v
       Python + Polars
    Selection / Renaming / Parquet
                |
                v
     Azure Blob Storage: SILVER
     urban_service_requests.parquet
                |
                v
        Azure Data Factory
          Copy Activity
                |
                v
      Azure Database for PostgreSQL
         GOLD Schema / Table
                |
                v
       Future SQL Analytics
        and Reporting

Apache Airflow: local environment running;
DAG orchestration not yet implemented.
```

### Bronze Layer

Stores the raw CSV dataset in Azure Blob Storage.

**Object:** `bronze/urban_service_requests.csv`

### Silver Layer

Stores selected and standardised fields in a Snappy-compressed Parquet dataset.

**Object:** `silver/urban_service_requests.parquet`

### Gold Layer

Stores transformed records in Azure Database for PostgreSQL for SQL queries and future analytical workloads.

**Database:** `urban_city_db`  
**Schema:** `gold`  
**Table:** `urban_city_requests`

## 5. Implementation and Visual Evidence

The following sections document each stage of the implementation. Screenshots are embedded directly beneath the relevant explanations to demonstrate the work performed and the resulting output.

### Phase 1: Development Environment Setup

**Status: Completed**

The project began with local environment configuration on macOS, including:

- Cloning the GitHub repository.
- Creating and activating a Python virtual environment.
- Configuring VS Code.
- Installing the required Python dependencies.
- Preparing the repository structure and Git configuration.

#### PostgreSQL Server Reference

The PostgreSQL environment was reviewed as part of the database setup and learning process.

![PostgreSQL Server Reference](screenshots/01-postgresql-server.png)

#### Project Directory Structure

The project separates scripts, documentation, and screenshots. Local datasets, environment files, and secrets are excluded from version control.

![Project Structure](screenshots/02-project-structure.png)

### Phase 2: Azure Storage Provisioning

**Status: Completed**

Provisioned an Azure Storage Account and configured containers to support the Bronze and Silver stages of the pipeline.

The storage design separates raw input data from transformed output data, allowing each stage to be managed and validated independently.

#### Azure Storage Account

![Azure Storage Account](screenshots/03-azure-storage-account.png)

### Phase 3: Data Ingestion into Bronze

**Status: Completed**

Developed the Python ingestion script:

`scripts/upload_raw_data.py`

The script uses the Azure Blob Storage SDK to read the source CSV from the local development environment and upload it into the Bronze container.

#### Successful Python Upload

The terminal output confirmed that the source file was uploaded successfully.

![Python Blob Upload Success](screenshots/04-python-blob-upload-success.png)

#### Bronze Layer Verification

Verified that the uploaded dataset was present in Azure Blob Storage.

![Azure Blob Storage Verification](screenshots/05-azure-blob-storage-verification.png)

#### Version Control

Used Git to track project changes and publish the implementation to GitHub.

![GitHub Repository Push](screenshots/06-github-repository-push.png)

### Phase 4: Data Transformation into Silver

**Status: Completed**

Developed the transformation script:

`scripts/transform.py`

The script uses Polars to process the source CSV dataset.

Transformation operations include:

- Selecting relevant service request fields.
- Standardising column names to snake_case.
- Writing the selected data into Parquet format.
- Applying Snappy compression.
- Uploading the transformed dataset to the Silver container.

Selected fields include request creation and closure dates, problem classifications, location details, borough, latitude, and longitude.

#### Successful Python Transformation

The transformation script completed successfully and wrote the Parquet dataset to Azure Blob Storage.

![Python Data Transformation Success](screenshots/07-python-data-transformation-success.png)

#### Silver Layer Verification

Verified that the transformed Parquet file was available in the Silver container.

![Azure Silver Layer Verification](screenshots/08-azure-silver-layer-verification.png)

### Phase 5: PostgreSQL Gold Layer

**Status: Completed**

Provisioned Azure Database for PostgreSQL and connected to the database using pgAdmin.

Created the `gold` schema and `gold.urban_city_requests` table to receive transformed service request records.

The destination table includes:

| Column | PostgreSQL Type |
|---|---|
| created_date | TIMESTAMP |
| closed_date | TIMESTAMP |
| problem | TEXT |
| problem_detail | TEXT |
| location_type | VARCHAR(100) |
| incident_address | TEXT |
| city | VARCHAR(100) |
| borough | VARCHAR(100) |
| latitude | DOUBLE PRECISION |
| longitude | DOUBLE PRECISION |

#### Gold Table Creation

Created the destination schema and table using SQL.

![PostgreSQL Gold Table Creation](screenshots/09-postgresql-gold-table-creation.png)

#### Gold Table Verification

Verified the destination table structure in PostgreSQL.

![PostgreSQL Gold Table Verification](screenshots/10-postgresql-gold-table-verification.png)

#### Gold Data Verification

Reviewed the destination database using pgAdmin.

![PostgreSQL Gold Data Verification](screenshots/11-postgresql-gold-data-verification.png)

### Phase 6: Azure Data Factory Silver-to-Gold Pipeline

**Status: Completed**

Created an Azure Data Factory pipeline named:

`UrbanCityDataFactoryPipeline`

Configured linked services and datasets for Azure Blob Storage and Azure Database for PostgreSQL.

The Copy activity, `SilverToGoldPipeline`, reads the transformed Parquet file and writes records into the Gold-layer PostgreSQL table.

#### Successful Pipeline Execution

The Azure Data Factory monitoring interface reported a successful pipeline run.

![Azure Data Factory Pipeline Success](screenshots/12-adf-silver-to-gold-pipeline-success.png)

#### Copy Activity Performance

The Copy activity produced the following results:

| Metric | Result |
|---|---|
| Execution status | Succeeded |
| Source | Azure Blob Storage |
| Destination | Azure Database for PostgreSQL |
| Files read | 1 |
| Rows read | 654,156 |
| Rows written | 654,156 |
| Source data read | 347.295 MB |
| Destination data written | 123.627 MB |
| Copy duration | 30 seconds |
| Reported throughput | 15.1 MB/s |

The activity reported matching source and destination row counts. Independent data consistency verification was not enabled for this execution.

![Azure Data Factory Copy Activity Performance](screenshots/13-adf-copy-activity-performance.png)

### Phase 7: Apache Airflow Local Environment

**Status: Environment Running; DAG Development Pending**

Installed Astronomer Astro CLI using Homebrew and updated the CLI to version 1.47.0.

Used Astro CLI and Docker to start Apache Airflow locally.

#### Astro CLI Startup

The local environment was started successfully using the Astro CLI.

![Astro CLI Airflow Startup](screenshots/14-astro-cli-airflow-startup.png)

#### Apache Airflow Dashboard

Accessed the Airflow web interface and confirmed that the metadata database, scheduler, triggerer, and DAG processor were healthy.

No active project DAGs had been configured at this stage.

![Apache Airflow Local Dashboard](screenshots/15-apache-airflow-local-dashboard.png)

## 6. Challenges and Lessons Learned

### Cross-Platform Python Environments

Learned that Python virtual environment activation commands differ between operating systems.

**Windows PowerShell:**

```powershell
.\environ\Scripts\Activate.ps1
```

**macOS / Linux:**

```bash
source environ/bin/activate
```

### Python Debugging

Encountered and resolved indentation errors, inconsistent function names, and unsupported arguments while developing ingestion and transformation scripts.

This strengthened my understanding of Python debugging and library APIs.

### Azure Blob Storage Integration

Learned how to organise cloud storage containers, configure application access, upload files programmatically, and verify stored datasets.

### PostgreSQL Network Connectivity

Troubleshot connectivity problems caused by network restrictions and PostgreSQL firewall configuration.

Resolved the issue by adjusting the network connection and permitted client IP address.

### Azure Data Factory Configuration

Encountered a PostgreSQL relation resolution error while configuring the destination dataset.

Corrected the destination schema and table mapping, then successfully executed the Copy activity.

### Pipeline Validation

Used Azure Data Factory execution metrics to inspect row counts, data volumes, execution duration, and reported throughput.

The experience highlighted the difference between a successful Copy activity and independently verifying data completeness and quality.

## 7. Next Steps

- Develop an Apache Airflow DAG for pipeline orchestration.
- Explore scheduling and task dependencies.
- Add data quality checks and independent row-count validation.
- Improve monitoring, logging, and error handling.
- Test end-to-end orchestration.
- Develop SQL queries for operational analysis.
- Finalise the architecture diagram and portfolio documentation.

## 8. Skills Demonstrated

- Microsoft Azure cloud infrastructure.
- Azure Blob Storage.
- Python scripting and debugging.
- Polars data transformation.
- CSV-to-Parquet processing.
- Medallion Architecture.
- Azure Data Factory pipeline development.
- Azure Database for PostgreSQL.
- SQL schema and table creation.
- Cloud connectivity troubleshooting.
- Docker-based development.
- Apache Airflow local setup.
- Git and GitHub version control.
- Technical documentation and implementation evidence.

## 9. Project Status

**CIVIC PULSE is an active data engineering portfolio project.**

The Bronze ingestion, Silver transformation, PostgreSQL Gold table, and Azure Data Factory Silver-to-Gold pipeline have been implemented.

The Azure Data Factory Copy activity successfully reported 654,156 records read and written.

Apache Airflow is running locally, and DAG-based orchestration remains the next development phase.

---

**Built as a practical cloud and data engineering learning project, with a focus on reproducible implementation, troubleshooting, and technical documentation.**