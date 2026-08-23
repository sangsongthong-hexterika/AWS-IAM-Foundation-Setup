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

## My IAM Setup

### Groups And Their Definition

#### hexterika-staff

This grants access to staff personal data database resources so they can modify some of their personal data.

The permissions I assigned to this groups are:

#### hexterika-patients-med-records

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

This grants the permission to the auditors to check for compliances to uphold the standard of the sensitive medical data information such as people who check if the hospital comply to HIPPA or not or the penetration tester or the vulnerability assessment people.

The permissions I assigned to this groups are:

## Note

In this labs, all the databases will be set to its simplest form that can be written directly by typing.

This is a fictional hospital that I set the IAM permission based on the idea above. This does not mean a real world hospital will follow this exact pattern but it shows my understanding that each organization has their own unique structure and requirements.

## Note To Self

Setup the IAM groups, assign the permissions, and add the IAM users. Then, take screenshots. Remove this section once the screenshots are fully attached.
