# Elasticsearch Sync Operations Guide

This document outlines the steps to manage Elasticsearch (ES) indexes, perform health checks, initiate data sync, and verify data integrity for the `beneficiary_index`.

<div align="left"><figure><img src="../../.gitbook/assets/Screenshot from 2026-01-13 22-45-47.png" alt=""><figcaption></figcaption></figure></div>

1[^1]. C**reate Index**

Create a new index with full mapping:

```
curl -X POST "identity-api/elasticsearch/index/create" \
  -H "Content-Type: application/json"
```

**Verify the mapping:**

{% code overflow="wrap" %}
```
curl -u "elastic:<password>" -X GET "http://localhost:9200/beneficiary_index/_mapping?pretty"
```
{% endcode %}

### **2. Check Elasticsearch Index**

Verify that the index exists and view basic details:

```
curl -u "elastic:<password>" -X GET "http://localhost:9200/beneficiary_index"
```

### 3. **Health Check of Elasticsearch Sync Service**

Check if the Elasticsearch Sync Service is running properly:

```
curl -X GET /identity-api/elasticsearch/health
```

**Sample Response:**

{% code overflow="wrap" %}
```
{ "status": "UP", "service": "Elasticsearch Sync Service", "asyncJobsRunning": false, "activeJobs": 0 }
```
{% endcode %}

* **status:** Indicates service status (`UP` or `DOWN`)
* **asyncJobsRunning:** `true` if a background sync job is running
* **activeJobs:** Number of currently active jobs

### 4. **Start Asynchronous Full Sync**

Start a full sync in the background:

{% code overflow="wrap" %}
```
curl -X POST "identity-api/elasticsearch/start?triggeredBy=admin"
```
{% endcode %}

**Sample Response:**

```
{
  "status": "success",
  "message": "Sync job started in background",
  "jobId": 1,
  "jobStatus": "PENDING",
  "checkStatusUrl": "/elasticsearch/status/1"
}
```

### 5. **Check Sync Job Progress**

Check the status of a running sync job:

```
curl -X GET identity-api/elasticsearch/status/{jobId}
```

**Sample Response:**

```
{
  "jobId": 1,
  "jobType": "FULL_SYNC",
  "status": "RUNNING",
  "totalRecords": 1000000,
  "processedRecords": 45000,
  "successCount": 44950,
  "failureCount": 50,
  "progressPercentage": "4.50",
  "processingSpeed": 125.5,
  "estimatedTimeRemaining": 7610,
  "startedAt": "2025-12-05T10:30:00",
  "completedAt": null,
  "errorMessage": null
}
```

* **progressPercentage:** % of total records processed
* **processingSpeed:** Records processed per second
* **estimatedTimeRemaining:** In seconds

### 6. **Verify Data in Elasticsearch**

**Count documents in an index:**

{% code overflow="wrap" fullWidth="true" expandable="true" %}
```
curl -u "elastic:<password>" -X GET "http://localhost:9200/beneficiary_index/_count?pretty"
```
{% endcode %}

**Search sample documents:**

{% code overflow="wrap" fullWidth="true" expandable="true" %}
```
curl -u "elastic:<password>" -X GET "http://localhost:9200/beneficiary_index/_search?size=25&pretty"
```
{% endcode %}

**Retrieve all indexes:**

```
http://localhost:9200/_cat/indices?v
```

### 7. **Stop a Running Sync Job**

```
curl -X POST identity-api/elasticsearch/cancel/{jobId}
```

### 8. **Delete an Elasticsearch Index**

```
curl -u "elastic:<password>" -X DELETE "http://localhost:9200/beneficiary_index"
```

### 9. **Resume the Job**

```
curl -X POST identity-api/elasticsearch/resume/{jobId}
```

### 10. **Elasticsearch Index Refresh**

```
curl -X POST identity-api/elasticsearch/refresh
```

[^1]: 
