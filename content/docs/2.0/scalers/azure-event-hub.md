+++
title = "Azure Event Hubs"
layout = "scaler"
availability = "v1.0+"
maintainer = "Community"
description = "Scale applications based on Azure Event Hubs."
notice = "As of now, the Event Hub scaler only supports reading checkpoints from Blob Storage, as well as scaling only Event Hub applications written in C#, Java, Python or created with Azure Functions."
go_file = "azure_eventhub_scaler"
+++

### Trigger Specification

This specification describes the `azure-eventhub` trigger for Azure Event Hubs.

```yaml
triggers:
- type: azure-eventhub
  metadata:
    connectionFromEnv: EVENTHUB_CONNECTIONSTRING_ENV_NAME # Connection string for Event Hub namespace appended with "EntityPath=<event_hub_name>"
    storageConnectionFromEnv: STORAGE_CONNECTIONSTRING_ENV_NAME # Connection string for account used to store checkpoint. As of now the Event Hub scaler only reads from Azure Blob Storage.
    consumerGroup: $Default # Optional. Consumer group of event hub consumer. Default: $Default
    unprocessedEventThreshold: '64' # Optional. Target number of unprocessed events across all partitions in Event Hub for HPA. Default: 64 events.
    blobContainer: 'name_of_container' # Optional. Container name to store checkpoint. This is needed when a using an Event Hub application written in dotnet or java, and not an Azure function.
```

The `connectionFromEnv` value is the name of the environment variable your deployment uses to get the Event Hub connection string which is appended with the Event Hub name using Entity Path variable. storageConnectionFromEnv` is the name of the environment variable your deployment uses to get the Storage connection string.

Environment variables are usually resolved from a `Secret V1` or a `ConfigMap V1` collections. `env` and `envFrom` are both supported.

### Authentication Parameters

Not supported yet.

### Example

```yaml
apiVersion: keda.sh/v1alpha1
kind: ScaledObject
metadata:
  name: azure-eventhub-scaledobject
  namespace: default
spec:
  scaleTargetRef:
    name: azureeventhub-function
  triggers:
  - type: azure-eventhub
    metadata:
      # Required
      connectionFromEnv: EventHub
      storageConnectionFromEnv: AzureWebJobsStorage
      # Optional
      consumerGroup: $Default # default: $Default
      unprocessedEventThreshold: '64' # default 64 events.
      blobContainer: ehcontainer
```
