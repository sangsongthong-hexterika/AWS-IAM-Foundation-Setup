# Walkthrough 02 — Fictional Hospital

## Lab Overview

### Objective

This lab uses a hospital as a base to set AWS IAM permissions and to demonstrate that a hospital is different enough from other organizations that generic groups such as `admins`, `finance`, and `developers` are too generic. This hospital is simplified and is not an exact replica of a real hospital.

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

## Disclaimer

This is a fictional AWS IAM lab built to demonstrate identity and access management concepts such as least privilege, role-based access control, and separation of duties in a healthcare-inspired environment. While some security and privacy concepts are inspired by HIPAA, this lab is not intended to implement, demonstrate, or claim HIPAA compliance. The organizational structure, resources, permissions, workflows, and data used in this lab are simplified and fictional.

## Sources

[Nova Scotia Health Patient Identification Policy](https://policy.nshealth.ca/Site_Published/IWK/document_render.aspx?documentRender.GenericField=&documentRender.Id=110343&documentRender.IdType=6)

[U.S. Indian Health Service Patient Merge Policy](https://www.ihs.gov/ehr/ftpfiles/?download=1&flname=17_2_IHS_PatientMergePolicy.pdf&p=ehr%5CTraining%5CManuals%5CEHR+MU+for+HIM+Training_May+2013%5CTAB-17+-+Patient+Merge%5C17_2_IHS_PatientMergePolicy.pdf)
