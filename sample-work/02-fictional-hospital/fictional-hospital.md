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
| Create a patient profile | - | Create a patient profile to keep patient's treatment record data organized |
| Read administrative record of a patient profile | - | Read a patient administrative information such as identity, demographic, and contact information |
| Write a patient profile | - | Write and modify patient administrative information such as update their contact information and identity |
| Create a profile for an unindentify patient | - | In case an unidentify patient shows up, an unidentified patient profile can be created to keep the record of the treatment and to be merged later. I think this is the same as creating a patient profile but the identity requirement is not required at the moment and it can be updated later. This should simplify the type of record to be only 1 type and allow the merge to help with the duplicated. |
| No reading permission to a patient's clinical data | - | The patient registeration role can create a patient profile and read the administrative data but not the clinical data. This means she can read and modify only some part of the patient profile so it may or may not be necessary to have a no read permission to block the read and write access to some part of a patient profile. |
| Can flag a duplicate patient profile to review and merge but no merge permission | - | Can look at the file and flag it for duplicate but not merge. The merging process needs to be reviewed by a person with permission to authorize the merge. |

### hexterika-doctors

This grants access to the doctors to add their diagnostic to each patient in the database. This is different from the nurses group because a nurse may allow to write down their check-up information but not diagnose the patient the same level as a doctor can do. This prevents the conflict of duties problem as well as safeguarding the patient that only a license medical doctor is allowed to write the diagnosis to a patient and not just anyone who works at the hospital can write everything on the patient.

| Hospital task | Actual AWS IAM permission | Business justification |
| --- | --- | --- |
| Can read all the clinical data of the patient profile but not the administrative part | - | A doctor can read all the clinical data record of a patient profile but not the administrative part because a doctor's task is to heal people. They are not responsible to work on the administrative part as well as to prevent doctor-patient bias from the administrative profile. |
| Can write to a patient's doctor's treatment part of the patient's clinical profile | - | A doctor can read all the clinical data of the patient but can only write to their specific doctor's diagonosis section. This support the separation of duties because a doctor and a pharmacist specialized in different area. This helps ensure that the patient's get high standard treatment. |

### hexterika-nurses

This grants access to the nurses to record that they treat X to patient Y at time Z. An example of this is when Nurse Joy records that she treats Ash Ketchup by taking his body temperature, his blood pressure, and giving him morphine injection to him according to Doctor Who Smith at 10:30 AM on August 1, 2026. This means nurse Joy isn't allowed to diagnose Ash Ketchup and has to read the diagnostic and order from Doctor Who Smith from the database and records the data. This prevents Nurse Joy from giving Ash Ketchup a Cyanize injection and get away with it. As for the prescription drug, Nurse Joy will get it from a Pharmacist who reads Ash Ketchup file. This will prevent her from giving the wrong drug if the pharmacist gives her the wrong drug and protect the pharmacist if they give the correct drug but Nurse Joy switches it out later.

| Hospital task | Actual AWS IAM permission | Business justification |
| --- | --- | --- |
| Can read all the clinical data of the patient profile but not the administrative part | - | A nurse can read all the clinical data record of a patient profile but not the administrative part because a nurse's task is to give the patient their treatment according to the doctor or giving the patient their medication according to the pharmacist. They are not responsible to work on the administrative part as well as to prevent nurse-patient bias. |
| Can write to the nurse part of the clinical data of a patient profile | - | This record the nurse's work as an evidence of their work such as when they check up on the patient, what medication or treatment they give to the patient, is it the correct treatment according to the doctor and the pharmacist. If the nurse does everything correctly, it can retrace the step to check if the pharmacist or the doctor screw up. This benefits all of the clinical professional so all of them have their records of work so they can re-trace the mistakes if it happens and hopefully it can be fixed in time. |

### hexterika-pharmacists

This grants access to the pharmacists to record their prescription stocks, read the patient file, and assign the correct prescription to the patient. In this lab, everything will be assumed to be typing into each patient database directly, however, in real world practice, a QR code or a barcode scan is recommended to maintain the correct record of the prescription given and to which nurse at what time.

| Hospital task | Actual AWS IAM permission | Business justification |
| --- | --- | --- |
| Can read all the clinical data of the patient profile but not the administrative part | - | A pharmacist can read all the clinical data record of a patient profile but not the administrative part because a pharmacist's task is to assign the suitable medication to the patient according to the doctor's diagnosis and assign the correct medication to the nurse so the nurse can deliver them to the patient correctly. They are not responsible to work on the administrative part to prevent pharmacist-patient bias from the administrative profile's data. |
| Can write to the pharmacist part of the patient clinical data profile | - | This allows a pharmacist to decide the suitable medication to the patient according to the doctor's diagnosis, or to check if the medication assign by the doctor is suitable to the patient, to see if the patient has any medication allergy so they can assign the suitable medication, and to give the correct drug to the nurse so the nurse can deliver it to the patient for an in-hospital patient and to give it to the correct patient directly for an out-patient at the counter |

### hexterika-laboratory

This grants the permission to the people who works at the lab so they can write their records to the patient file. An example is a lab technician can write the blood test result, urine test result, hormornes test result, etc.

| Hospital task | Actual AWS IAM permission | Business justification |
| --- | --- | --- |
| Can read the patient's clinical data's doctor's diagnosis part | - | This is so they can perform the correct lab test from the patient's samples such as urine, blood, etc. This role has no need to gain administrative data read access of the patient profile. |
| Can write to a patient's clinical data's lab result part | - | This allows the lab data to be recorded to the patient |

### hexterika-radiology

Briefly describe the group in one or two sentences.

| Hospital task | Actual AWS IAM permission | Business justification |
| --- | --- | --- |
| Can read the patient's clinical data's doctor's diagnosis part | - | This is so they can perform the correct radiology test from the patient such as performing a left foot X-ray for a patient who fell of a horse to confirm a broken foot. This role has no need to gain administrative data read access of the patient profile. |
| Can write to a patient's clinical data's radiology result part | - | This allows the radiology data to be recorded to the patient profile such as an X-ray result |

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
