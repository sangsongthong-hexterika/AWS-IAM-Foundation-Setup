# 🔐 AWS IAM Setup Guide – [Client Name]

Welcome! This guide helps you understand and safely manage your AWS Identity and Access Management (IAM) setup.

---

## 1. AWS Account Summary

- **AWS Account ID:** `xxxxxxxxxxxx`
- **Account Purpose:** `[e.g., Dev/Test/Staging/Production]`
- **Setup Date:** `[YYYY-MM-DD]`
- **Total Users Created:** `[X]`

---

## 2. IAM Group Overview

Your users are organized into IAM groups, each with a predefined set of permissions.

| Group Name | Permissions | Description |
|------------|-------------|-------------|
| `Admins` | AdministratorAccess | Full access to AWS services |
| `Developers` | AmazonEC2FullAccess, CloudWatchReadOnlyAccess | Developer access to compute and monitoring |
| `Finance` | Billing | View billing and usage data |

> 📸 *Add screenshot of IAM Groups screen here*

---

## 3. IAM User List

Each team member has been added as an IAM user and placed into one or more groups.

| User | Group(s) | MFA Enabled | Notes |
|------|----------|-------------|-------|
| John | Developers | ✅ | – |
| Sarah | Finance | ✅ | – |

> 📸 *Add screenshot of IAM Users page here*

---

## 4. MFA and Security Settings

- **MFA Required for All Users:** ✅ Yes  
- **Password Policy:**  
  - Minimum Length: 12 characters  
  - Must Include: Uppercase, number, symbol  
  - Expiry: Every 90 days  
- **Root Account Access Keys:** ❌ Disabled  
- **User Access Keys:** `[enabled/disabled]`

> 📸 *Add screenshot of password policy page here*

---

## 5. How to Add a New User (Step-by-Step)

Follow these steps to add a new user safely:

1. Sign in to AWS Console and go to **IAM > Users > Add user**  
2. Enter the user’s name  
3. Select access type (Console, Programmatic, or Both)  
4. Assign the user to one or more existing IAM groups  
5. Check "Require password reset at next sign-in"  
6. Enable MFA after the user logs in for the first time  
7. Share credentials securely  

> 📸 *Insert screenshots that show each step of adding a new IAM user (from the IAM > Users > Add user flow in the AWS Console)*

---

## 6. Tips & Best Practices

- Assign users only to existing groups — avoid attaching policies directly to users  
- Review users quarterly and remove inactive accounts  
- Never use the root account for day-to-day tasks  
- Avoid sharing passwords or access keys

---

## 7. Support

If you need help updating access or creating new groups, contact the original setup provider or your AWS administrator.

---

## Legal Notice

This guide was prepared by Hexterika Cyberlab, a freelance service operated by Sangsongthong Chantaranothai.  
Business registration as an Individual Entrepreneur (IE) in Georgia is planned for November 2025.

The information provided is for operational use and does not represent formal security certification or regulatory compliance advice.
