# Milestone Two (M2) - HIP Data Sharing

**Context**\
Milestone Two (M2) extended AMRIT’s ABDM integration from being an identity enabler (M1) to a Health Information Provider (HIP). At this stage, AMRIT could generate and share patient health records into the ABDM ecosystem with consent.

This milestone was implemented in the `.NET` HIP Service (`src/In.ProjectEKA.HipService`). Core responsibilities included link-token based care-context linking (v3), discovery, FHIR bundle generation, notify → dataTransfer flows, and sandbox certification.

### 1. Objectives of M2

* Implement v3 **link-token based care-context linking**.
* Store and manage link tokens in Postgres (`InitiatedLinkRequest`, `GenerateTokenResponse`) and Mongo (`GenerateLinkTokenResponse`).
* Build FHIR bundles from AMRIT visit data (`FHIRResourceServiceImpl`).
* Implement **Quartz scheduler pipeline** for trigger → bundle generation.
* Expose **discovery APIs** via `PatientController`.
* Handle full consent lifecycle: notify → dataTransfer (via `DataFlowController`).
* Validate bundles against NRCeS FHIR IG.
* Pass ABDM sandbox functional certification for HIP role.

### 2. Architecture & System Context

#### Key Modules & Services

* **LinkController.cs** / **LinkCareContextVersion3Controller.cs**\
  Handles `/generateToken` callbacks, link initiation, and link confirmation flows.
* **PatientController.cs**\
  Exposes discovery APIs, queries Postgres + Mongo, returns care contexts.
* **DataFlowController.cs**\
  Handles `/health-information/request`, `/health-information/{id}`, orchestrates notify → dataTransfer lifecycle.
* **FHIRResourceServiceImpl**\
  Maps AMRIT visit data to NRCeS FHIR resources.
* **Quartz Scheduler**\
  Polls trigger table `ndhm_trigger_visit_data`, enqueues bundle generation.

#### Persistence

* **Postgres (hipservice)** — link requests, discovery, data-flow, health information.
* **MongoDB (AmritHRP)** — generated FHIR bundles, ABDM responses, patient-care contexts.
* **Redis** — caches link-tokens (3-month TTL).

### 3. v3 Care Context Linking

**Flow**

1. AMRIT → Gateway: `/generateToken` with ABHA + demographics.
2. Gateway → AMRIT: `{ linkToken, abhaAddress }`
   * Saved in Postgres (`InitiatedLinkRequest`, `GenerateTokenResponse`)
   * Persisted in Mongo (`GenerateLinkTokenResponse`).
3. Link token cached in Redis (\~90 days).
4. AMRIT → Gateway: `/linkCareContext` with `visitCode + hiTypes`.
5. Gateway → AMRIT (callback): on-link confirmed → persisted in:
   * Postgres `CareContext` table
   * Mongo `PatientCareContexts`.

**Error Handling**

* Demographic mismatch → rejected at generateToken → surfaced in UI.
* Expired token → retried by calling `/generateToken` fresh.

### 4. Database Entities

#### Postgres Tables (via EF Core migrations)

* **InitiatedLinkRequest**
  * `request_id`, `txn_id`, `link_reference_number`, `status`, `created_at`
* **CareContext**
  * `care_context_number`, `link_reference_number`, `visit_code`, `display`, `facility_id`
* **DiscoveryRequest**
  * `transaction_id`, `consent_manager_user_id`, `patient_reference_number`, `timestamp`
* **DataFlowRequest**
  * `transaction_id`, `health_information_request`, `status`
* **HealthInformation**
  * `information_id`, `data`, `token`, `created_at`

#### MongoDB Collections (`src/In.ProjectEKA.DbLayer`)

* **GenerateLinkTokenResponse** → persists `/generateToken` responses
* **NDHMResponse** → all ABDM callbacks (init, confirm, notify, ack)
* **PatientCareContexts** → linked contexts, used in discovery
* **Amrit\_Resource** → generated FHIR bundles

### 5. Scheduler Pipeline (Quartz)

**Flow**

1. New visit inserted into `ndhm_trigger_visit_data`.
2. Quartz job polls unprocessed rows.
3. Job enqueues bundle generation.
4. Worker calls `FHIRResourceServiceImpl` → builds bundle.
5. Bundle saved in Mongo (`Amrit_Resource`), `process_flag` updated in trigger table.

**Performance**

* Bundle gen time \~2–3s per visit.
* Quartz misfires handled; failures retried with backoff.

### 6. FHIR Bundle Generation

**Resource Mapping**

* OP Consult → `Encounter`, `Composition`, `Condition`, `Observation`
* Prescription → `MedicationRequest`
* Lab Order → `DiagnosticReport`, `Observation`
* Discharge Summary → `Composition`
* Practitioner → `Practitioner`, `Organization`

**Validation**

* All bundles validated against **NRCeS FHIR IG**.
* Invalid bundles persisted in Mongo (flagged `invalid=true`).

**Example (simplified)**

```json
{
  "resourceType": "Bundle",
  "id": "VISIT-20250923-001",
  "type": "document",
  "entry": [
    {
      "resource": {
        "resourceType": "Encounter",
        "id": "enc-123",
        "status": "finished",
        "class": { "code": "AMB" }
      }
    }
  ]
}

```

### 7. Discovery API

**HIP Discovery Flow**

* HIU → Gateway: discovery request (ABHA address).
* Gateway → AMRIT HIP: forwards discovery.
* AMRIT HIP → Postgres (`CareContext`, `DiscoveryRequest`) + Mongo (`PatientCareContexts`).
* Response sent back with list of contexts.

**Sample Response**

```json
{
  "careContexts": [
    {
      "referenceNumber": "VISIT-20250923-001",
      "display": "OP Visit Sep 23"
    }
  ]
}

```

### 8. Consent → Notify → DataTransfer

**Lifecycle**

1. HIU initiates consent (Gateway).
2. Patient approves → consent artefact issued.
3. ABDM → HIP: `/notify` callback.
4. HIP looks up DataFlowRequest + bundles (Mongo).
5. Bundles encrypted (`Encryptor.cs`, session key infra) → pushed to `/dataTransfer`.
6. ABDM acknowledges → saved in `NDHMResponse`.

**Encryption**

* Asymmetric key exchange: patient key used for payload.
* Session keys managed via Vault.

**Error Handling**

* Missing bundles → logged.
* Encryption errors → retried via Quartz job.
* Delayed notify callbacks → replayed from Mongo.

### 9. Error Handling & Edge Cases

* **Demographic mismatch** — flagged at `/generateToken`.
* **Rate limit** — sandbox limited generateToken (\~3/day); Redis cache used.
* **Scheduler lag** — Quartz misfire handling in place.
* **Invalid bundles** — persisted in Mongo but flagged `invalid`.
* **Callback failures** — persisted in `NDHMResponses` for replay.

### 10. Repo / Service Mapping

**Controllers**

* `LinkController.cs` → link initiation, confirmation
* `LinkCareContextVersion3Controller.cs` → `/generateToken` callbacks
* `DataFlowController.cs` → health-information request/serve
* `PatientController.cs` → discovery

**Services**

* `CareContextService` → linking, discovery
* `FHIRResourceServiceImpl` → bundle generation
* `DataTransferService` → notify → dataTransfer orchestration

**DB**

* **Postgres**: InitiatedLinkRequest, CareContext, DiscoveryRequest, DataFlowRequest, HealthInformation
* **Mongo**: GenerateLinkTokenResponse, NDHMResponse, PatientCareContexts, Amrit\_Resource
* **Redis**: link-token cache
