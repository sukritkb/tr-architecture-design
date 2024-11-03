# Data Architecture on Azure

This repository provides an overview of a data architecture solution built on Azure, utilising multiple Azure services for data ingestion, processing, storage, analytics, machine learning, and monitoring. Each Azure tool is selected to fulfill a specific role in the architecture based on its specialised capabilities and role as a best-practice solution in Azure's ecosystem.

---

## Table of Contents

1. [Architecture Overview](#architecture-overview)
2. [Component Details](#component-details)
   - [Azure Data Factory](#azure-data-factory)
   - [Storage Accounts](#storage-accounts)
   - [Synapse Analytics](#synapse-analytics)
   - [Azure Machine Learning](#azure-machine-learning)
   - [Application Insights](#application-insights)
   - [Cosmos DB](#cosmos-db)
   - [Event Hubs](#event-hubs)
3. [Why These Azure Tools?](#why-these-azure-tools)

---

## 1. Architecture Overview

The primary components of this Azure-based data architecture are as follows:

1. **Azure Data Factory** - Acts as the main orchestrator for data workflows.
2. **Storage Accounts** - Serves as the data lake, storing raw, transformed, and processed data.
3. **Synapse Analytics** - Provides advanced analytics, data warehousing, and compute resources for large-scale data processing.
4. **Azure Machine Learning** - Used to train and deploy machine learning models on the data.
5. **Application Insights** - Handles telemetry and logging, providing deep insights into application and pipeline performance.
6. **Cosmos DB** - Serves as a source for real-time data ingestion for specific jobs.
7. **Event Hubs** - Manages the streaming of Change Data Capture (CDC) events from OLTP systems.

Each component has a dedicated role, providing an efficient, scalable, and robust data architecture on Azure.

---

## 2. Component Details

### 2.1 Azure Data Factory

**Purpose:** Azure Data Factory (ADF) is the orchestrator of the architecture, managing workflows and data movement between components.

- **Key Features:** 
  - Supports a range of data ingestion, transformation, and loading (ETL) activities.
  - Integrates seamlessly with Azure and third-party data sources.
  - Provides monitoring and automation, making it an ideal choice for managing complex workflows.

### 2.2 Storage Accounts

**Purpose:** Storage Accounts in Azure are used to build a common data lake that holds both raw and processed data in different containers.

- **Key Features:** 
  - Highly scalable storage with tiered data access (hot, cool, and archive).
  - Blobs and data lake capabilities make it suitable for storing unstructured and structured data.
  - Integration with ADF, Synapse, and Azure ML for seamless data access across stages.

### 2.3 Synapse Analytics

**Purpose:** Synapse Analytics is the compute and warehousing component, ideal for large-scale data processing and analytics.

- **Key Features:**
  - Combines big data and data warehousing capabilities in a single platform.
  - Spark and SQL pools support both batch processing and on-demand querying.
  - Scalable compute options ensure performance efficiency even with high-volume workloads.

### 2.4 Azure Machine Learning

**Purpose:** Azure Machine Learning provides the necessary tools to train, validate, and deploy machine learning models.

- **Key Features:**
  - Integrated with various data sources on Azure, simplifying data preparation and feature engineering.
  - Supports MLOps (Machine Learning Operations) for deploying, monitoring, and managing models in production.
  - AutoML, experimentation, and model registries accelerate the ML lifecycle.

### 2.5 Application Insights

**Purpose:** Application Insights is used for telemetry and logging, tracking application health and diagnosing issues.

- **Key Features:**
  - Provides live metrics, customisable alerts, and logging for deep insights.
  - Integrates with ADF, Synapse, and other services for unified monitoring across the architecture.
  - Facilitates root cause analysis, optimising pipeline performance and minimising downtime.

### 2.6 Cosmos DB

**Purpose:** Cosmos DB is a NoSQL database designed for real-time data processing and event-driven jobs.

- **Key Features:**
  - Globally distributed, low-latency storage with multi-region replication.
  - Change Feed capabilities allow for real-time data updates, suitable for streaming and reactive workflows.
  - Flexibility in data modeling, with support for document, key-value, graph, and columnar data models.

### 2.7 Event Hubs

**Purpose:** Event Hubs is used for streaming Change Data Capture (CDC) events from OLTP systems, providing real-time insights.

- **Key Features:**
  - Capable of handling millions of events per second, ideal for high-throughput scenarios.
  - Integrates with ADF, Synapse, and Cosmos DB, ensuring data flows seamlessly across the architecture.
  - Reliable ingestion pipeline for real-time analytics, IoT data, and event-driven architectures.

---

## Why These Azure Tools?

All of the tools chosen are the go-to solutions in Azure for their respective use cases, as they provide optimal performance, scalability, and integration within the Azure ecosystem. This architecture leverages these tools to build a reliable, high-performance, and fully managed solution for data processing, storage, and analytics.


## Architecture Diagrams

### High Level Architecture Diagram
![High Level Architecture Diagram](./drawio/high_level_architecture_design.png "High Level Architecture Diagram")

### Sample Data Ingestion Pipeline

Let’s consider an example of a source-aligned data application. In this context, ***"source-aligned"*** refers to applications where the data originates from an external source rather than being stored in the data lake. On the other hand, ***"curated data applications"*** takes a dataset already in the data lake—whether from another source-aligned product or a curated data product—and transform it into a new dataset. 

This code snippet is a direct conversion to terraform of the JSON template used in the Azure Data Factory's copy data activity. The official documentation [link here](https://learn.microsoft.com/en-us/azure/data-factory/copy-activity-overview)

```
resource "azurerm_data_factory_pipeline" "example_pipeline" {
  name                = "example_pipeline"
  resource_group_name = "your-resource-group"
  data_factory_name   = "your-data-factory-name"

  activity {
    name = "CopyActivityTemplate"
    type = "Copy"

    inputs = [
      {
        reference_name = "<source dataset name>"
        type           = "DatasetReference"
      }
    ]

    outputs = [
      {
        reference_name = "<sink dataset name>"
        type           = "DatasetReference"
      }
    ]

    type_properties = jsonencode({
      source = {
        type       = "<source type>"
        # Add specific source properties here, e.g., "sqlTable": "your_table_name"
      }
      sink = {
        type       = "<sink type>"
        # Add specific sink properties here, e.g., "sinkType": "AzureBlob"
      }
      translator = {
        type           = "TabularTranslator"
        columnMappings = "<column mapping>"
      }
      dataIntegrationUnits        = <number>
      parallelCopies              = <number>
      enableStaging               = <true_or_false>
      stagingSettings             = {
        # Define staging properties if enableStaging is true
      }
      enableSkipIncompatibleRow   = <true_or_false>
      redirectIncompatibleRowSettings = {
        # Define redirect settings if enableSkipIncompatibleRow is true
      }
    })
  }
}
```
**Ingestion Data Application**
![Ingestion Data Application](./drawio/sample_ingestion.png "Ingestion Data Application")