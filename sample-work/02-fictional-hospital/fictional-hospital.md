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

### hexterika-patients-med-records

This grants access to create a patient record in the database for the database staff, but since they are not a doctor or a nurse, they cannot write any diagnosis to the patient.

The permissions I assigned to this groups are:

#### hexterika-doctors

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
