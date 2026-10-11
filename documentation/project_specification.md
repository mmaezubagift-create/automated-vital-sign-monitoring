
# Automated Vital-Sign Monitoring and Reporting System

## Project Information

**Project Type:** Educational healthcare data and automation prototype

**Domain:** Nursing, healthcare data, workflow automation

**Status:** Requirements and data-model design

**Project Owner:** Mmaezuba Gift Ifeoma

**Version:** 0.1

---

## 1. Project Overview

The Automated Vital-Sign Monitoring and Reporting System is an educational project designed to explore how patient vital-sign observations can be recorded, evaluated against approved rules, tracked, and organized into reports.

The proposed system aims to reduce duplicate data entry, improve data quality, support timely review of alerts, and preserve a traceable history of observations and staff actions.

The design includes patient observations, configurable alert rules, staff and shift assignments, interventions, and shift handovers.

This document describes the requirements and proposed data model. It does not claim that a functioning clinical system has been built.

The initial prototype will use synthetic data only.

## 2. Problem Statement

Healthcare staff may need to copy patient observations from one system or record into another report. Repeated manual entry can create delays, inconsistent information, transcription errors, and difficulties tracing how an alert was handled.

A reporting workflow should preserve the original observation, identify the rule that triggered an alert, record subsequent actions, and maintain a history that authorized users can review.

The project explores how structured data, validation, automation, and reporting can support safer and more traceable workflows.

## 3. Intended Users

The proposed users include:

- Nurses who record vital-sign observations and document care.
- Nurses receiving or handing over a shift.
- Authorized staff who prepare rosters and correct assignments.
- Supervisors or clinical leads who review workflow and data quality.
- Developers and analysts learning healthcare data systems.

Access must be based on individual authenticated accounts and role permissions. Shared accounts must not be used.

Logging in, being placed on a roster, or checking in does not by itself prove physical attendance or clinical responsibility.

## 4. Observation Data Requirements

Each observation represents one recorded measurement and must have a unique observation ID.

Proposed fields include:

- `observation_id` — primary key.
- `patient_id` — foreign key to the synthetic patient record.
- `vital_type` — measurement type, such as pulse, temperature, or oxygen saturation.
- `value` — measured numerical value.
- `unit` — measurement unit.
- `measured_at` — reported time the measurement was taken.
- `recorded_at` — automatically generated time the record was saved.
- `observer_id` — staff member reported to have taken the measurement.
- `recorded_by` — staff member who entered the record.
- `patient_assignment_id` — optional link to the patient's staff assignment at the time.

The observer and the person entering the record may be different people.

The system must distinguish the reported measurement time from the automatically generated record-save time. Late documentation must not be represented as though the measurement was entered immediately.

Each observation must be linked to a valid synthetic patient. Where a patient assignment is supplied, the assignment must match the patient and relevant time period.

Measurement types, units, and values must be validated.

Original observations must be retained. Corrections must preserve the previous information, the reason for correction, the person making it, and the correction time.

## 5. Feature Requirements

### 5.1 Observation Recording

The proposed system should allow authorized users to record vital-sign observations and associate them with the correct synthetic patient.

### 5.2 Rule-Based Alert Generation

The system should evaluate observations against configurable rules approved for the intended clinical context.

The initial educational prototype will use simple individual-parameter rules rather than an aggregate early-warning score.

Rules must not be presented as universal clinical thresholds. Actual thresholds, oxygen-saturation targets, and escalation procedures depend on approved clinical protocols and the patient's circumstances.

An observation may trigger more than one alert only when distinct applicable rules justify each alert.

Each alert must identify the observation that triggered it and the rule applied.

### 5.3 Alert Tracking

The system should maintain the current alert status and a chronological history of relevant events.

Alert events may include:

- Alert created.
- Alert acknowledged.
- Escalation recorded.
- Intervention documented.
- Follow-up or verification recorded.
- Alert formally resolved.

The current status belongs in the Alerts record. The historical sequence belongs in Alert Events.

The alert history must not be silently overwritten.

### 5.4 Intervention Documentation

The system should record interventions associated with a patient and, where applicable, an alert.

The record should distinguish:

- `performed_at` — reported time the intervention was performed.
- `recorded_at` — automatically generated time the intervention was entered.
- `performed_by` — staff member reported to have performed the intervention.
- `recorded_by` — staff member who entered the documentation.

The patient's response may be recorded separately, including the person who documented the response and the automatically generated response-recording time.

An intervention record does not automatically mean that the patient improved or that an alert should be resolved.

### 5.5 Departments and Wards

The system should organize wards within departments.

A department may contain multiple wards. Each ward belongs to one department in the initial prototype.

### 5.6 Shift Management

The system should support Morning, Afternoon, and Night shifts.

Each shift definition should include its scheduled start and end times. Overnight shifts must be able to span two calendar dates.

Scheduled times must be distinguished from actual check-in and check-out times.

### 5.7 Staff Shift Assignments

Authorized staff should be able to prepare and correct staff shift assignments.

Each assignment should identify the staff member, shift, ward, effective period, and assignment status.

Assignment statuses may include:

- Scheduled.
- Active.
- Completed.
- Cancelled.

Optional actual check-in and check-out timestamps may be recorded.

A staff member's assignment may change over time. Changes must preserve assignment history rather than erase previous assignments.

A login or check-in event alone does not prove that the staff member was physically present or responsible for a specific patient's care.

### 5.8 Patient Staff Assignments

The system should record which staff assignment is associated with a patient's care during a defined period.

A patient assignment should identify the patient, the staff shift assignment, the effective period, and the assignment status.

The staff member can be identified through the linked staff shift assignment; the staff ID should not be duplicated unnecessarily.

The model should preserve historical assignments and allow authorized changes when shifts, responsibilities, or patient allocations change.

A shift ending does not automatically mean every patient assignment has ended.

### 5.9 Shift Handover

The system should support a ward-level handover between outgoing and incoming staff assignments.

A handover record should identify:

- The ward.
- The outgoing assignment.
- The incoming assignment.
- The staff member who prepared the handover.
- The staff member who received it.
- The handover status.
- Handover notes.
- The automatically generated record and acknowledgement timestamps.

The handover workflow should support Draft, Pending Acknowledgement, Acknowledged, and Escalated statuses.

Patient-specific outstanding alerts and follow-up tasks should be represented as separate Handover Items linked to the handover record.

An incoming staff member's acknowledgement means the handover was received or reviewed. It does not prove that the patient is clinically stable, that an outstanding task is complete, or that an alert has been resolved.

Urgent alerts must follow the approved escalation procedure and must not wait for routine shift handover.

### 5.10 Handover Items

Each handover item should identify the relevant patient and describe an outstanding alert, intervention, follow-up task, or other actionable issue.

Where applicable, the item may link to an alert or intervention.

Item statuses may include:

- Pending.
- Acknowledged.
- Completed.
- Escalated.

The system should validate that each item contains enough information to identify the issue or action requiring follow-up.

Acknowledging a handover item must not automatically resolve its linked alert or mark an undocumented clinical task as completed.

### 5.11 Automated Reporting

The proposed system should support reports such as:

- Vital-sign observations over a selected period.
- Alerts by status and rule.
- Outstanding alerts requiring review.
- Interventions and documented patient responses.
- Outstanding handover items.
- Activity and documentation summaries by ward or shift.

Reports should use validated data and preserve traceability to the underlying records.

### 5.12 Audit and Accountability

Each action should be attributable to an individual authenticated user or clearly identified as a system-generated event.

The system should record relevant changes and preserve a chronological history.

Automatic timestamps and audit logs can make changes easier to trace, but they do not prove that a clinical action actually occurred.

## 6. Proposed Alert Lifecycle

The proposed alert lifecycle is:

1. A vital-sign observation is recorded.
2. The system validates the observation.
3. The system evaluates the observation against applicable configured rules.
4. If a rule is triggered, an alert is created and linked to the observation.
5. An initial Alert Event records alert creation.
6. The alert is routed for review according to the approved workflow.
7. An authorized staff member acknowledges the alert.
8. Any intervention, escalation, or follow-up is documented.
9. The patient's response may be recorded when available.
10. The responsible authorized staff member formally resolves the alert when the applicable workflow requirements are met.
11. The system records the resolver, resolution time, and corresponding Alert Event.

The exact clinical response and escalation process must be approved by the responsible clinical authority.

An alert being acknowledged does not mean it is resolved. An intervention being documented does not prove recovery. A handover acknowledgement does not establish clinical stability.

The system must preserve alert events as a chronological history rather than replacing earlier events with the latest status.

## 7. Network Failure Behavior

The proposed design should consider unreliable network access and temporary disconnection.

If offline recording is supported in a later implementation:

- Records should be marked as pending synchronization until the system confirms successful synchronization.
- The device-reported event time and local record-save time should be preserved where available.
- The server receipt time should be recorded only when the server actually receives the record.
- The system must not invent a server timestamp for an action performed offline.
- Duplicate submissions should be detected using stable identifiers and appropriate validation.
- Conflicts should be reviewed and resolved without silently overwriting historical information.
- Users should be able to distinguish unsynchronized records from successfully synchronized records.

Offline functionality is a future design requirement; this document does not claim that it has been implemented.

## 8. Non-Functional Requirements

### 8.1 Data Integrity

Primary keys must be unique. Foreign keys must refer to existing records. Required fields must not be missing, and measurement values and units must be validated.

Relationships between patients, assignments, observations, alerts, interventions, and handovers must remain consistent.

### 8.2 Security and Access Control

The intended design should use individual authenticated accounts, role-based permissions, and appropriate access restrictions.

Authentication credentials must not be stored in the project dataset or public documentation.

### 8.3 Auditability

The system should preserve a traceable history of important actions, corrections, assignment changes, alert events, and handover activity.

### 8.4 Time Integrity

The design should distinguish scheduled times, reported event times, automatically generated record-save times, and server receipt times.

For a connected implementation, UTC storage is preferred, with local-time display where needed. Learning examples will use the 24-hour time format.

Connected systems should use a trusted synchronized clock. Offline device timestamps should retain their source and synchronization status.

GPS data may provide supporting context in some systems, but it does not prove that a person performed an action or was clinically responsible for a patient.

### 8.5 Usability

The proposed workflow should minimize unnecessary duplicate entry and make outstanding alerts and handover tasks easy to identify.

### 8.6 Reliability and Maintainability

Rules, validation checks, and reporting logic should be documented and testable. Changes should be traceable, and the design should support future improvements without losing historical records.

### 8.7 Privacy

Only synthetic patient data should be used in the public learning repository. No identifiable patient information should be included.

## 9. Safety and Scope Limitation

This project is an educational healthcare data and automation prototype.

It is not a validated clinical decision-support system and must not be used to make real patient-care decisions.

The project will not independently diagnose patients, prescribe treatment, or replace nursing assessment, professional judgement, or clinical escalation procedures.

Clinical thresholds and alert rules must be based on an approved protocol appropriate to the intended patient population.

Oxygen-saturation targets may differ according to clinical circumstances. Therefore, a single value must not be treated as a universal rule for every patient.

The project must not assume that an alert acknowledgement, an intervention entry, a shift handover, or a recorded timestamp proves that a patient is safe or that care was completed.

Any future real-world deployment would require appropriate clinical governance, privacy and security review, validation, usability testing, and authorized oversight.

## 10. Initial Success Criteria

The design will be considered ready for the next learning stage when:

- The proposed tables and relationships are documented.
- Each observation can be linked to a synthetic patient and, when applicable, a valid patient staff assignment.
- Observation measurement time is distinguished from record-save time.
- Each alert can be traced to its triggering observation and applicable rule.
- Alert events preserve a chronological action history.
- Formal alert resolution records the responsible staff member and resolution time.
- Intervention performance time is distinguished from record-save time.
- Shift assignments and patient assignments can change without erasing history.
- Handover records can identify outgoing and incoming assignments.
- Handover items can list outstanding alerts and follow-up tasks.
- Acknowledging a handover does not automatically resolve an alert or complete an outstanding task.
- Data validation and referential-integrity rules are documented.
- The design addresses unreliable networks and delayed synchronization without inventing timestamps.
- Public files contain no identifiable patient information.
- A small synthetic test dataset can be used to demonstrate the design safely.

## 11. Future Development

Possible future stages include:

- Create a relational database schema based on the proposed thirteen tables.
- Build synthetic data for patients, staff, departments, wards, shifts, observations, alerts, assignments, interventions, and handovers.
- Write SQL queries for outstanding alerts, patient observation histories, and handover summaries.
- Create a simple dashboard and automated reporting workflow.
- Add data-quality checks and a repeatable data-processing pipeline.
- Test duplicate records, delayed documentation, invalid values, network failure, assignment changes, and shift transitions.
- Explore APIs and automation after the data model and safety requirements are clear.
- Evaluate rule configuration against an approved clinical protocol.
- Consider a clinically governed evaluation before any real-world deployment.

## Proposed Data Model Summary

The proposed data model contains thirteen tables:

1. Patients
2. Observations
3. Alerts
4. Alert Events
5. Staff
6. Interventions
7. Departments
8. Wards
9. Shifts
10. Staff Shift Assignments
11. Patient Staff Assignments
12. Handover Records
13. Handover Items

The field definitions, primary keys, foreign keys, relationships, and integrity rules are documented separately in `documentation/data_model_relationships.md`.
