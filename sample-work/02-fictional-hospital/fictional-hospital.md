# Walkthrough 02 — Hospital Patient-Record Access Control

## Lab Overview

### The Problem

A hospital keeps patient records for seven staff groups in one place, and each group needs a different slice of them:

- Clinical staff need to read every clinical department's records to treat a patient safely. A pharmacist checks the doctor's diagnosis and the patient's allergies; a dentist checks for medication that makes bleeding hard to stop.
- Each department must write only its own records, so no one can overwrite another department's notes, and a mistake can be traced to the department that made it.
- Clinical staff must not see administrative data such as home addresses, ID numbers and emergency contacts, and registration staff must not see clinical results.
- No staff group should be able to delete a record.

Generic groups such as `admins`, `finance` and `developers` can't express these rules, so staff end up with far more access than their job needs.

### What I Built

One S3 bucket organized by department, and seven IAM groups with one policy each. Clinical groups read all clinical records, write only their own department's, and can't delete. Patient registration reads and writes administrative records only.

### Result

90 IAM Policy Simulator tests, 51 expected allows and 39 expected denies. Every result matched. [Full test evidence](./part-3-phase-1-implementation-and-testing.md#permission-tests)

### Scope

- Approximately 10 fictional hospital workers
- Simplified patient-record storage in one Amazon S3 bucket
- Focused on AWS IAM users, groups, and policies
- Minimal supporting AWS resources
- Not a complete hospital application
- Not a claim of healthcare-regulation compliance

### Relationship to Walkthrough 01

Walkthrough 01 defines the account-wide setup and `gexter-boss`, the account administrator.

- The hospital is a tenant in the AWS account that `gexter-boss` owns.
- `gexter-boss` creates every AWS resource this lab requires, creates every IAM user, group, and policy in this lab, and removes them when the lab ends.
- `gexter-boss` is not a member of any hospital group and does not appear as a hospital worker.
- Account-wide billing, IAM administration, activity logging, and security monitoring stay with `gexter-boss` and are never granted to a hospital group.
- When this lab ends, all hospital resources, users, and groups are deleted. Only the account-wide setup survives.

## Lab Phases

| Phase | Scope | Status |
| ----- | ----- | ------ |
| Phase 1 — Department-level IAM authorization | Seven department groups, one S3 access policy each, 90 permission tests | Completed |
| Phase 2 — Duplicate-record verification and merge | Review flag, verifier and merge-operator groups, merge function | Planned |
| Phase 3 — Hospital IT operations and controlled deletion | Hospital IT permissions, `system-testing/` prefix, versioning and deletion behavior | Planned |

## Read the Lab

| Part | Contents |
| ---- | -------- |
| [Part 1 — Hospital Access Requirements and AWS Resource Model](./part-1-access-requirements-and-resource-model.md) | Hospital roles, S3 bucket and prefix structure, patient-record organization |
| [Part 2 — IAM Groups and Users](./part-2-iam-groups-and-users.md) | Phase 1 department groups, hospital tasks and business justification, users and group membership |
| [Part 3 — Phase 1 Implementation and Testing](./part-3-phase-1-implementation-and-testing.md) | Policies, how the permissions work, test method, 90 permission tests with evidence, Phase 1 conclusion |
| [Part 4 — Planned Phases](./part-4-planned-phases.md) | Duplicate-record workflow, hospital IT, hospital security, external access |

Supporting files:

- [`policies/`](./policies/) — Phase 1 IAM policy documents in JSON
- [`hexterika-patient-record-files/`](./hexterika-patient-record-files/) — fictional sample patient records uploaded to the bucket
- [`images/`](./images/) — build and test screenshots

## Key Technical Explanations

These sections explain the reasoning behind the Phase 1 permissions:

- **[Why department-first](./part-1-access-requirements-and-resource-model.md#why-department-first):** why S3 keys start with the department, and how that keeps each department's access to one Resource line in its policy.
- **[Cross-department read, own-department write](./part-3-phase-1-implementation-and-testing.md#cross-department-read-own-department-write):** why clinical groups can read every clinical department's records but write only to their own.
- **[Administrative access restrictions](./part-3-phase-1-implementation-and-testing.md#administrative-access-restrictions):** why clinical groups cannot reach administrative records, and how staff still know which patient a record belongs to.
- **[PutObject versus DeleteObject](./part-3-phase-1-implementation-and-testing.md#putobject-versus-deleteobject):** why writing is allowed and deleting is denied, and where versioning fits in.
- **[ListBucket restrictions](./part-3-phase-1-implementation-and-testing.md#listbucket-restrictions):** bucket ARN versus object ARN, and how the `s3:prefix` condition allows or denies a list request.
- **[Implicit versus explicit deny](./part-3-phase-1-implementation-and-testing.md#implicit-versus-explicit-deny):** the evaluation order, and why Phase 1 uses no explicit Deny.
- **[Test Method](./part-3-phase-1-implementation-and-testing.md#test-method):** what the IAM Policy Simulator results show and what they do not.

## Disclaimer

This is a fictional AWS IAM lab built to demonstrate identity and access management concepts such as least privilege, role-based access control, and separation of duties in a healthcare-inspired environment. While some security and privacy concepts are inspired by HIPAA, this lab is not intended to implement, demonstrate, or claim HIPAA compliance. The organizational structure, resources, permissions, workflows, and data used in this lab are simplified and fictional.

## Sources

- [Identification of Unidentified Patients — Model Policy (New York State, 2017)](https://health.ny.gov/professionals/hospital_administrator/letters/2017/docs/2017-01_id_of_unidentified_patients_model_policy.pdf)
- [Naming Convention for Unidentified Patients (ASPR TRACIE, U.S. Department of Health and Human Services)](https://files.asprtracie.hhs.gov/documents/aspr-tracie-ta---naming-convention-for-unidentified-patients---6-6-18.pdf)
- [NIST SP 800-53 Rev. 5 — Security and Privacy Controls for Information Systems and Organizations (AC-5 Separation of Duties)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-53r5.pdf)
