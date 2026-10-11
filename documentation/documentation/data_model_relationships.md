# Data Model Relationships

**Project:** Automated Vital-Sign Monitoring and Reporting System  
**Document Type:** Data Model Documentation  
**Version:** 0.1  
**Status:** Educational Prototype Specification  
**Time Format:** 24-hour time

---

## 1. Purpose

This document explains how the project's 13 database tables connect, what each relationship means, and how the system protects data integrity and traceability.

The system supports vital-sign recording, rule-based alerts, intervention documentation, staff assignments, and ward handovers.

**Safety limitation:** This is an educational prototype, not a system approved for real patient-care decisions.

---

## 2. Overview of the 13 Tables

### 2.1 Core Clinical and Monitoring Tables

1. **Patients** — stores a non-identifying patient reference and patient status.
2. **Observations** — stores individual vital-sign measurements.
3. **Alerts** — stores alerts generated from observations.
4. **Alert Events** — records the history of alert actions and status changes.
5. **Staff** — stores staff references, roles, and account status.
6. **Interventions** — records actions performed in response to a patient's condition or an alert.

### 2.2 Organization and Workflow Tables

7. **Departments** — represents hospital departments.
8. **Wards** — represents wards belonging to departments.
9. **Shifts** — defines shift categories and scheduled start and end times.
10. **Staff Shift Assignments** — records staff members' ward and shift assignments.
11. **Patient Staff Assignments** — records patient assignments linked to staff shift assignments.
12. **Handover Records** — records information transferred between outgoing and incoming shifts.
13. **Handover Items** — records patient-specific alerts, tasks, and follow-up items included in a handover.

---

## 3. Primary Keys and Foreign Keys

### 3.1 Primary Key (PK)

A primary key uniquely identifies a record in a table.

**Example:** `patient_id` uniquely identifies a patient record in the Patients table.

### 3.2 Foreign Key (FK)

A foreign key links a record to a related record in another table.

**Example:** `patient_id` in Observations links a vital-sign measurement to the corresponding patient record.

---

## 4. Relationships Between Tables

### 4.1 Departments and Wards

**Relationship:** One department can have many wards.

**Fields involved:**
- Departments: `department_id` (PK)
- Wards: `ward_id` (PK), `department_id` (FK)

Each ward belongs to a department. A department may contain multiple wards.

### 4.2 Wards and Staff Shift Assignments

**Relationship:** One ward can have many staff shift assignments.

**Fields involved:**
- Wards: `ward_id` (PK)
- Staff Shift Assignments: `assignment_id` (PK), `ward_id` (FK)

This records the ward associated with a staff member's shift assignment.

### 4.3 Shifts and Staff Shift Assignments

**Relationship:** One shift category can be used by many staff shift assignments.

**Fields involved:**
- Shifts: `shift_id` (PK)
- Staff Shift Assignments: `assignment_id` (PK), `shift_id` (FK)

Examples of shift categories include Morning, Afternoon, and Night. The dated assignment period is stored in Staff Shift Assignments.

### 4.4 Staff and Staff Shift Assignments

**Relationship:** One staff member can have many shift assignments over time.

**Fields involved:**
- Staff: `staff_id` (PK)
- Staff Shift Assignments: `assignment_id` (PK), `staff_id` (FK)

A staff member's identity remains the same when their ward or shift assignment changes. Separate assignment records preserve the history.

### 4.5 Patients and Patient Staff Assignments

**Relationship:** One patient can have many patient-staff assignment records over time.

**Fields involved:**
- Patients: `patient_id` (PK)
- Patient Staff Assignments: `patient_assignment_id` (PK), `patient_id` (FK)

This allows patient assignments to change without overwriting previous assignment records.

### 4.6 Staff Shift Assignments and Patient Staff Assignments

**Relationship:** One staff shift assignment can be linked to many patient staff assignments.

**Fields involved:**
- Staff Shift Assignments: `assignment_id` (PK)
- Patient Staff Assignments: `patient_assignment_id` (PK), `assignment_id` (FK)

The linked shift assignment identifies the staff member and ward. Patient Staff Assignments therefore does not need a duplicate `staff_id`.

### 4.7 Patients and Observations

**Relationship:** One patient can have many observations.

**Fields involved:**
- Patients: `patient_id` (PK)
- Observations: `observation_id` (PK), `patient_id` (FK)

Each observation represents one measurement, such as pulse, temperature, or oxygen saturation.

### 4.8 Patient Staff Assignments and Observations

**Relationship:** One patient staff assignment can be associated with many observations.

**Fields involved:**
- Patient Staff Assignments: `patient_assignment_id` (PK)
- Observations: `patient_assignment_id` (optional FK)

When this optional link is supplied, the system must verify that the assignment belongs to the same patient and is valid for the relevant period. The system must not invent an assignment when it cannot be established.

### 4.9 Staff and Observations

**Relationship:** One staff member can be associated with many observations in different roles.

**Fields involved:**
- Staff: `staff_id` (PK)
- Observations: `observer_id` (FK), `recorded_by` (FK)

`observer_id` identifies the person reported to have measured the vital sign. `recorded_by` identifies the person who entered the record. These may be different people.

### 4.10 Observations and Alerts

**Relationship:** One observation can generate zero, one, or multiple distinct alerts.

**Fields involved:**
- Observations: `observation_id` (PK)
- Alerts: `alert_id` (PK), `observation_id` (FK)

Every alert must reference an observation. Multiple alerts should only be generated when distinct approved rules justify them. Duplicate alerts for the same concern should be avoided.

### 4.11 Alerts and Alert Events

**Relationship:** One alert can have many alert events.

**Fields involved:**
- Alerts: `alert_id` (PK)
- Alert Events: `event_id` (PK), `alert_id` (FK)

Alerts stores the current status. Alert Events preserves the history of creation, acknowledgement, escalation, and resolution.

Earlier events must not be silently overwritten.

### 4.12 Alerts and Interventions

**Relationship:** One alert may be linked to multiple interventions.

**Fields involved:**
- Alerts: `alert_id` (PK)
- Interventions: `alert_id` (optional FK)

An intervention may relate to an alert. The link is optional because an intervention may be documented for another clinical reason.

### 4.13 Patients and Interventions

**Relationship:** One patient can have many interventions.

**Fields involved:**
- Patients: `patient_id` (PK)
- Interventions: `intervention_id` (PK), `patient_id` (FK)

Each intervention identifies the patient associated with the documented action.

### 4.14 Staff and Interventions

**Relationship:** One staff member may perform or record many interventions.

**Fields involved:**
- Staff: `staff_id` (PK)
- Interventions: `performed_by` (FK), `recorded_by` (FK)

`performed_by` identifies the person reported to have performed the action. `recorded_by` identifies who entered it.

`performed_at` is the reported time the action occurred. `recorded_at` is generated when the record is saved. These timestamps must not be treated as interchangeable.

### 4.15 Staff and Alert Events

**Relationship:** One staff member can be associated with many alert events.

**Fields involved:**
- Staff: `staff_id` (PK)
- Alert Events: `staff_id` (FK)

A system-generated event may have no individual staff actor. In that case, the actor reference may be null, but the event must still be identifiable as system-generated.

### 4.16 Wards and Handover Records

**Relationship:** One ward can have many handover records over time.

**Fields involved:**
- Wards: `ward_id` (PK)
- Handover Records: `handover_id` (PK), `ward_id` (FK)

Each handover record represents a ward-level transfer of information between shift assignments.

### 4.17 Staff Shift Assignments and Handover Records

**Relationship:** One staff shift assignment may be referenced by many handover records.

**Fields involved:**
- Staff Shift Assignments: `assignment_id` (PK)
- Handover Records: `outgoing_assignment_id` (FK), `incoming_assignment_id` (FK)

These fields identify the outgoing and incoming shift assignments. The system must verify that both assignments are appropriate for the ward and handover period.

### 4.18 Staff and Handover Records

**Relationship:** One staff member may prepare or receive many handovers.

**Fields involved:**
- Staff: `staff_id` (PK)
- Handover Records: `prepared_by` (FK), `received_by` (FK)

`prepared_by` identifies who prepared the handover. `received_by` identifies who received or acknowledged it. The receiving staff member must be consistent with the incoming assignment.

### 4.19 Handover Records and Handover Items

**Relationship:** One handover record can contain many handover items.

**Fields involved:**
- Handover Records: `handover_id` (PK)
- Handover Items: `handover_item_id` (PK), `handover_id` (FK)

Each item represents a patient-specific issue or follow-up task.

### 4.20 Patients and Handover Items

**Relationship:** One patient can be referenced by many handover items.

**Fields involved:**
- Patients: `patient_id` (PK)
- Handover Items: `patient_id` (FK)

Each patient-specific item must identify the relevant patient without exposing unnecessary identifying information.

### 4.21 Alerts and Handover Items

**Relationship:** One alert may be referenced by multiple handover items.

**Fields involved:**
- Alerts: `alert_id` (PK)
- Handover Items: `alert_id` (optional FK)

This allows an outstanding alert to be included in relevant handovers. Acknowledging a handover item does not resolve the linked alert.

### 4.22 Interventions and Handover Items

**Relationship:** One intervention may be referenced by multiple handover items.

**Fields involved:**
- Interventions: `intervention_id` (PK)
- Handover Items: `intervention_id` (optional FK)

This allows a handover item to refer to a documented intervention when it is relevant to the ongoing task or follow-up.

---

## 5. Data Integrity Rules

The system should enforce these rules:

1. Every primary key must be unique and present.
2. Every required foreign key must reference an existing record.
3. A linked patient assignment must belong to the same patient as the related observation or record.
4. Assignment periods must be checked for consistency, and historical assignments must be preserved.
5. `measured_at`, `performed_at`, and `recorded_at` represent different events and must remain distinct.
6. The system must not fabricate server timestamps when a device is offline. Offline status and later synchronization must be represented honestly.
7. Corrections must preserve an audit trail and include a reason.
8. Every alert must link to the observation that triggered it and, where applicable, to the approved rule used.
9. Alert creation should generate an initial event. Later actions and status changes should be recorded in Alert Events.
10. Formal alert resolution must record the appropriate resolver and resolution time.
11. Handover acknowledgement does not mean an alert is resolved or that the patient is clinically stable.
12. Handover items must contain enough information to identify the issue or follow-up action.
13. Urgent alerts must follow approved escalation procedures and must not wait for routine handover.
14. Staff accounts and permissions must be individual and role-appropriate. Shared logins must not be used.
15. Clinical rules must be reviewed and approved by appropriately qualified clinical governance personnel before real-world use.

---

## 6. Time and Auditability

The connected implementation should preferably store timestamps in Coordinated Universal Time (UTC) and display them in the appropriate local time zone.

The learning examples use 24-hour time. For example, `19:00` to `07:00` represents a night shift crossing midnight. Assignment dates must make the actual period unambiguous.

A timestamp records what the system recorded. It does not, by itself, prove that a person was physically present or that a reported clinical action occurred.

---

## 7. Scope and Safety

This data model is for learning database design and documenting an educational prototype. It does not establish a hospital-approved clinical protocol.

Vital-sign thresholds, alert routing, escalation, staff permissions, and clinical workflows must be validated by appropriate professionals before implementation in a real healthcare environment.

**Do not place real patient-identifying information in this public repository.**

---

## 8. Related Document

The project requirements and scope are described in:

`documentation/project_specification.md`
