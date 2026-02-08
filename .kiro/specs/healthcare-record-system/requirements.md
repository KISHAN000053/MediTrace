# Healthcare Record System - Requirements Document

## Introduction

The Healthcare Record System is an offline-first, privacy-first platform designed to create a unified medical history repository for patients in rural setups. The system enables patients to store their lifelong medical records locally on their mobile devices, with optional encrypted backup to cloud storage. Healthcare providers (doctors, pharmacists, lab technicians) can access patient data through QR-code-based consent mechanisms with automatic session expiry. The system operates without central server dependency, ensuring data sovereignty and privacy compliance.

## Glossary

- **Patient**: End user who owns and manages their medical records
- **Healthcare_Provider**: Generic term for doctors, pharmacists, or lab technicians
- **Doctor**: Healthcare provider who examines patients and prescribes treatments
- **Pharmacist**: Healthcare provider who dispenses medicines and updates medicine history
- **Lab_Technician**: Healthcare provider who uploads and links lab reports
- **Medical_Record**: Complete patient health history including prescriptions, lab reports, medicines, and visit notes
- **Prescription**: Doctor-issued medication instruction with dosage and duration
- **Lab_Report**: Medical test results in PDF format linked to patient history
- **Medicine**: Pharmaceutical product with barcode, name, dosage, and expiry
- **Visit_Note**: Doctor's documentation of patient examination and findings
- **QR_Code**: Machine-readable consent token containing session credentials and expiry
- **Consent_Session**: Time-limited access grant to patient data by a healthcare provider
- **Offline_Mode**: System operation without internet connectivity
- **Encrypted_Backup**: Patient data encrypted and stored on cloud (Google Drive)
- **Role_Based_Interface**: UI rendered differently based on user role (Patient, Doctor, Pharmacist, Lab_Technician)
- **Barcode**: Machine-readable identifier on medicine packaging
- **Session_Expiry**: Automatic termination of healthcare provider access after defined time period
- **Data_Sovereignty**: Patient retains ownership and control of their medical data

## Requirements

### Requirement 1: Patient Medical Record Storage

**User Story:** As a patient, I want to store my complete medical history locally on my device, so that I can maintain a lifelong record of my health without depending on external servers.

#### Acceptance Criteria

1. THE Patient_Record_Store SHALL persist all patient medical data (prescriptions, lab reports, medicines, visit notes) to local device storage
2. WHEN a patient adds a new prescription, lab report, or medicine record THEN the Patient_Record_Store SHALL immediately write the data to local storage
3. WHEN the device is offline THEN the Patient_Record_Store SHALL continue to function and allow reading/writing of medical records
4. THE Patient_Record_Store SHALL support storing at least 20 years of medical history without performance degradation
5. WHEN a patient views their medical history THEN the system SHALL retrieve and display all stored records in chronological order

### Requirement 2: Patient Data Backup and Recovery

**User Story:** As a patient, I want my medical records to be backed up securely to cloud storage, so that I don't lose my data if my device is lost or damaged.

#### Acceptance Criteria

1. WHEN a patient enables backup THEN the system SHALL encrypt all medical records using patient-controlled encryption keys before uploading to cloud storage
2. WHEN backup is enabled THEN the system SHALL automatically sync new records to encrypted cloud storage within 5 minutes of creation
3. WHEN a patient restores from backup THEN the system SHALL decrypt and restore all backed-up records to local storage
4. THE system SHALL support Google Drive as the primary cloud storage backend
5. WHEN a patient disables backup THEN the system SHALL stop syncing new records to cloud storage and delete all cloud copies

### Requirement 3: QR-Code-Based Consent and Access Control

**User Story:** As a patient, I want to grant time-limited access to my medical records to healthcare providers through QR codes, so that I can control who sees my data and for how long.

#### Acceptance Criteria

1. WHEN a patient initiates data sharing THEN the system SHALL generate a unique QR code containing encrypted consent credentials with a default 24-hour expiry
2. WHEN a healthcare provider scans the QR code THEN the system SHALL validate the consent token and establish a time-limited access session
3. WHEN a consent session expires THEN the system SHALL automatically revoke the healthcare provider's access to patient data
4. WHEN a patient revokes consent THEN the system SHALL immediately terminate all active access sessions for that healthcare provider
5. THE system SHALL allow patients to customize consent session duration (1 hour to 30 days) before generating QR codes
6. WHEN a healthcare provider attempts to access patient data after session expiry THEN the system SHALL deny access and return an error

### Requirement 4: Doctor Access and Prescription Management

**User Story:** As a doctor, I want to access patient medical history with consent and add prescriptions and visit notes, so that I can provide informed care and maintain treatment records.

#### Acceptance Criteria

1. WHEN a doctor scans a patient's consent QR code THEN the system SHALL authenticate the doctor and grant access to the patient's medical history
2. WHEN a doctor views patient data THEN the system SHALL display prescriptions, lab reports, medicines, and visit notes in a readable format
3. WHEN a doctor adds a new prescription THEN the system SHALL record the prescription with medication name, dosage, duration, and timestamp
4. WHEN a doctor adds a visit note THEN the system SHALL record the note with examination findings, diagnosis, and treatment plan
5. WHEN a doctor's access session expires THEN the system SHALL prevent further access to patient data and clear cached data from the doctor's device

### Requirement 5: Pharmacist Medicine Management

**User Story:** As a pharmacist, I want to scan medicine barcodes and update patient medicine history, so that I can track medication dispensing and maintain accurate records.

#### Acceptance Criteria

1. WHEN a pharmacist scans a medicine barcode THEN the system SHALL retrieve medicine details (name, dosage, expiry, manufacturer)
2. WHEN a pharmacist scans a patient's consent QR code THEN the system SHALL grant access to update the patient's medicine history
3. WHEN a pharmacist dispenses a medicine THEN the system SHALL add the medicine record to patient history with dispensing date and quantity
4. WHEN a pharmacist generates a bill THEN the system SHALL calculate total cost from dispensed medicines and create a bill record
5. WHEN a pharmacist's access session expires THEN the system SHALL prevent further updates to patient medicine history

### Requirement 6: Lab Technician Report Management

**User Story:** As a lab technician, I want to upload lab reports in PDF format and link them to patient history, so that test results are properly documented and accessible.

#### Acceptance Criteria

1. WHEN a lab technician scans a patient's consent QR code THEN the system SHALL grant access to upload lab reports
2. WHEN a lab technician uploads a PDF lab report THEN the system SHALL store the report and link it to the patient's medical history
3. WHEN a lab report is uploaded THEN the system SHALL record the report with test name, date, and lab name
4. WHEN a patient views their medical history THEN the system SHALL display all uploaded lab reports with download capability
5. WHEN a lab technician's access session expires THEN the system SHALL prevent further uploads to patient history

### Requirement 7: Role-Based User Interface

**User Story:** As a system user, I want the interface to adapt to my role (patient, doctor, pharmacist, lab technician), so that I see only relevant features and controls.

#### Acceptance Criteria

1. WHEN a user launches the application THEN the system SHALL display a role selection screen
2. WHEN a user selects their role THEN the system SHALL render the interface with role-specific features and controls
3. WHEN a patient is logged in THEN the system SHALL display patient-specific features (view history, generate QR codes, manage backup)
4. WHEN a healthcare provider is logged in THEN the system SHALL display provider-specific features (scan QR code, access patient data, add records)
5. THE system SHALL use large buttons (minimum 48x48 dp) and high-contrast colors for accessibility
6. WHEN a user switches roles THEN the system SHALL clear cached data and re-render the interface for the new role

### Requirement 8: Offline-First Operation

**User Story:** As a patient in a rural area with intermittent internet, I want the system to work completely offline, so that I can access and manage my medical records regardless of connectivity.

#### Acceptance Criteria

1. WHEN the device is offline THEN the system SHALL continue to function for all read and write operations on local data
2. WHEN the device regains internet connectivity THEN the system SHALL automatically sync pending changes to cloud backup (if enabled)
3. WHEN a healthcare provider is offline THEN the system SHALL allow access to previously cached patient data within the consent session duration
4. WHEN the device is offline THEN the system SHALL queue all backup operations and execute them when connectivity is restored
5. THE system SHALL not require internet connectivity for core functionality (viewing records, adding prescriptions, scanning barcodes)

### Requirement 9: Data Security and Encryption

**User Story:** As a patient, I want my medical data to be encrypted and protected, so that my privacy is maintained even if my device is compromised.

#### Acceptance Criteria

1. WHEN patient data is stored locally THEN the system SHALL encrypt all sensitive data using AES-256 encryption
2. WHEN patient data is backed up to cloud storage THEN the system SHALL encrypt data using patient-controlled encryption keys before transmission
3. WHEN a healthcare provider accesses patient data THEN the system SHALL ensure data is encrypted in transit using TLS 1.2 or higher
4. WHEN a consent session expires THEN the system SHALL securely delete all cached patient data from the healthcare provider's device
5. THE system SHALL not store any unencrypted patient data on the device or cloud storage

### Requirement 10: Medicine Barcode Scanning

**User Story:** As a pharmacist, I want to scan medicine barcodes to quickly retrieve medicine information, so that I can efficiently update patient records.

#### Acceptance Criteria

1. WHEN a pharmacist opens the barcode scanner THEN the system SHALL activate the device camera and display a scanning interface
2. WHEN a barcode is scanned THEN the system SHALL decode the barcode and retrieve medicine details from a local medicine database
3. WHEN medicine details are retrieved THEN the system SHALL display medicine name, dosage, expiry date, and manufacturer
4. WHEN a barcode cannot be decoded THEN the system SHALL allow manual entry of medicine details
5. THE system SHALL support standard barcode formats (EAN-13, UPC-A, Code-128)

### Requirement 11: Lab Report PDF Management

**User Story:** As a lab technician, I want to upload PDF lab reports and have them stored with patient records, so that test results are properly documented.

#### Acceptance Criteria

1. WHEN a lab technician selects a PDF file THEN the system SHALL validate the file format and size (maximum 10 MB)
2. WHEN a valid PDF is selected THEN the system SHALL store the PDF in encrypted local storage
3. WHEN a lab report is uploaded THEN the system SHALL create a metadata record with test name, date, and lab name
4. WHEN a patient views lab reports THEN the system SHALL display report metadata and provide download capability
5. WHEN a lab report is downloaded THEN the system SHALL decrypt and display the PDF in a viewer

### Requirement 12: Prescription and Medicine History Tracking

**User Story:** As a doctor or patient, I want to view complete prescription and medicine history, so that I can track treatment progression and medication compliance.

#### Acceptance Criteria

1. WHEN a patient views their prescription history THEN the system SHALL display all prescriptions in reverse chronological order with doctor name and date
2. WHEN a patient views their medicine history THEN the system SHALL display all dispensed medicines with dispensing date, quantity, and pharmacy name
3. WHEN a doctor views patient prescriptions THEN the system SHALL display all active and past prescriptions with current status
4. WHEN a prescription is marked as completed THEN the system SHALL update the prescription status and maintain historical record
5. THE system SHALL calculate and display medicine compliance metrics (prescribed vs. dispensed quantities)

### Requirement 13: Visit Notes and Medical Summary

**User Story:** As a doctor, I want to add and view visit notes, so that I can maintain a record of patient examinations and treatment decisions.

#### Acceptance Criteria

1. WHEN a doctor adds a visit note THEN the system SHALL record the note with examination findings, diagnosis, and treatment plan
2. WHEN a patient views their medical summary THEN the system SHALL display recent visit notes, active prescriptions, and pending lab reports
3. WHEN a doctor views patient history THEN the system SHALL display all visit notes in reverse chronological order
4. WHEN a visit note is added THEN the system SHALL timestamp the note with doctor name and date
5. THE system SHALL support rich text formatting in visit notes (bold, italic, lists)

### Requirement 14: QR Code Generation and Validation

**User Story:** As a patient, I want to generate QR codes for sharing my data with healthcare providers, so that I can control access with simple scanning.

#### Acceptance Criteria

1. WHEN a patient initiates QR code generation THEN the system SHALL create a unique token with encrypted consent credentials
2. WHEN a QR code is generated THEN the system SHALL encode the token into a scannable QR code image
3. WHEN a healthcare provider scans the QR code THEN the system SHALL validate the token signature and check expiry
4. WHEN a QR code expires THEN the system SHALL invalidate the token and prevent further access
5. WHEN a patient views active consent sessions THEN the system SHALL display all active QR codes with remaining validity time

### Requirement 15: Low-End Device Optimization

**User Story:** As a patient with a low-end device, I want the system to run efficiently without consuming excessive resources, so that I can use it on older smartphones.

#### Acceptance Criteria

1. THE system SHALL use minimal memory footprint (less than 50 MB for core functionality)
2. WHEN the application starts THEN the system SHALL load within 3 seconds on devices with 1 GB RAM
3. THE system SHALL optimize database queries to minimize CPU usage
4. WHEN displaying large medical histories THEN the system SHALL implement pagination to reduce memory consumption
5. THE system SHALL support devices running Android 8.0 or higher and iOS 12 or higher

