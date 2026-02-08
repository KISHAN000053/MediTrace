# Healthcare Record System - Design Document

## Overview

The Healthcare Record System is an offline-first, privacy-first mobile application that enables patients to maintain a unified medical history on their personal devices. The system uses a decentralized architecture where patient data is stored locally on the patient's device with optional encrypted backup to cloud storage. Healthcare providers (doctors, pharmacists, lab technicians) access patient data through QR-code-based consent mechanisms with automatic session expiry. The system operates without a central server, ensuring data sovereignty and privacy compliance while supporting low-end devices in rural areas with intermittent internet connectivity.

### Key Design Principles

1. **Offline-First**: All core functionality works without internet connectivity
2. **Privacy-First**: Patient data never leaves the patient's device without encryption
3. **Decentralized**: No central server dependency; data sovereignty remains with the patient
4. **Accessibility**: Large buttons, high-contrast UI, simple navigation for low-end devices
5. **Minimal Resource Usage**: Optimized for devices with 1 GB RAM and older OS versions

## Architecture

### System Components

```
┌─────────────────────────────────────────────────────────────┐
│                    Mobile Application                        │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │              Role-Based UI Layer                      │   │
│  │  ┌─────────────┬──────────────┬──────────────────┐   │   │
│  │  │   Patient   │    Doctor    │   Pharmacist     │   │   │
│  │  │   Interface │  Interface   │   Interface      │   │   │
│  │  └─────────────┴──────────────┴──────────────────┘   │   │
│  │                                                        │   │
│  │  ┌──────────────────────────────────────────────┐    │   │
│  │  │      Lab Technician Interface                │    │   │
│  │  └──────────────────────────────────────────────┘    │   │
│  └──────────────────────────────────────────────────────┘   │
│                           ▲                                   │
│                           │                                   │
│  ┌────────────────────────┴────────────────────────────┐    │
│  │         Application Logic Layer                      │    │
│  │  ┌──────────────────────────────────────────────┐   │    │
│  │  │  Record Management │ Consent Management      │   │    │
│  │  │  Barcode Scanning  │ Session Management      │   │    │
│  │  │  PDF Handling      │ Sync Management         │   │    │
│  │  └──────────────────────────────────────────────┘   │    │
│  └────────────────────────┬────────────────────────────┘    │
│                           │                                   │
│  ┌────────────────────────┴────────────────────────────┐    │
│  │         Data Access Layer                           │    │
│  │  ┌──────────────────────────────────────────────┐   │    │
│  │  │  Encryption/Decryption │ Database Access    │   │    │
│  │  │  Backup Sync           │ Cache Management   │   │    │
│  │  └──────────────────────────────────────────────┘   │    │
│  └────────────────────────┬────────────────────────────┘    │
│                           │                                   │
│  ┌────────────────────────┴────────────────────────────┐    │
│  │         Storage Layer                               │    │
│  │  ┌──────────────────────────────────────────────┐   │    │
│  │  │  Local SQLite DB │ Encrypted File Storage   │   │    │
│  │  │  Cache Storage   │ Backup Queue             │   │    │
│  │  └──────────────────────────────────────────────┘   │    │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
└─────────────────────────────────────────────────────────────┘
                           │
                           │ (Optional, Encrypted)
                           ▼
                    Google Drive Backup
```

### Data Flow

**Patient Adding a Record (Offline)**:
1. Patient enters data through role-specific UI
2. Application Logic validates and processes data
3. Data Access Layer encrypts data
4. Storage Layer persists to local SQLite database
5. If backup enabled, record is queued for sync

**Healthcare Provider Accessing Data**:
1. Provider scans patient's QR code
2. Consent Management validates token and creates session
3. Session Management grants time-limited access
4. Provider views cached or locally-stored patient data
5. Provider adds records (prescriptions, notes, medicines)
6. Records are stored locally on provider's device
7. On next patient sync, provider's records are transferred to patient's device

**Backup and Sync**:
1. When internet connectivity is restored
2. Sync Manager checks for pending backup operations
3. Data Access Layer encrypts records using patient's encryption key
4. Backup operations are executed to Google Drive
5. Sync status is updated in local database

## Components and Interfaces

### 1. Patient Record Store

**Responsibility**: Persist and retrieve patient medical records from local storage

**Key Operations**:
- `addRecord(record: MedicalRecord): void` - Add new record to storage
- `getRecords(filters?: RecordFilter): MedicalRecord[]` - Retrieve records with optional filtering
- `updateRecord(id: string, updates: Partial<MedicalRecord>): void` - Update existing record
- `deleteRecord(id: string): void` - Delete record from storage
- `getAllRecords(): MedicalRecord[]` - Retrieve all records in chronological order

**Storage Details**:
- Uses SQLite for structured data (prescriptions, medicines, visit notes)
- Uses encrypted file storage for PDFs and binary data
- Implements indexing on date fields for efficient chronological retrieval
- Supports pagination for large datasets

### 2. Encryption Manager

**Responsibility**: Handle encryption and decryption of sensitive data

**Key Operations**:
- `generateEncryptionKey(): EncryptionKey` - Generate patient-controlled encryption key
- `encryptData(data: string, key: EncryptionKey): EncryptedData` - Encrypt data using AES-256
- `decryptData(encrypted: EncryptedData, key: EncryptionKey): string` - Decrypt data
- `encryptFile(filePath: string, key: EncryptionKey): EncryptedFile` - Encrypt file
- `decryptFile(encrypted: EncryptedFile, key: EncryptionKey): File` - Decrypt file

**Encryption Details**:
- Algorithm: AES-256-GCM for authenticated encryption
- Key derivation: PBKDF2 with SHA-256
- All patient data encrypted at rest
- All data encrypted in transit using TLS 1.2+

### 3. Consent Manager

**Responsibility**: Generate, validate, and manage QR-code-based consent tokens

**Key Operations**:
- `generateConsentToken(duration: Duration): ConsentToken` - Generate unique consent token
- `generateQRCode(token: ConsentToken): QRCodeImage` - Encode token into QR code
- `validateToken(token: ConsentToken): boolean` - Validate token signature and expiry
- `revokeConsent(providerId: string): void` - Revoke all sessions for a provider
- `getActiveConsents(): ConsentSession[]` - Get all active consent sessions

**Token Details**:
- Token format: Encrypted JSON containing provider ID, patient ID, expiry timestamp, and permissions
- Signature: HMAC-SHA256 for integrity verification
- Default expiry: 24 hours (customizable 1 hour to 30 days)
- Unique token per QR code generation

### 4. Session Manager

**Responsibility**: Manage time-limited access sessions for healthcare providers

**Key Operations**:
- `createSession(token: ConsentToken): Session` - Create session from validated token
- `getSession(sessionId: string): Session | null` - Retrieve session details
- `isSessionValid(sessionId: string): boolean` - Check if session is still valid
- `expireSession(sessionId: string): void` - Manually expire a session
- `clearExpiredSessions(): void` - Clean up expired sessions

**Session Details**:
- Session ID: Unique identifier for each access session
- Expiry: Automatic expiry based on token duration
- Permissions: Read-only or read-write based on provider role
- Cache: Stores accessed patient data for offline access

### 5. Barcode Scanner

**Responsibility**: Scan and decode medicine barcodes

**Key Operations**:
- `startScanning(): void` - Activate camera and start barcode detection
- `stopScanning(): void` - Stop scanning and release camera
- `decodeBarcodeFormat(barcode: string): BarcodeFormat` - Identify barcode format
- `lookupMedicine(barcode: string): Medicine | null` - Retrieve medicine details from local database
- `getMedicineDatabase(): MedicineDatabase` - Access local medicine database

**Barcode Details**:
- Supported formats: EAN-13, UPC-A, Code-128
- Local medicine database: Indexed by barcode for fast lookup
- Fallback: Manual entry if barcode cannot be decoded
- Database updates: Periodic updates from online sources when connectivity available

### 6. PDF Handler

**Responsibility**: Handle PDF upload, storage, and retrieval

**Key Operations**:
- `validatePDF(file: File): ValidationResult` - Validate PDF format and size (max 10 MB)
- `storePDF(file: File, metadata: ReportMetadata): string` - Store encrypted PDF and return ID
- `retrievePDF(id: string): File` - Retrieve and decrypt PDF
- `createReportMetadata(testName: string, date: Date, labName: string): ReportMetadata` - Create metadata
- `displayPDF(file: File): void` - Display PDF in viewer

**PDF Details**:
- Storage: Encrypted file storage with metadata in SQLite
- Metadata: Test name, date, lab name, file size, upload timestamp
- Retrieval: Lazy loading with caching for frequently accessed reports
- Display: Native PDF viewer integration

### 7. Backup Manager

**Responsibility**: Manage encrypted backup to cloud storage

**Key Operations**:
- `enableBackup(encryptionKey: EncryptionKey): void` - Enable backup with encryption key
- `disableBackup(): void` - Disable backup and delete cloud copies
- `syncToCloud(): void` - Sync pending records to cloud storage
- `restoreFromCloud(): void` - Restore backed-up records from cloud
- `getBackupStatus(): BackupStatus` - Get current backup status

**Backup Details**:
- Cloud provider: Google Drive
- Encryption: Patient-controlled encryption keys
- Sync frequency: Automatic within 5 minutes of new records (when online)
- Backup format: Encrypted JSON files organized by date
- Restoration: Full or incremental restore options

### 8. Sync Manager

**Responsibility**: Manage synchronization of data between devices and cloud

**Key Operations**:
- `queueBackupOperation(operation: BackupOperation): void` - Queue backup operation
- `processQueue(): void` - Process queued operations when online
- `syncProviderRecords(sessionId: string): void` - Sync records added by provider back to patient
- `getNetworkStatus(): NetworkStatus` - Check current network connectivity
- `registerNetworkListener(callback: NetworkCallback): void` - Register for connectivity changes

**Sync Details**:
- Queue-based: Operations queued when offline, executed when online
- Conflict resolution: Last-write-wins for concurrent updates
- Incremental sync: Only new/modified records synced
- Retry logic: Exponential backoff for failed operations

### 9. Role-Based UI Manager

**Responsibility**: Render role-specific user interfaces

**Key Operations**:
- `setUserRole(role: UserRole): void` - Set current user role
- `renderUI(role: UserRole): void` - Render UI for specified role
- `clearCachedData(): void` - Clear cached data when switching roles
- `getUIComponents(role: UserRole): UIComponent[]` - Get role-specific UI components

**UI Details**:
- Patient UI: View history, generate QR codes, manage backup, view summary
- Doctor UI: Scan QR code, view patient data, add prescriptions, add visit notes
- Pharmacist UI: Scan QR code, scan medicine barcodes, dispense medicines, generate bills
- Lab Technician UI: Scan QR code, upload PDF reports, view upload history
- Accessibility: Minimum 48x48 dp buttons, high-contrast colors, large fonts

## Data Models

### MedicalRecord (Base)
```
{
  id: string (UUID)
  patientId: string
  recordType: "prescription" | "medicine" | "lab_report" | "visit_note"
  createdDate: Date
  createdBy: string (provider name or "patient")
  encrypted: boolean
  encryptionKeyId: string
}
```

### Prescription
```
{
  ...MedicalRecord
  recordType: "prescription"
  medicationName: string
  dosage: string
  duration: string
  frequency: string
  doctorName: string
  doctorLicense: string (optional)
  status: "active" | "completed" | "discontinued"
  notes: string (optional)
}
```

### Medicine
```
{
  ...MedicalRecord
  recordType: "medicine"
  medicineName: string
  barcode: string
  dosage: string
  expiryDate: Date
  manufacturer: string
  dispensingDate: Date
  quantity: number
  pharmacyName: string
  cost: number
  batchNumber: string (optional)
}
```

### LabReport
```
{
  ...MedicalRecord
  recordType: "lab_report"
  testName: string
  labName: string
  reportDate: Date
  fileId: string (reference to encrypted PDF)
  fileSize: number
  uploadDate: Date
  techniciansName: string
}
```

### VisitNote
```
{
  ...MedicalRecord
  recordType: "visit_note"
  doctorName: string
  doctorLicense: string (optional)
  examinationFindings: string
  diagnosis: string
  treatmentPlan: string
  vitals: {
    bloodPressure: string (optional)
    temperature: number (optional)
    heartRate: number (optional)
    weight: number (optional)
  }
  notes: string (optional)
}
```

### ConsentToken
```
{
  tokenId: string (UUID)
  patientId: string
  providerId: string (optional, can be anonymous)
  providerRole: "doctor" | "pharmacist" | "lab_technician"
  permissions: string[] (e.g., ["read", "write:prescription"])
  issuedAt: Date
  expiresAt: Date
  signature: string (HMAC-SHA256)
  encryptedPayload: string
}
```

### Session
```
{
  sessionId: string (UUID)
  token: ConsentToken
  createdAt: Date
  expiresAt: Date
  isValid: boolean
  cachedPatientData: MedicalRecord[]
  lastAccessTime: Date
  accessCount: number
}
```

### EncryptionKey
```
{
  keyId: string (UUID)
  algorithm: "AES-256-GCM"
  derivationMethod: "PBKDF2-SHA256"
  salt: string (base64)
  iterations: number (minimum 100,000)
  createdAt: Date
  rotationSchedule: Date (optional)
}
```

## Correctness Properties

A property is a characteristic or behavior that should hold true across all valid executions of a system—essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.

### Property 1: Record Persistence Round Trip
*For any* medical record, when it is added to the Patient Record Store and then retrieved, the retrieved record should be identical to the original record.
**Validates: Requirements 1.1, 1.2**

### Property 2: Chronological Ordering
*For any* set of medical records with different creation dates, when retrieved from the Patient Record Store, they should be ordered in reverse chronological order (newest first).
**Validates: Requirements 1.5, 12.1, 12.2, 13.3**

### Property 3: Offline Operation Invariant
*For any* medical record operation (read or write), when the device is offline, the operation should succeed and the data should be persisted locally.
**Validates: Requirements 8.1, 8.5**

### Property 4: Backup Encryption Round Trip
*For any* medical record, when it is encrypted, backed up to cloud storage, and then restored and decrypted, the restored record should be identical to the original record.
**Validates: Requirements 2.1, 2.3, 9.1, 9.2**

### Property 5: Backup Disable Idempotence
*For any* backup operation, when backup is disabled, subsequent disables should have no additional effect and all cloud copies should remain deleted.
**Validates: Requirements 2.5**

### Property 6: QR Code Uniqueness
*For any* two QR code generation operations, the generated tokens should be unique and not collide.
**Validates: Requirements 3.1, 14.1**

### Property 7: Token Validation Consistency
*For any* valid consent token, when it is validated multiple times before expiry, all validations should succeed and return the same session.
**Validates: Requirements 3.2, 14.3**

### Property 8: Session Expiry Enforcement
*For any* consent session, when the session expiry time has passed, subsequent access attempts should be denied.
**Validates: Requirements 3.3, 3.6, 4.5, 5.5, 6.5**

### Property 9: Consent Revocation Completeness
*For any* active consent session, when consent is revoked, all subsequent access attempts should be denied and all cached data should be cleared.
**Validates: Requirements 3.4, 9.4**

### Property 10: Session Duration Customization
*For any* session duration value between 1 hour and 30 days, the system should accept it and create a session with the specified duration.
**Validates: Requirements 3.5**

### Property 11: Doctor Access Control
*For any* patient data access by a doctor, the access should only be granted if a valid, non-expired consent session exists.
**Validates: Requirements 4.1**

### Property 12: Doctor Record Creation
*For any* prescription or visit note added by a doctor, the record should be stored with all required fields (medication name, dosage, duration, timestamp, doctor name).
**Validates: Requirements 4.3, 4.4, 13.1**

### Property 13: Pharmacist Barcode Lookup
*For any* valid barcode, when scanned, the system should retrieve the corresponding medicine details from the local database.
**Validates: Requirements 5.1, 10.2**

### Property 14: Pharmacist Medicine Dispensing
*For any* medicine dispensing operation, the medicine record should be created with dispensing date, quantity, and pharmacy name.
**Validates: Requirements 5.3**

### Property 15: Bill Calculation Accuracy
*For any* set of dispensed medicines, the calculated bill total should equal the sum of individual medicine costs.
**Validates: Requirements 5.4**

### Property 16: Lab Technician Report Upload
*For any* valid PDF file, when uploaded by a lab technician, the file should be stored encrypted and linked to patient history with metadata.
**Validates: Requirements 6.2, 6.3, 11.2, 11.3**

### Property 17: PDF File Validation
*For any* file selection, if the file is not a valid PDF or exceeds 10 MB, the system should reject it and display an error.
**Validates: Requirements 11.1**

### Property 18: PDF Decryption Round Trip
*For any* PDF file, when it is encrypted, stored, retrieved, and decrypted, the decrypted file should be identical to the original file.
**Validates: Requirements 11.5**

### Property 19: Role-Based UI Rendering
*For any* user role selection, the system should render only the UI components and features specific to that role.
**Validates: Requirements 7.2, 7.3, 7.4**

### Property 20: Role Switching Cache Clearing
*For any* role switch operation, all cached data from the previous role should be cleared before rendering the new role's UI.
**Validates: Requirements 7.6**

### Property 21: Button Accessibility
*For any* button in the UI, the button should have a minimum size of 48x48 dp and use high-contrast colors.
**Validates: Requirements 7.5**

### Property 22: Sync on Reconnection
*For any* pending backup operations queued while offline, when the device regains internet connectivity, all queued operations should be executed.
**Validates: Requirements 8.2, 8.4**

### Property 23: Offline Cache Access
*For any* healthcare provider with cached patient data, when the device is offline, the provider should be able to access the cached data within the session duration.
**Validates: Requirements 8.3**

### Property 24: Data Encryption Coverage
*For any* patient data stored locally or backed up to cloud, the data should be encrypted using AES-256.
**Validates: Requirements 9.1, 9.2, 9.5**

### Property 25: Transport Encryption
*For any* patient data accessed by a healthcare provider, the data should be encrypted in transit using TLS 1.2 or higher.
**Validates: Requirements 9.3**

### Property 26: Barcode Format Support
*For any* barcode in supported formats (EAN-13, UPC-A, Code-128), the system should successfully decode it.
**Validates: Requirements 10.5**

### Property 27: Barcode Decode Fallback
*For any* barcode that cannot be decoded, the system should allow manual entry of medicine details.
**Validates: Requirements 10.4**

### Property 28: Medicine History Display
*For any* set of dispensed medicines, when displayed, all medicines should be shown with dispensing date, quantity, and pharmacy name.
**Validates: Requirements 12.2**

### Property 29: Prescription Status Update
*For any* prescription marked as completed, the status should be updated and the historical record should be maintained.
**Validates: Requirements 12.4**

### Property 30: Medicine Compliance Metrics
*For any* patient with prescriptions and dispensed medicines, the system should calculate compliance metrics showing prescribed vs. dispensed quantities.
**Validates: Requirements 12.5**

### Property 31: Visit Note Rich Text
*For any* visit note with rich text formatting (bold, italic, lists), the formatting should be preserved when stored and retrieved.
**Validates: Requirements 13.5**

### Property 32: Medical Summary Display
*For any* patient, the medical summary should display recent visit notes, active prescriptions, and pending lab reports.
**Validates: Requirements 13.2**

### Property 33: Lab Report Display
*For any* uploaded lab report, when viewed, the report metadata and download capability should be available.
**Validates: Requirements 6.4, 11.4**

### Property 34: Active Consent Session Display
*For any* active consent session, when viewed by the patient, the session should display with remaining validity time.
**Validates: Requirements 14.5**

### Property 35: Pagination for Large Histories
*For any* patient with a large medical history, when displayed, the records should be paginated to reduce memory consumption.
**Validates: Requirements 15.4**

## Error Handling

### Local Storage Errors
- **Disk Full**: Display user-friendly message, suggest clearing old records or enabling backup
- **Database Corruption**: Attempt automatic repair; if failed, offer restore from backup
- **File Access Denied**: Log error, display message, retry with elevated permissions if available

### Encryption Errors
- **Key Derivation Failure**: Log error, prompt user to re-enter password
- **Decryption Failure**: Indicate data corruption, offer restore from backup
- **Invalid Signature**: Reject data, log security event

### Network Errors
- **Connection Lost During Sync**: Queue operation, retry when connectivity restored
- **Cloud Storage Unavailable**: Queue operation, retry with exponential backoff
- **Authentication Failure**: Prompt user to re-authenticate with cloud provider

### Barcode Scanning Errors
- **Camera Permission Denied**: Display permission request dialog
- **Invalid Barcode Format**: Display error, allow manual entry
- **Medicine Not Found**: Display message, allow manual entry

### PDF Handling Errors
- **Invalid PDF Format**: Display error message, reject file
- **File Too Large**: Display error with size limit, reject file
- **PDF Corruption**: Display error, offer to re-upload

### Session Errors
- **Invalid Token**: Display error, prompt user to generate new QR code
- **Session Expired**: Display message, prompt user to generate new QR code
- **Unauthorized Access**: Log security event, deny access

### Sync Errors
- **Backup Encryption Key Lost**: Display error, offer to generate new key
- **Cloud Storage Quota Exceeded**: Display message, suggest deleting old backups
- **Sync Conflict**: Use last-write-wins strategy, log conflict

## Testing Strategy

### Unit Testing Approach

Unit tests verify specific examples, edge cases, and error conditions:

1. **Record Storage Tests**
   - Test adding, retrieving, updating, and deleting records
   - Test edge cases: empty records, maximum size records, special characters
   - Test error conditions: disk full, database corruption

2. **Encryption Tests**
   - Test encryption and decryption with various data types
   - Test key generation and derivation
   - Test invalid keys and corrupted data

3. **Consent Management Tests**
   - Test token generation and validation
   - Test token expiry and revocation
   - Test invalid tokens and expired tokens

4. **Barcode Scanning Tests**
   - Test barcode decoding for supported formats
   - Test invalid barcodes and fallback to manual entry
   - Test medicine database lookup

5. **PDF Handling Tests**
   - Test PDF validation (format, size)
   - Test PDF encryption and decryption
   - Test metadata creation and retrieval

6. **UI Tests**
   - Test role-based UI rendering
   - Test button sizes and color contrast
   - Test role switching and cache clearing

### Property-Based Testing Approach

Property-based tests verify universal properties across all inputs using randomization:

1. **Record Persistence Property Tests**
   - Generate random medical records
   - Store and retrieve them
   - Verify round-trip consistency (Property 1)
   - Verify chronological ordering (Property 2)

2. **Encryption Property Tests**
   - Generate random data and encryption keys
   - Encrypt and decrypt
   - Verify round-trip consistency (Property 4)
   - Verify data integrity with invalid keys

3. **Consent Management Property Tests**
   - Generate random consent tokens
   - Verify uniqueness (Property 6)
   - Verify validation consistency (Property 7)
   - Verify expiry enforcement (Property 8)

4. **Offline Operation Property Tests**
   - Simulate offline mode
   - Generate random operations
   - Verify all operations succeed (Property 3)
   - Verify data persistence

5. **Sync Property Tests**
   - Generate random pending operations
   - Simulate connectivity changes
   - Verify queue processing (Property 22)
   - Verify no data loss

6. **Barcode Property Tests**
   - Generate random barcodes in supported formats
   - Verify decoding (Property 26)
   - Verify fallback for invalid barcodes (Property 27)

7. **PDF Property Tests**
   - Generate random PDF files
   - Verify encryption round trip (Property 18)
   - Verify metadata preservation

8. **UI Property Tests**
   - Generate random role selections
   - Verify UI rendering (Property 19)
   - Verify cache clearing (Property 20)

### Test Configuration

- **Minimum iterations**: 100 per property test
- **Test framework**: Jest (for TypeScript/JavaScript) or equivalent
- **Coverage target**: 80% code coverage
- **CI/CD integration**: Run all tests on every commit

### Test Tagging

Each property-based test must be tagged with:
```
Feature: healthcare-record-system, Property {number}: {property_text}
```

Example:
```
Feature: healthcare-record-system, Property 1: Record Persistence Round Trip
```

