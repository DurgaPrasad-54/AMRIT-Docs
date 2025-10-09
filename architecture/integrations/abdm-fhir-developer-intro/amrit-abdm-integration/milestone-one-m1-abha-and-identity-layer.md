# Milestone One (M1) - ABHA & Identity Layer

**Context**\
Milestone One (M1) was the starting point of AMRIT’s ABDM integration. In this milestone, we built and validated the ABHA identity flows: creating and verifying ABHAs (Ayushman Bharat Health Accounts), mapping them to AMRIT beneficiaries, and implementing the sandbox’s required v0.5 OTP-based care context linking.

The implementation was delivered within AMRIT’s **FHIR-API module**, with persistence across **MySQL/Postgres** and **MongoDB**. OTP tracking and linking were also added for sandbox compliance.

### 1. Objectives of M1

* Register AMRIT facility and services in ABDM sandbox.
* Set up secure session management with ABDM Gateway.
* Implement ABHA creation & verification APIs (QR, Aadhaar-assisted, manual).
* Map ABHA ↔ AMRIT beneficiary records.
* Implement the v0.5 OTP flow for care context linking (for sandbox compliance).

### 2. Architecture & System Context

**Modules & Services**

* `HealthIDServiceImpl` → Implements ABHA creation & verification (methods like `ndhmGenerateOTP`, `ndhmCreateHealthID`, `ndhmVerifyHealthID`).
* `ABHAServiceImpl` → Orchestrates Aadhaar-assisted enrollment flows.
* `CareContextService` → Implements v0.5 OTP care-context linking (`generateOTPForCareContext`, `addCareContext`).

**Entities**

* `HealthIDResponse` → Maps to relational table `t_healthid`.
* `BenHealthIDMapping` → Handles beneficiary ↔ ABHA mapping (prevents duplicates).

**Tech stack**

* Spring Boot REST APIs
* Quartz scheduler (integrated here; extended in M2 for bundle generation)
* MySQL/Postgres (ABHA identities, link requests, OTP records)
* MongoDB (ABDM callbacks & sandbox payloads)
* Redis (introduced here for token caching; heavier usage in M2)

### 3. Sandbox Registration

* AMRIT facility registered in **HFR (Health Facility Registry)**.
* HIP and HIU IDs provisioned via sandbox portal.
* Sandbox credentials (`CLIENT_ID`, `CLIENT_SECRET`) stored in `common_ci.properties`.
* `X-CM-ID` set to `sbx` (sandbox).

### 4. Session Management

* API: `POST /sessions` → retrieves JWT session token (valid \~15 min).
* Tokens managed via `fire-api` module (automatic refresh + retry).
* Required headers in every call:
  * `Authorization: Bearer <session-token>`
  * `X-Request-ID: <uuid>`
  * `X-HIP-ID: <AMRIT_HIP_ID>`
  * `X-HIU-ID: <AMRIT_HIU_ID>`
  * `X-CM-ID: sbx`
  * `Content-Type: application/json`

### 5. ABHA Creation & Verification

**Implemented Flows**

* **QR Scan Flow**: Scan ABHA QR → verify via `/search` & `/verify`.
* **Manual Entry Flow**: Enter ABHA manually → verified.
* **Aadhaar-Assisted Flow**: Enter Aadhaar → ABDM generates ABHA → OTP verified.

**Service Mapping**

* `HealthIDServiceImpl` → OTP generation & creation APIs.
* `ABHAServiceImpl` → Aadhaar-assisted OTP flows.

**Transaction ID Usage**

* Each `/generateOtp`, `/verifyOtp`, `/createHealthID` call included a `txnId`.
* Persisted in `HealthIDResponse.txnId` to tie together multi-step flows.
* Provided auditability across retries and callbacks.

### 6. Database Entities

#### Relational (Postgres/MySQL)

* **`OtpRequests`** (otpservice schema)
  * Tracks OTP lifecycle (ABHA creation, OTP-based linking).
  * Key fields: `request_id`, `abha_number`, `otp_sent_to`, `status`, `created_at`.
* **`InitiatedLinkRequest`** (hipservice schema)
  * Tracks care-context link initiation in v0.5 OTP flow.
  * Fields: `request_id`, `txn_id`, `link_reference_number`, `status`, `created_at`.
* **`t_healthid`**
  * Stores ABHA profiles returned after verification.
  * Populated via `HealthIDResponse` entity.
* **`m_benhealthidmapping`**
  * Maps `beneficiaryRegId` ↔ `healthIdNumber`.
  * Prevents duplicate ABHA registrations.

#### MongoDB (AmritHRP)

* **`GenerateTokenAbdmResponses`**
  * Stores sandbox responses to `/generateToken` requests.
  * Includes link token, ABHA address, txnId.
* **`PatientCareContexts`**
  * Persists ABHA ↔ care-context mappings established via OTP linking.
* **`NDHMResponses`**
  * Stores raw sandbox callbacks for audit/debugging.

### 7. API Endpoints (FHIR-API)

* `/healthID/generateOTP`
* `/healthID/verifyOTPAndGenerateHealthID`
* `/healthID/getBenhealthID`
* `/healthID/getBenIdForhealthID`
* `/healthIDRecord/mapHealthIDToBeneficiary`
* `/healthIDRecord/addHealthIdRecord`

These were exposed via `HealthIDController` and implemented in `HealthIDServiceImpl`.

### 8. v0.5 OTP Care Context Linking

**Flow**

1. `CareContextService.generateOTPForCareContext()` → calls ABDM `/init`.
2. ABDM sends OTP → logged in `OtpRequests`.
3. User verifies OTP → `CareContextService.addCareContext()` attaches context.
4. ABDM confirms success → persisted in Mongo (`PatientCareContexts`).

**Example Init Callback**

```json
{
  "requestId": "uuid-123",
  "timestamp": "2025-09-22T10:00:00Z",
  "transactionId": "txn-12345",
  "abhaNumber": "ABHA-987654321000"
}

```

### 9. Error Handling & Edge Cases

* **Duplicate ABHA**: Prevented via `existsByHealthIdNumber`.
* **Existing ABHA**: Linked instead of recreated.
* **OTP Blocking**: Sandbox blocked after \~3 OTP/day → caching + retry logic.
* **TxnId persistence**: Always stored to avoid orphaned flows.
* **Callback failures**: Logged into `NDHMResponses` (Mongo) for replay/debug.

### 10. Repo / Service Mapping

* **Services**:
  * `HealthIDServiceImpl` → OTP, create, verify.
  * `ABHAServiceImpl` → Aadhaar-assisted flows.
  * `CareContextService` → OTP linking.
* **Entities**:
  * `HealthIDResponse` → table `t_healthid`.
  * `BenHealthIDMapping` → ABHA ↔ beneficiary mapping (`m_benhealthidmapping)`.
* **Modules**:
  * `fhir-api` → Gateway session management.
  * `otpservice` → OTP tracking (Postgres).
  * `hipservice` → Link initiation tracking (Postgres).
  * `AmritHRP` → ABDM responses, tokens, care contexts (Mongo).
  * `Redis` → caching of session/OTP data.
