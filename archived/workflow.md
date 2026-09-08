# Workflow

## 🔄 3. **Workflow Summary – IAM Foundation Setup Offer**

Here's a checklist-style **workflow summary** so you know what to do each time.

---

### 💼 **Before the Session (Prep)**

+ ✅ Confirm scope: one AWS account, under 10 users, no workloads
+ ✅ Ask client to:
  + Provide list of users, their roles, and services needed
  + Grant temporary admin access **or** book screen-share session
+ ✅ Schedule 1–2 hour block for the setup

---

### 🛠️ **During the Setup**

1. **Login or connect to AWS console**
2. **Create groups** based on roles (Dev, Billing, Admin, etc.)
3. **Attach AWS-managed policies** to groups (avoid inline or custom)
4. **Set up users**
    + Assign to correct group(s)
    + Enable MFA
    + Generate temporary password or key
5. **Configure account security settings**
    + Enforce MFA for all users
    + Password policy
    + Check access key settings
6. **Remove or secure root account access key if present**

---

### 📦 **After Setup**

+ ✅ Create the IAM layout guide (from template above)
+ ✅ Send guide + user credentials securely
+ ✅ Offer optional add-ons:
+ Custom policies ($10 per policy)
