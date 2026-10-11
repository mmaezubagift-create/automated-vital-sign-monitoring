# Database Schema

## 1. Purpose

This document defines the database structure for the Automated Vital-Sign Monitoring and Reporting System.

The database will organize fictional patient records, vital-sign observations, alerts, staff assignments, interventions, and shift handovers.

## 2. Design Principles

- Each table must have a primary key to identify each record uniquely.
- Foreign keys must connect related records.
- Historical observations and audit events must be preserved.
- Measurement time must be distinguished from record-entry time.
- Only fictional data will be used in this learning prototype.
- Clinical rules must be configurable and approved before any real clinical use.

## 3. Planned Tables

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

## 4. Implementation Status

The table definitions and relationships are documented in `data_model_relationships.md`.

The next development stage is to translate this design into an actual database structure, then test it using fictional records.
