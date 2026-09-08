# AWS IAM Lab Part 1

## Overview

This was how I learned about AWS IAM by setting up the root account and created other accounts with different detail. The overview idea is to start off by setting up the root account. Then, set other accounts with slight variation such as adding it to Administrator Access group or not adding it there or set a password vs passwordless.

## Creating Root Account & Post Initial Setup

This account is the first account that the owner of the company or an AWS user needs to create in order to use the services unless they are the user at a company who only need to sign-in to work as a normal user. Basically, whoever creates and/or have accessed to this account is the most powerful person who can control the AWS usage of the whole company. This person is also responsible to manage the billing of the services. This account isn't a day-to-day account.

Basically in this step, I start from signing up for the root account. The log in as the root account. Then setup the MFA and billing. ***Talk about the how to with screenshots***

### The tasks that can only be performed by the root account

+ Closing the AWS account
+ Changing or viewing payment methods (Manage billing)
+ Changing the root account's email address
+ Enabling/disabling MFA on the root account
+ Activating IAM user access to billing console - Only the root account can grant other IAM account the right to access the billing console. After that an IAM user with AdministratorAccess can add that specific IAM user who has access to the billing console to the group with *read-only* or *read & write* the billing regardless if the IAM user with AdministratorAccess is being grant access to the billing console or not. This is a part of separation of duties. Think of it as the IAM users with AdministratorAccess has the power to add accountant1 to the group that has permission to read and write the billing, in this case called the finance, but that accountant1 does not have the power to add other IAM users to the finance group. After root enables IAM billing access, an IAM user with billing permissions can view billing, but the activation itself requires root. More on this in part 2.

### Special groups & policies that needs to be created by the root account

This group is called the AdministratorAccess group.

Q: Why is this group so special?
A: It is because technically any IAM account with AdministratorAccess policy can create this group, but the best practice is to create a policy and attach it to a group. This means if the group is created by the root account, then, there is no need to re-create this group again even if in theory the IAM user with AdministratorAccess are trusted by the root account, therefore, have the rights to create any group or policy including creating another AdministratorAccess group. By following this best practice, you can avoid getting confuse and able to manage other IAM users with AdministratorAccess in a more organized and more structured way than manually attached the policy to each one of them. More on this in part 2.

## Things That An IAM Account With AdministratorAccess Can And Cannot Do

### The ***CAN DO***

+ Create other IAM accounts
+ Delete other IAM accounts
+ Delete itself (AWS will show a warning.)
+ Create policies
+ Create groups
+ Attach policies to groups

## Create AdministratorAccess Group Policy (Need To Be Done From Root Account)

## Create A Group With The Policy That Grant The Accounts ***READ-ONLY*** To View The Billing But Not Managing

## Creating An IAM Account With Password

I start of by *explain the how to*.

The benefit of creating a password for this account.

The risk of creating a password for this account.

Best Practice.

+ The admin setup with default a password and force password reset on the user at their initial login.
+ Secure password policy needs to be applied. This means the admin should care enough about enforcing password strength best practice.
  + 8-14 characters long
  + At least 1 uppercase
  + At least 1 lowercase
  + At least 1 number
  + At least 1 symbol
  + Decide whether to allow blank space or not

## Creating a Passwordless IAM Account

I start of by *explain the how to*.

The benefit of ***NOT*** creating a password for this account.

The cons of ***NOT*** creating a password for this account.

Best Practice.

---

**Author:** Sangsongthong C.
**Published Date:**
