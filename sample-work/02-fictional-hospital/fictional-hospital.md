# Walkthrough 02 — Fictional Hospital

## Lab Overview

### Objective

This lab uses a hospital as a base to set AWS IAM permissions and to demonstrate that a hospital is different enough from other organizations that generic groups such as admins, finance, and developers are too generic. Note that this hospital may not be an exact replica of a real hospital.

### Scope

- Approximately 10 fictional hospital workers
- Simplified patient-record storage
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

Group names are prefixed `hospital-` where the role name is generic enough to recur in another organization, such as IT and security. Role names specific to a hospital, such as doctors or pharmacists, are not prefixed.

---

## Hospital Access Requirements

This section explains the hospital sufficiently to make IAM decisions. It must not become a complete operating model.

### Simplified Patient-Record Areas

- Administrative patient information
- Doctors' records
- Nursing records
- Pharmacy records
- Laboratory records
- Radiology records
- Record-merge requests and approvals
- Hospital Roles

### Hospital Roles

|       Role           |                                     Required access                                    |
| -------------------- | -------------------------------------------------------------------------------------- |
| Patient registration | Create and maintain administrative patient information                                 |
| Doctors              | Read relevant patient information and maintain doctors' clinical records               |
| Nurses               | Read authorized clinical instructions and maintain nursing-treatment records           |
| Pharmacists          | Read prescriptions and maintain medication-dispensing records                          |
| Laboratory staff     | Read laboratory orders and maintain laboratory results                                 |
| Radiology staff      | Read imaging orders and maintain radiology results                                     |
| IT                   | Operate hospital technology without routine access to patient-record contents          |
| External access      | Receive temporary, engagement-specific access for administration, assessment, or audit |

### Record-Merge Design Decision

Real hospitals handle unidentified patients and duplicate-record merging according to their jurisdiction, staffing, and information systems. Nova Scotia Health demonstrates the use of assigned identities and medical-record numbers for unidentified patients, while the U.S. Indian Health Service recommends separating record verification from merge execution between two users.

These practices are references, not universal requirements. Because Hexterika Hospital is a fictional small hospital with approximately 10 workers, duplicate-record responsibilities are assigned to existing authorized roles instead of a dedicated Health Information Management department. Reporting, approval, and technical execution remain separated so that one person does not control the entire process.

### AWS Resource Model

This section identifies the minimum AWS resources used to make the IAM policies real. Resources start at zero. A resource is added only when a group duty cannot be expressed without it.

#### Amazon S3 (+1)

One S3 bucket represents the hospital's simplified record storage.

```text
hexterika-hospital-records/
├── administrative/
├── doctors/
├── nursing/
├── pharmacy/
├── laboratory/
├── radiology/
├── merge-requests/
├── merge-approvals/
├── merge-results/
└── system-testing/
```

#### AWS Lambda (+1)

One Lambda function handles the outcome of a merge decision.

This is required because the merge operator must move records without holding read access to record contents. The function holds the access; the operator holds only the permission to invoke it.

The function reads the verifier's decision and takes one of two branches. An approved decision merges the records and clears the `merge-review` tag. A rejected decision clears the tag only. Both branches close the verifier's read window automatically, and the operator cannot choose which one runs.

### Record-Review Flag

Suspected duplicates are marked with an S3 object tag, `merge-review=open`, applied to the administrative and clinical records of both reported profiles.

The tag is what opens the verifier's read access. Verifier permissions are written with the `s3:ExistingObjectTag/merge-review` condition, so membership in the verifier group grants no standing access to any record. Without a flag raised by patient registration, a verifier can open nothing.

The flag lives on the records themselves rather than only in `merge-requests/` because IAM evaluates conditions against the object being requested. There is no cross-object lookup at policy-evaluation time, so a request record sitting elsewhere in the bucket cannot gate access to a patient record.

Object tagging is a feature of S3, not an additional service, so the resource count does not change.

### Resource Count

|       Resource        |                   Required by                | Running total |
| --------------------- | -------------------------------------------- | ------------- |
| S3 bucket             | All record-handling groups                   | 1             |
| Lambda merge function | `hexterika-duplicate-record-merge-operators` | 2             |

Services not added: CloudTrail, Config, Security Hub, and KMS. Activity logging and security monitoring are account-wide and belong to `gexter-boss` under Walkthrough 01. Encryption management is not yet required by any hospital group duty.

---

## IAM Setup

This section contains only deployable IAM groups and their real AWS permissions.

### hexterika-patient-registration

This group allows patient-registration staff to create patient profiles and maintain identity, demographic, and contact information. It may also create profiles for unidentified patients and report suspected duplicates.

| Hospital task | Actual AWS IAM permission | Business justification |
| ------------- | ------------------------- | ---------------------- |
| Create a patient profile | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | Create a patient profile to keep the patient's treatment record data organized |
| Read the administrative record of a patient profile | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | Read patient administrative information such as identity, demographic, and contact information |
| Write a patient profile | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | Write and modify patient administrative information, such as updating contact information and identity |
| Create a profile for an unidentified patient | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | In case an unidentified patient shows up, an unidentified patient profile can be created to keep the record of the treatment and to be merged later. This is the same as creating a patient profile, except the identity requirement is not required at the moment and can be updated later. This keeps the record type to only one type and lets the merge handle the duplicate. |
| No reading permission to a patient's clinical data | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | The patient registration role can create a patient profile and read the administrative data but not the clinical data. This means she can read and modify only some part of the patient profile, so a no-read permission may or may not be necessary to block read and write access to some part of a patient profile. |
| Can flag a duplicate patient profile for review and merge, but no merge permission | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | Can look at the file and flag it as a duplicate but not merge it. The merging process needs to be reviewed by a person with permission to authorize the merge. |
| Raise the review flag on both reported profiles by tagging their administrative and clinical records | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | Registration are the ones who meet patients and maintain identity details, so they are the ones who notice that two records look like one person. Raising the flag is also what opens the verifier's read access, so the verifier can never open a patient's records on their own initiative. Registration can set only this one tag and cannot read clinical data, so raising a flag gives them nothing they did not already have. |
| Cannot clear the review flag | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | Registration opens the window and nothing more. Closing it belongs to the merge function, which runs on both an approval and a rejection, so the window never stays open because someone forgot to close it by hand. |

### hexterika-duplicate-record-merge-operators

- Contains only selected IT personnel.
- Membership is additional to their normal IT group membership.
- Not every IT worker receives merge authority.
- Operators act on an already-decided merge request through a controlled function.
- They cannot approve or reject merge requests.
- They should not receive routine direct access to patient-record contents merely because they operate the merge.

| Hospital task | Actual AWS IAM permission | Business justification |
| ------------- | ------------------------- | ---------------------- |
| Read the verifier's decision on a merge request | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | The operator has to know which request was decided, by whom, and which two records it covers. They act on the written decision, not on someone walking over and asking. This also means the decision exists as a record before anything happens to the data. |
| Invoke the merge function for an approved or a rejected decision | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | The merge is technical work, so it goes to someone who understands what happens to the data. Routing it through a function means the operator never holds the underlying record access themselves. The function reads the decision and either merges the records and clears the review flag, or clears the flag only, so the operator holds one permission and no choice over which branch runs. Rejections go through the same function so that closing the review window never depends on anyone remembering to do it. |
| Cannot approve or reject a merge, including one they will act on | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | If the same person can decide and act, the decision step is decoration. This is the control the whole merge chain exists to create. |
| Cannot invoke the function without a recorded verifier decision | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | Stops an operator from merging two records because someone asked them to as a favour, or because they believe the records match. Deciding that two records are the same patient is a clinical and administrative judgement, not a technical one. |
| No routine read access to patient-record contents | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | Operating the merge does not require reading a diagnosis, a prescription, or a lab result. Granting record access to the person who runs the tooling would give one technician a reason-free view of every patient in the hospital. |
| Cannot raise or clear the review flag directly | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | The flag is cleared by the function, not by a separate action the operator chooses to take. An operator who could clear flags by hand could also leave one open, or close one before a verifier had finished reading. |
| Membership is separate from the IT group | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | Merge authority is given to specific people, not to everyone who works in IT. Keeping it in its own group means it can be granted and removed without changing anyone's IT access. |

### hexterika-duplicate-record-verifiers

Contains selected hospital personnel trusted to verify and approve merges.
It is permission-based, not tied to a department or profession.
A doctor may handle ordinary cases.
Another suitable authorized person could handle exceptional cases involving legal, forensic, or administrative concerns.
Verifiers approve or reject; they do not execute the merge.

One important distinction: a real hospital could choose the appropriate verifier case by case, but this AWS lab must demonstrate a concrete setup. One fictional doctor is assigned as the ordinary verifier, while exceptional cases may require a different authorized verifier.

The resulting control chain is:

Patient registration flags → selected verifier approves or rejects → selected IT merge operator invokes the merge function → the function merges and clears the flag, or clears the flag only → verifier confirms an executed merge

No individual holds both verification and merge-operation permissions.

| Hospital task | Actual AWS IAM permission | Business justification |
| ------------- | ------------------------- | ---------------------- |
| Read a reported duplicate-record request | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | The verifier has to see what was reported and why before deciding anything. The request carries the two record identifiers and the reason registration believes they describe one person. |
| Read the administrative records of both flagged profiles, only while the review flag is set | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | Deciding that two records are the same patient needs identity, demographic, and contact details, which live on the administrative side. This is wider access than the verifier's own profession normally gets, so it is not standing access: it opens when registration raises the flag and closes when the flag clears. |
| Read the clinical records of both flagged profiles, only while the review flag is set | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | Clinical history is the other half of the match. Two people can share a name and a birth year, and administrative details alone will produce a wrong merge. The group grants this half as well as the administrative half so that membership means the same access for every member, including a non-doctor verifier handling a legal or forensic case who holds no clinical read from anywhere else. |
| Approve or reject a merge request | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | The approval is the decision point. Registration reports what they noticed, but registration cannot see clinical data and so cannot confirm it. The verifier can see both and is accountable for the call. |
| Cannot execute the merge | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | Keeps the decision and the action in two different hands. A verifier who could also execute would only need to convince themselves. |
| Cannot create a merge request | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | The report comes from registration, who maintain the administrative records and are the ones who encounter duplicates during their normal work. Separating the report from the approval means the verifier is always reviewing someone else's observation. |
| Cannot raise or clear the review flag | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | If a verifier could set the flag, they would be granting themselves the access the flag controls, and the prerequisite would mean nothing. The flag has to be raised by someone who gains nothing from raising it. |
| Read the record of an executed merge and confirm it matched the approval | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | Without this, approval and execution are never compared and nobody would notice a merge that ran against the wrong pair of records. The verifier is checking the operator's work, not their own, so this does not collapse the separation. |

### hexterika-doctors

This grants access to the doctors to add their diagnosis to each patient in the database. This is different from the nurses group because a nurse may be allowed to write down their check-up information but not diagnose the patient at the same level a doctor can. This prevents the conflict of duties problem and safeguards the patient, so that only a licensed medical doctor is allowed to write the diagnosis and not just anyone who works at the hospital can write everything on the patient.

| Hospital task | Actual AWS IAM permission | Business justification |
| ------------- | ------------------------- | ---------------------- |
| Can read all the clinical data of the patient profile but not the administrative part | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | A doctor can read all the clinical data of a patient profile but not the administrative part because a doctor's task is to heal people. They are not responsible for the administrative part, and this prevents doctor-patient bias from the administrative profile. |
| Can write to a patient's doctor's treatment part of the patient's clinical profile | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | A doctor can read all the clinical data of the patient but can only write to their specific doctor's diagnosis section. This supports the separation of duties because a doctor and a pharmacist specialize in different areas. This helps ensure that patients get high-standard treatment. |

### hexterika-nurses

This grants access to the nurses to record that they treat X to patient Y at time Z. An example of this is when Nurse Joy records that she treats Ash Ketchup by taking his body temperature, his blood pressure, and giving him a morphine injection according to Doctor Who Smith at 10:30 AM on August 1, 2026. This means Nurse Joy isn't allowed to diagnose Ash Ketchup and has to read the diagnosis and order from Doctor Who Smith from the database and record the data. This prevents Nurse Joy from giving Ash Ketchup a cyanide injection and getting away with it. As for the prescription drug, Nurse Joy will get it from a pharmacist who reads Ash Ketchup's file. This will prevent her from giving the wrong drug if the pharmacist gives her the wrong drug, and protect the pharmacist if they give the correct drug but Nurse Joy switches it out later.

| Hospital task | Actual AWS IAM permission | Business justification |
| ------------- | ------------------------- | ---------------------- |
| Can read all the clinical data of the patient profile but not the administrative part | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | A nurse can read all the clinical data of a patient profile but not the administrative part because a nurse's task is to give the patient their treatment according to the doctor, or their medication according to the pharmacist. They are not responsible for the administrative part, and this prevents nurse-patient bias. |
| Can write to the nurse part of the clinical data of a patient profile | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | This records the nurse's work as evidence: when they check on the patient, what medication or treatment they give, and whether it matches what the doctor and the pharmacist ordered. If the nurse does everything correctly, the steps can be retraced to check whether the pharmacist or the doctor made the error. This benefits all the clinical professionals, because each of them has their own record of work and mistakes can be retraced and hopefully fixed in time. |

### hexterika-pharmacists

This grants access to the pharmacists to record their prescription stocks, read the patient file, and assign the correct prescription to the patient. In this lab, everything is assumed to be typed into each patient database directly. In real-world practice, a QR code or barcode scan is recommended to maintain the correct record of the prescription given, to which nurse, and at what time.

| Hospital task | Actual AWS IAM permission | Business justification |
| ------------- | ------------------------- | ---------------------- |
| Can read all the clinical data of the patient profile but not the administrative part | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | A pharmacist can read all the clinical data of a patient profile but not the administrative part because a pharmacist's task is to assign suitable medication according to the doctor's diagnosis and hand the correct medication to the nurse for delivery. They are not responsible for the administrative part, and this prevents pharmacist-patient bias from the administrative profile's data. |
| Can write to the pharmacist part of the patient clinical data profile | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | This allows a pharmacist to decide the suitable medication according to the doctor's diagnosis, to check whether medication assigned by the doctor is suitable, to see whether the patient has any medication allergy, and to give the correct drug to the nurse for an in-hospital patient or directly to the correct patient at the counter for an out-patient. |

### hexterika-laboratory

This grants permission to the people who work at the lab so they can write their records to the patient file. An example is a lab technician writing a blood test result, urine test result, hormone test result, and so on.

| Hospital task | Actual AWS IAM permission | Business justification |
| ------------- | ------------------------- | ---------------------- |
| Can read the patient's clinical data's doctor's diagnosis part | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | This is so they can perform the correct lab test from the patient's samples such as urine, blood, and so on. This role has no need for administrative read access to the patient profile. |
| Can write to a patient's clinical data's lab result part | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | This allows the lab data to be recorded to the patient |

### hexterika-radiology

This grants permission to radiology staff to read the doctor's imaging order and write the imaging result to the patient's record. It is kept separate from laboratory because the two produce different result types, and neither has a reason to write into the other's section.

| Hospital task | Actual AWS IAM permission | Business justification |
| ------------- | ------------------------- | ---------------------- |
| Can read the patient's clinical data's doctor's diagnosis part | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | This is so they can perform the correct radiology test, such as a left foot X-ray for a patient who fell off a horse, to confirm a broken foot. This role has no need for administrative read access to the patient profile. |
| Can write to a patient's clinical data's radiology result part | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | This allows the radiology data to be recorded to the patient profile, such as an X-ray result |

### hexterika-hospital-it

This group operates the hospital technology inside the AWS environment the hospital was given. It does not create AWS resources, does not administer identities, and does not read patient-record contents.

| Hospital task | Actual AWS IAM permission | Business justification |
| ------------- | ------------------------- | ---------------------- |
| Operate the hospital's record storage without reading record contents | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | IT keeps storage available, configured, and recoverable. None of that requires reading a diagnosis or a lab result. Granting record access to keep storage working would hand one technician a view of every patient in the hospital with no clinical reason attached to it. |
| Use a separate testing area to verify configuration and access | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | IT needs somewhere to upload, retrieve, and delete objects to confirm that settings and permissions actually behave as intended. Doing that against live patient records risks damaging or exposing them, so `system-testing/` exists to make technical verification possible without touching real records. |
| Cannot create AWS resources or add new services | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | The hospital is a tenant in an AWS account it does not own. Resource creation belongs to the account owner. This also keeps the hospital's resource count a deliberate decision rather than something that grows whenever a technician decides something would be handy. |
| Cannot create, modify, or delete IAM users, groups, or policies | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | Identity administration belongs to the account owner. If IT could edit IAM, every separation of duties in this lab would be advisory only, because IT could write itself whatever permission it was missing. |
| Cannot add itself or anyone else to a group | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | Stated separately from the policy restriction because it is the specific failure this lab is built to prevent. An IT member who can add themselves to the verifier group breaks the merge control chain without ever touching a policy document. |
| Cannot approve a record merge | - | Approving a merge is a judgement about whether two records describe the same person. It needs the record contents IT deliberately does not have, and it needs clinical and administrative context IT does not hold. |
| Supplies the merge operators, but merge authority is a separate group | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | Executing a merge is technical work, so operators come from IT. Not every IT worker gets it, so it lives in its own group rather than being attached to the IT group by default. |
| Cannot view or change account-wide security, logging, or billing settings | `{"Version": "2012-10-17", "Statement": [ { "Sid": "", "Effect": "", "Action": "", "Resource": "" } ] }` | These belong to the account owner under Walkthrough 01. A tenant does not configure logging, findings, or cost controls for an account it does not own. |

### Hospital Security

The hospital does not have an internal security group.

Every AWS security surface in this lab — activity logging, findings, configuration review, and cost controls — is account-wide and belongs to `gexter-boss` under Walkthrough 01. A hospital group monitoring those would need account-wide read access, which duplicates the account owner and breaks least privilege, or it would receive nothing and exist as a group name with no access behind it.

For a hospital of approximately 10 workers, security assessment and audit are engagements rather than permanent staff duties. They are granted under Temporary External-Access Groups.

The internal oversight duty that would otherwise sit with a security group — confirming that an executed merge matched its approval — is assigned to `hexterika-duplicate-record-verifiers`. That keeps the check internal and keeps it in different hands from the operator who performed the merge.

### Temporary External-Access Groups

Add separate groups or roles here according to engagement type. Do not combine AWS administration, auditing, vulnerability assessment, and penetration testing into one permission set.

External personnel are not eligible for `hexterika-duplicate-record-merge-operators`. Executing a patient-record merge is a standing internal duty and is not granted through a temporary engagement.

### IAM Users and Group Membership

|           IAM user             |              Fictional position           |                          IAM group or groups                          |
| ------------------------------ | ----------------------------------------- | --------------------------------------------------------------------- |
| `hospital-reg-thea-queen`      | Patient registration officer              | `hexterika-patient-registration`                                      |
| `hospital-dr-who-smith`        | Attending doctor                          | `hexterika-doctors`                                                   |
| `hospital-dr-jack-harkness`    | Attending doctor, ordinary merge verifier | `hexterika-doctors`, `hexterika-duplicate-record-verifiers`           |
| `hospital-nurse-joy`           | Ward nurse                                | `hexterika-nurses`                                                    |
| `hospital-nurse-john-hart`     | Ward nurse                                | `hexterika-nurses`                                                    |
| `hospital-pharm-oliver-queen`  | Pharmacist                                | `hexterika-pharmacists`                                               |
| `hospital-lab-prof-oak`        | Laboratory technician                     | `hexterika-laboratory`                                                |
| `hospital-rad-chrollo`         | Radiology technician                      | `hexterika-radiology`                                                 |
| `hospital-it-felicity-smoak`   | IT technician                             | `hexterika-hospital-it`                                               |
| `hospital-it-charlie-bradbury` | IT technician, merge operator             | `hexterika-hospital-it`, `hexterika-duplicate-record-merge-operators` |

Every user carries the `hospital-` prefix so the whole lab can be identified and deleted at the end without touching the account-wide identities.

Two users demonstrate the merge chain. `hospital-dr-jack-harkness` approves, `hospital-it-charlie-bradbury` acts, and neither holds the other's permission. `hospital-dr-who-smith` and `hospital-it-felicity-smoak` exist to show that verification and merge authority are not granted by profession.

---

## Implementation and Testing

### Implementation Steps

Record the relevant resources, groups, policies, and users created in AWS.

### Permission Tests

| Test user | Attempted action | Expected result | Actual result |
| --------- | ---------------- | --------------- | ------------- |

### Screenshots

Attach implementation and permission-test screenshots here.

---

## Limitations

S3 and any additional services are simplified technical representations. Hospital-specific operations would normally be enforced by an application. This lab focuses on the AWS permissions used to access the supporting resources.

The review flag has no automatic expiry. IAM date conditions compare the current time against a value written in the policy, not against a value stored on an object, and S3 Lifecycle removes objects rather than tags. Both decided outcomes close through the merge function, so the remaining case is a verifier who never records a decision at all, which leaves the flag open. This is deliberately left outside the AWS boundary and handled as a hospital working procedure. Building a scheduled sweeper to close stale flags would add application code that no permission test could demonstrate.

IAM evaluates allows as a union, so the flag condition constrains only the access a member does not already hold from another group. A verifier who is also a doctor keeps ungated clinical read from the doctors group, and the flag effectively gates their administrative access alone. For a verifier who holds no other clinical access, the flag gates both halves. This is the intended result, not a gap in the condition.

Registration and a verifier acting together can open a review window with no genuine duplicate behind it. Two-person collusion is accepted and recorded rather than controlled at this hospital size.

## Disclaimer

This is a fictional AWS IAM lab designed to demonstrate identity and access management concepts such as least privilege, role-based access control, and separation of duties in a healthcare-inspired environment. While some security and privacy concepts are inspired by HIPAA, this lab is not intended to implement, demonstrate, or claim HIPAA compliance. The organizational structure, resources, permissions, workflows, and data used in this lab are simplified and fictional for educational purposes.

## Sources

[Nova Scotia Health Patient Identification Policy](https://policy.nshealth.ca/Site_Published/IWK/document_render.aspx?documentRender.GenericField=&documentRender.Id=110343&documentRender.IdType=6)

[U.S. Indian Health Service Patient Merge Policy](https://www.ihs.gov/ehr/ftpfiles/?download=1&flname=17_2_IHS_PatientMergePolicy.pdf&p=ehr%5CTraining%5CManuals%5CEHR+MU+for+HIM+Training_May+2013%5CTAB-17+-+Patient+Merge%5C17_2_IHS_PatientMergePolicy.pdf)
