# Fictional Hospital

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

### Design decision

Real hospitals handle unidentified patients and duplicate-record merging according to their own jurisdiction, staffing, and information systems. A Nova Scotia Health policy demonstrates the use of assigned identities and medical-record numbers for unidentified patients, while the U.S. Indian Health Service recommends separating record verification from merge execution between two users.

These sources are used as practical references, not as universal requirements. Because Hexterika Hospital is a fictional small hospital with approximately 10 workers, this lab assigns duplicate-record responsibilities to existing authorized roles instead of creating a dedicated Health Information Management department. The selected workflow still separates reporting, approval, and technical execution so that one person does not control the entire merge process.

Sources: [Nova Scotia Health Patient Identification Policy](https://policy.nshealth.ca/Site_Published/IWK/document_render.aspx?documentRender.GenericField=&documentRender.Id=110343&documentRender.IdType=6) and [U.S. Indian Health Service Patient Merge Policy](https://www.ihs.gov/ehr/ftpfiles/?download=1&flname=17_2_IHS_PatientMergePolicy.pdf&p=ehr%5CTraining%5CManuals%5CEHR+MU+for+HIM+Training_May+2013%5CTAB-17+-+Patient+Merge%5C17_2_IHS_PatientMergePolicy.pdf).

### Important distinctions

#### Administrative profile

Internal patient ID
Name and known aliases
Date of birth
Address
Telephone number
Emergency contact
Nationality or citizenship information
Passport or national ID information
Identified/unidentified status

#### Clinical record

Diagnoses
Medical history
Doctors’ notes
Nursing observations and treatments
Laboratory results
Radiology results
Prescriptions and medication administration

Registration staff can access the first category, not the second. This follows the minimum-necessary principle: workforce access should be based on the information needed to perform the person’s job. (HHS guidance)

#### Unidentified-patient workflow

1. Registration staff search for a possible existing record.
2. If identity cannot be established, they create an unidentified patient profile.
3. The system assigns an immutable internal patient ID.
4. Clinical staff document treatment against that record.
5. Registration staff later add verified demographic and identity information.
6. If no previous profile exists, the same record continues as the permanent identified record.
7. If a previous profile exists, registration staff flag the records as a possible duplicate.
8. Registration staff cannot merge them.
9. Authorized HIM staff verify the match, and a different authorized HIM worker executes the merge.
10. Retention rule for this lab

Patient-registration staff have no deletion permission. Hexterika Hospital retains patient records under a separate retention policy, including records of deceased patients. The proposed “10+ years” should remain a fictional internal assumption until the lab selects a jurisdiction, because real retention periods vary by record type and governing law.

## My IAM Setup

### hexterika-patient-registration

hexterika-patient-registration grants authorized non-clinical staff access to create patient profiles and view or modify patient identity, demographic, and contact information. The group may create temporary profiles for unidentified patients and report suspected duplicate records. It cannot access clinical information, change internal patient identifiers, delete records, verify duplicates, or merge patient records.

#### Permitted actions

| Action | Scope and restrictions |
| --- | --- |
| Search for an existing patient | Search identity and demographic information before creating another record. |
| View administrative information | View patient name, date of birth, contact information, address, recorded identity documents, and internal patient ID. |
| Create an identified patient profile | Create a record when sufficient identity information is available. |
| Create an unidentified patient profile | Create an emergency record using an assigned placeholder identity when the patient cannot be identified. Real hospital policies use assigned naming formats and medical-record numbers for this situation. (Nova Scotia Health policy) |
| Update demographic information | Correct or update names, addresses, telephone numbers, emergency contacts, and similar non-clinical information. |
| Add identity evidence | Add a subsequently obtained passport number, citizenship ID, or other approved identity evidence. |
| Identify an unknown patient | Replace placeholder demographic information after the patient’s identity has been adequately verified. |
| Report possible duplicates | Flag two records that may represent the same person and send them to authorized HIM staff. |
| Read the internal patient ID | Use the hospital-generated identifier to locate and distinguish records. |

Patient registration being responsible for creating records and maintaining accurate patient details reflects actual medical-reception and registration work. (NHS receptionist example)

#### Prohibited actions

| Action | Reason |
| --- | --- |
| Change the internal patient ID | The hospital-generated identifier remains the stable reference for the record. |
| Delete a patient profile | Retention and eventual disposal are records-management functions, not registration duties. |
| Merge duplicate records | A false merge could combine two people’s clinical histories and may be irreversible. |
| Approve their own suspected duplicate | The HIM verification process must remain independent. |
| View diagnoses | Not required for patient registration. |
| View doctors’ clinical notes | Outside the group’s administrative purpose. |
| View nursing treatment records | Outside the group’s administrative purpose. |
| View laboratory results | Outside the group’s administrative purpose. |
| View prescriptions or medication records | Outside the group’s administrative purpose. |
| Create or modify clinical information | Reserved for the relevant clinical groups. |
| Change record-retention status | Reserved for authorized records-management or compliance staff. |
| Declare a patient deceased | This is a clinical determination, although registration staff might later update permitted administrative fields based on an authorized record. |
| Use fingerprints or dental records to make an independent clinical identity determination | They may flag or submit the evidence, but specialized verification belongs to authorized HIM or clinical personnel. |

### hexterika-him-verifiers

Full name: Hexterika Hospital Health Information Management Record Verifiers

hexterika-him-verifiers grants selected hospital personnel permission to investigate suspected duplicate patient profiles and approve or reject proposed record merges. Members of this group verify that the profiles represent the same person and specify which internal patient record must remain as the surviving record. They cannot execute merges.

#### Permitted actions HIM Verifier

| Action | Scope and restrictions |
| --- | --- |
| View suspected duplicate profiles | View the administrative identity information of profiles submitted for duplicate review. |
| Compare internal patient IDs | Compare the immutable hospital-generated identifiers belonging to the suspected duplicate profiles. |
| Review identity evidence | Compare names, aliases, dates of birth, addresses, contact information, national IDs, passport information, and other approved identity evidence. |
| View limited record metadata | Confirm that clinical records exist and compare relevant encounter dates or record history without modifying or unnecessarily reading clinical contents. |
| Request additional verification | Return an inconclusive case to registration staff or request assistance from authorized clinical personnel when identity cannot be safely confirmed. |
| Reject a proposed merge | Reject the request when the available evidence is insufficient, contradictory, or indicates that the profiles represent different people. |
| Approve a proposed merge | Record approval when sufficient evidence shows that the profiles represent the same patient. |
| Select the surviving record | Identify which internal patient record must remain active and which duplicate must be merged into it. |
| Submit an approved merge request | Send the approved record pair and merge direction to an authorized HIM merger operator. |

Prohibited actions
Action	Reason
Execute a record merge	Verification and execution must be performed by different authorized people.
Approve and execute the same merge	This would remove the two-person control.
Modify clinical information	Diagnoses, treatments, results, and prescriptions remain controlled by the relevant clinical groups.
Independently correct patient demographics	Corrections belong to patient-registration staff unless they form part of an authorized merge outcome.
Change an internal patient ID	Internal patient IDs are immutable system identifiers.
Delete a patient record	Record deletion is outside the duplicate-verification function.
Approve a merge without sufficient evidence	An incorrect merge could combine the clinical histories of different people.
Alter audit records	Verification decisions and related activity must remain traceable.

hexterika-him-mergers

Full name: Hexterika Hospital Health Information Management Record Merger Operators

hexterika-him-mergers grants selected hospital personnel permission to execute a duplicate-record merge that has already been verified and approved by an authorized HIM verifier. Members of this group cannot independently approve a merge or change the approved merge direction.

Permitted actions
Action	Scope and restrictions
View approved merge requests	View the approval, the two internal patient IDs, the selected surviving record, and the identity of the verifier.
Validate the merge request	Confirm that the request is complete, approved, and refers to the exact records selected by the verifier.
Return an invalid request	Reject or return a request that is incomplete, inconsistent, expired, or not approved by an authorized verifier.
Execute an approved merge	Merge the specified duplicate record into the specified surviving record.
Preserve the surviving internal patient ID	Ensure that the approved surviving record retains its original immutable internal patient ID.
Preserve record history	Ensure that the merge retains the required history and traceability of the former duplicate record.
Record the merge result	Produce an auditable record showing who executed the merge, when it occurred, which records were involved, and which record survived.
Prohibited actions
Action	Reason
Create or approve a merge request	Approval belongs to the separate HIM verifier group.
Execute an unapproved merge	Every merge requires prior approval from an authorized verifier.
Change the approved record pair	The operator may only merge the records named in the approval.
Reverse the approved merge direction	The verifier determines which record survives.
Substitute another surviving record	This would execute a different action from the one that was reviewed.
Modify patient demographics or clinical contents	The operator’s permission is limited to executing the approved merge.
Delete an unrelated patient record	Merge authority does not provide general record-deletion authority.
Alter or remove audit records	Merge activity must remain traceable.
Separation-of-duties rule

A person must not simultaneously belong to both hexterika-him-verifiers and hexterika-him-mergers. Registration staff may report suspected duplicates but cannot approve or execute merges.

The workflow requires two different authorized people:

A patient-registration worker reports a suspected duplicate.
An HIM verifier investigates the records and either rejects or approves the merge.
The verifier specifies the surviving record and submits the approved request.
A different HIM merger operator validates the approval and executes the specified merge.
The verification and execution activities are retained in the audit history.

This two-user structure is based on Indian Health Service guidance recommending that one user perform identification and verification while another user performs the actual merge because the merge may be irreversible. IHS Patient Merge Policy

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

## Disclaimer

This is a fictional AWS IAM lab designed to demonstrate identity and access management concepts such as least privilege, role-based access control, and separation of duties in a healthcare-inspired environment. While some security and privacy concepts are inspired by HIPAA, this lab is not intended to implement, demonstrate, or claim HIPAA compliance. The organizational structure, resources, permissions, workflows, and data used in this lab are simplified and fictional for educational purposes.

## Note To Self

Setup the IAM groups, assign the permissions, and add the IAM users. Then, take screenshots. Remove this section once the screenshots are fully attached.
