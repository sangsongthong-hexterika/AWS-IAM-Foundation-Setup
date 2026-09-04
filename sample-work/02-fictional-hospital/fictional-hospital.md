# Fictional Hospital (Old)

This lab use a hospital as a base to set AWS IAM permission to demonstrate that a hospital is different from other organization enough that using a generic groups such as `admins`, `finance`, and `developers` is too generic. Note that this hospital may not be an exact replica of a real hospital.

## Objective

+ To show the understanding that each type of different companies or organizations are unique and requires different IAM permission setup. In this document, it is a fictional hospital called Hexterika Hospital.

## Organizational Structure

**Model:** sensitive-data + clinical-role access

### Related Roles

+ Doctors
+ Nurses
+ Pharmacy
+ Laboratory
+ Radiology
+ Medical records
+ IT
+ Security
+ Compliance / audit

## My IAM Setup

### hexterika-patient-registration

This group allows patient-registration staff to create patient profiles and maintain identity, demographic, and contact information. It may also create profiles for unidentified patients and report suspected duplicates.

Allowed:

+ Search administrative patient information.
+ Create identified or unidentified patient profiles.
+ Update identity, demographic, and contact information.
+ Report suspected duplicate profiles.

Not allowed:

+ Access or modify clinical information.
+ Change internal patient IDs.
+ Delete patient profiles.
+ Approve or execute record merges.

### hexterika-record-merge-verifiers

This group allows selected hospital staff to investigate suspected duplicate patient profiles and approve or reject a proposed merge.

Allowed:

+ Compare identity information and limited record metadata.
+ Approve or reject a proposed merge.
+ Specify which patient record must survive.

Not allowed:

+ Execute the merge.
+ Modify administrative or clinical information.
+ Delete patient records.

### hexterika-record-merge-operators

This group allows selected hospital staff to execute a record merge that has already been approved by an authorized verifier.

Allowed:

+ View the approved merge request.
+ Execute the exact approved merge.
+ Record the result for auditing.

Not allowed:

+ Approve a merge.
+ Change the approved records or merge direction.
+ Modify patient information independently.
+ Delete unrelated records.

### Separation of duties

A person must not belong to both merge groups. Patient registration reports the duplicate, a verifier approves the merge, and a different operator executes it.

### hexterika-doctors

This grants access to the doctors to add their diagnostic to each patient in the database. This is different from the nurses group because a nurse may allow to write down their check-up information but not diagnose the patient the same level as a doctor can do. This prevents the conflict of duties problem as well as safeguarding the patient that only a license medical doctor is allowed to write the diagnosis to a patient and not just anyone who works at the hospital can write everything on the patient.

The permissions I assigned to this groups are:

#### hexterika-nurses

This grants access to the nurses to record that they treat X to patient Y at time Z. An example of this is when Nurse Joy records that she treats Ash Ketchup by taking his body temperature, his blood pressure, and giving him morphine injection to him according to Doctor Who Smith at 10:30 AM on August 1, 2026. This means nurse Joy isn't allowed to diagnose Ash Ketchup and has to read the diagnostic and order from Doctor Who Smith from the database and records the data. This prevents Nurse Joy from giving Ash Ketchup a Cyanize injection and get away with it. As for the prescription drug, Nurse Joy will get it from a Pharmacist who reads Ash Ketchup file. This will prevent her from giving the wrong drug if the pharmacist gives her the wrong drug and protect the pharmacist if they give the correct drug but Nurse Joy switches it out later.

The permissions I assigned to this groups are:

#### hexterika-pharmacists

This grants access to the pharmacists to record their prescription stocks, read the patient file, and assign the correct prescription to the patient. In this lab, everything will be assumed to be typing into each patient database directly, however, in real world practice, a QR code or a barcode scan is recommended to maintain the correct record of the prescription given and to which nurse at what time.

The permissions I assigned to this groups are:

#### hexterika-IT-security

This grants access to the IT department staff at Hexterika Hospital to maintain the overall digital databases at Hexterika Hospital. This includes reseting staff's username and passwords databases and keep them secure, check wifi and LAN internet throughout the hospital, etc. These group of people cannot read nor write the patient databases but they can set up and encrypt the databases.

The permissions I assigned to this groups are:

#### hexterika-securityguards

This grants the permission for the securityguard to escort and help the patients as well as take them to the appropriate rooms and help with the generic physical access throughout the hospital. This is where the keycard access allows them to enter a room or not.

The permissions I assigned to this groups are:

#### hexterika-laboratory

This grants the permission to the people who works at the lab so they can write their records to the patient file. An example is a lab technician can write the blood test result, urine test result, hormornes test result, etc.

The permissions I assigned to this groups are:

#### hexterika-auditors

This grants the permission to the auditors to check for compliances to uphold the standard of the sensitive medical data information such as people who check if the hospital comply to HIPAA or not or the penetration tester or the vulnerability assessment people.

The permissions I assigned to this groups are:

## Note

In this labs, all the databases will be set to its simplest form that can be written directly by typing.

This is a fictional hospital that I set the IAM permission based on the idea above. This does not mean a real world hospital will follow this exact pattern but it shows my understanding that each organization has their own unique structure and requirements.

# Fictional Hospital

## Lab Overview

### Objective

Explain that this lab demonstrates organization-specific IAM design for a small fictional hospital instead of relying on generic groups such as administrators, developers, and finance.

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

Keep the existing short design-decision text referencing Nova Scotia Health and the U.S. Indian Health Service here.

Do not include the long unidentified-patient workflow, complete HIM department structure, or detailed medical-record procedures.

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

Briefly describe the group in one or two sentences.

| Hospital task | Actual AWS IAM permission | Business justification |
| --- | --- | --- |

### hexterika-doctors

Briefly describe the group in one or two sentences.

| Hospital task | Actual AWS IAM permission | Business justification |
| --- | --- | --- |

### hexterika-nurses

Briefly describe the group in one or two sentences.

| Hospital task | Actual AWS IAM permission | Business justification |
| --- | --- | --- |

### hexterika-pharmacists

Briefly describe the group in one or two sentences.

| Hospital task | Actual AWS IAM permission | Business justification |
| --- | --- | --- |

### hexterika-laboratory

Briefly describe the group in one or two sentences.

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

Real hospitals handle unidentified patients and duplicate-record merging according to their own jurisdiction, staffing, and information systems. A Nova Scotia Health policy demonstrates the use of assigned identities and medical-record numbers for unidentified patients, while the U.S. Indian Health Service recommends separating record verification from merge execution between two users.

These sources are used as practical references, not as universal requirements. Because Hexterika Hospital is a fictional small hospital with approximately 10 workers, this lab assigns duplicate-record responsibilities to existing authorized roles instead of creating a dedicated Health Information Management department. The selected workflow still separates reporting, approval, and technical execution so that one person does not control the entire merge process.

Sources: [Nova Scotia Health Patient Identification Policy](https://policy.nshealth.ca/Site_Published/IWK/document_render.aspx?documentRender.GenericField=&documentRender.Id=110343&documentRender.IdType=6) and [U.S. Indian Health Service Patient Merge Policy](https://www.ihs.gov/ehr/ftpfiles/?download=1&flname=17_2_IHS_PatientMergePolicy.pdf&p=ehr%5CTraining%5CManuals%5CEHR+MU+for+HIM+Training_May+2013%5CTAB-17+-+Patient+Merge%5C17_2_IHS_PatientMergePolicy.pdf).
