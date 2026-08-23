# Fictional Bank

This lab use a bank as a base to set AWS IAM permission to demonstrate that a bank is different from other organization enough that using a generic groups such as `admins`, `finance`, and `developers` is too generic. Note that this bank may not be an exact replica of a real bank.

## Objective

+ To show the understanding that each type of different companies or organizations are unique and requires different IAM permission setup. In this document, it is a fictional bank called Hexterika Bank.

## Organizational Structure

Model: financial + segregation-of-duties access

Retail banking
Corporate banking
Payments
Risk
Fraud
Compliance
Internal audit
Security operations

## My IAM Setup

### Groups And Their Definition

#### hexterika-staff

This grants access to staff personal data database resources so they can modify some of their personal data.

The permissions I assigned to this groups are:

#### hexterika-securityguards

This grants the permission for the securityguard to escort and help the patients as well as take them to the appropriate rooms and help with the generic physical access throughout the hospital. This is where the keycard access allows them to enter a room or not.

The permissions I assigned to this groups are:

***Add More***

#### hexterika-auditors

This grants the permission to the auditors to check for compliances to uphold the standard of the sensitive financial data information such as people who check if the bank comply to the regulation such as DORA or not or the penetration tester or the vulnerability assessment people.

The permissions I assigned to this groups are:

## Note

In this labs, all the databases will be set to its simplest form that can be written directly by typing.

This is a fictional hospital that I set the IAM permission based on the idea above. This does not mean a real world bank will follow this exact pattern but it shows my understanding that each organization has their own unique structure and requirements.

## Note To Self

Add more groups

Setup the IAM groups, assign the permissions, and add the IAM users.

Then, take screenshots. Remove this section once the screenshots are fully attached.
