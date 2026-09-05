# Fictional Hospital

## Lab Overview

### Objective

This lab use a hospital as a base to set AWS IAM permission to demonstrate that a hospital is different from other organization enough that using a generic groups such as `admins`, `finance`, and `developers` is too generic. Note that this hospital may not be an exact replica of a real hospital.

### Scope

+ Approximately 10 fictional hospital workers
+ Simplified patient-record-record storage
+ Focused on AWS IAM users, groups, and policies
+ Minimal supporting AWS resources
+ Not a complete hospital application
+ Not a claim of healthcare-regulation compliance

## Hospital Access Requirements

This section explains the hospital sufficiently to make IAM decisions. It must not become a complete operating model.

### Simplified Patient-Record Areas

+ Administrative patient information
+ Doctors’ records
+ Nursing records
+ Pharmacy records
+ Laboratory records
+ Radiology records
+ Record-merge requests and approvals

### Hospital Roles

| Role | Required access |
| --- | --- |
| Patient registration | Create and maintain administrative patient information |
| Doctors | Read relevant patient information and maintain doctors’ clinical records |
| Nurses | Read authorized clinical instructions and maintain nursing-treatment records |
| Pharmacists | Read prescriptions and maintain medication-dispensing records |
| Laboratory staff | Read laboratory orders and maintain laboratory results |
| Radiology staff | Read imaging orders and maintain radiology results |
| IT | Maintain AWS infrastructure without routine access to patient-record contents |
| Security | Monitor AWS security without modifying patient records |
| External access | Receive temporary, engagement-specific access for administration, assessment, or audit |

### Record-Merge Design Decision

Real hospitals handle unidentified patients and duplicate-record merging according to their jurisdiction, staffing, and information systems. Nova Scotia Health demonstrates the use of assigned identities and medical-record numbers for unidentified patients, while the U.S. Indian Health Service recommends separating record verification from merge execution between two users.

These practices are references, not universal requirements. Because Hexterika Hospital is a fictional small hospital with approximately 10 workers, duplicate-record responsibilities are assigned to existing authorized roles instead of a dedicated Health Information Management department. Reporting, approval, and technical execution remain separated so that one person does not control the entire process.

## AWS Resource Model

This section identifies the minimum AWS resources used to make the IAM policies real.

### Amazon S3

One S3 bucket represents the hospital’s simplified record storage.

```text
hexterika-hospital-records/
├── administrative/
├── doctors/
├── nursing/
├── pharmacy/
├── laboratory/
├── radiology/
├── merge-requests/
└── merge-approvals/
```

### Additional AWS Resources

Add resources here only when a group requires them.

Examples may include:

AWS Lambda for a controlled application-level operation
AWS CloudTrail for activity records
AWS Config or Security Hub for security review
AWS KMS for encryption management

---

## IAM Setup

This section contains only deployable IAM groups and their real AWS permissions.

### hexterika-patient-registration

This group allows patient-registration staff to create patient profiles and maintain identity, demographic, and contact information. It may also create profiles for unidentified patients and report suspected duplicates.

| Hospital task | Actual AWS IAM permission | Business justification |
| --- | --- | --- |

### hexterika-doctors

This grants access to the doctors to add their diagnostic to each patient in the database. This is different from the nurses group because a nurse may allow to write down their check-up information but not diagnose the patient the same level as a doctor can do. This prevents the conflict of duties problem as well as safeguarding the patient that only a license medical doctor is allowed to write the diagnosis to a patient and not just anyone who works at the hospital can write everything on the patient.

| Hospital task | Actual AWS IAM permission | Business justification |
| --- | --- | --- |

### hexterika-nurses

This grants access to the nurses to record that they treat X to patient Y at time Z. An example of this is when Nurse Joy records that she treats Ash Ketchup by taking his body temperature, his blood pressure, and giving him morphine injection to him according to Doctor Who Smith at 10:30 AM on August 1, 2026. This means nurse Joy isn't allowed to diagnose Ash Ketchup and has to read the diagnostic and order from Doctor Who Smith from the database and records the data. This prevents Nurse Joy from giving Ash Ketchup a Cyanize injection and get away with it. As for the prescription drug, Nurse Joy will get it from a Pharmacist who reads Ash Ketchup file. This will prevent her from giving the wrong drug if the pharmacist gives her the wrong drug and protect the pharmacist if they give the correct drug but Nurse Joy switches it out later.

| Hospital task | Actual AWS IAM permission | Business justification |
| --- | --- | --- |

### hexterika-pharmacists

This grants access to the pharmacists to record their prescription stocks, read the patient file, and assign the correct prescription to the patient. In this lab, everything will be assumed to be typing into each patient database directly, however, in real world practice, a QR code or a barcode scan is recommended to maintain the correct record of the prescription given and to which nurse at what time.

| Hospital task | Actual AWS IAM permission | Business justification |
| --- | --- | --- |

### hexterika-laboratory

This grants the permission to the people who works at the lab so they can write their records to the patient file. An example is a lab technician can write the blood test result, urine test result, hormornes test result, etc.

| Hospital task | Actual AWS IAM permission | Business justification |
| --- | --- | --- |

### hexterika-radiology

Briefly describe the group in one or two sentences.

| Hospital task | Actual AWS IAM permission | Business justification |
| --- | --- | --- |

### hexterika-it

Briefly describe the group in one or two sentences.

| Hospital task | Actual AWS IAM permission | Business justification |
| --- | --- | --- |

### hexterika-security

Briefly describe the group in one or two sentences.

| Hospital task | Actual AWS IAM permission | Business justification |
| --- | --- | --- |

### Temporary External-Access Groups

Add separate groups or roles here according to engagement type. Do not combine AWS administration, auditing, vulnerability assessment, and penetration testing into one permission set.

### IAM Users and Group Membership

| IAM user | Fictional position | IAM group or groups |
| --- | --- | --- |

---

## Implementation and Testing

### Implementation Steps

Record the relevant resources, groups, policies, and users created in AWS.

### Permission Tests

| Test user | Attempted action | Expected result | Actual result |
| --- | --- | --- | --- |

### Screenshots

Attach implementation and permission-test screenshots here.

## Limitations

Explain that S3 and any additional services are simplified technical representations. Hospital-specific operations may normally be enforced by an application, while this lab focuses on the AWS permissions used to access supporting resources.

## Disclaimer

This is a fictional AWS IAM lab designed to demonstrate identity and access management concepts such as least privilege, role-based access control, and separation of duties in a healthcare-inspired environment. While some security and privacy concepts are inspired by HIPAA, this lab is not intended to implement, demonstrate, or claim HIPAA compliance. The organizational structure, resources, permissions, workflows, and data used in this lab are simplified and fictional for educational purposes.

## Sources

[Nova Scotia Health Patient Identification Policy](https://policy.nshealth.ca/Site_Published/IWK/document_render.aspx?documentRender.GenericField=&documentRender.Id=110343&documentRender.IdType=6)

[U.S. Indian Health Service Patient Merge Policy](https://www.ihs.gov/ehr/ftpfiles/?download=1&flname=17_2_IHS_PatientMergePolicy.pdf&p=ehr%5CTraining%5CManuals%5CEHR+MU+for+HIM+Training_May+2013%5CTAB-17+-+Patient+Merge%5C17_2_IHS_PatientMergePolicy.pdf).
