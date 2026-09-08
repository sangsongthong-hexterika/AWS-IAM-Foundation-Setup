# AWS IAM Lab Part 2

---

🌱 Activating IAM User Access to the Billing Console
✅ This is a one-time account-level toggle, NOT a policy.

👉 Only the root account can turn this setting on or off:

Enable IAM Access to the Billing Console

Once this setting is enabled by root:
✅ Now IAM policies can control who gets billing permissions (read or write).
✅ AdministratorAccess allows managing billing permissions.

✅ Before this setting is enabled, no IAM user—no matter what policy or group—is able to access billing info.

🟢 To your question about creating a special group:
Can the root create a group that activates IAM users to see billing?

Clarification:

The activation itself is not done via a group or policy—it is a toggle in the account settings:

Root must log in.

Go to My Account → IAM User and Role Access to Billing Information.

Check the box to enable it.

After that toggle is ON, IAM users can access billing if their policies allow.

✅ So:

Groups and policies control who can see billing after it’s enabled.

*Only root can do the enabling.

🟢 Example process:
1️⃣ Root enables IAM access to billing console.
2️⃣ Root (or any admin IAM user) creates a group (e.g., BillingReadOnly).
3️⃣ Attach the AWS managed policy job-function/Billing (read-only billing access) to that group.
4️⃣ Add users to the group.

🟢 About managing membership in this group:
Who can add users to this group—root only, or any admin?

✅ Once the toggle is ON:

Any IAM user with AdministratorAccess can:

Create the group.

Attach billing policies.

Add or remove users from the group.
✅ There is no restriction that only root can add users.

✅ Short version:

Root must flip the billing access switch.

After it’s on, groups/policies work as normal.

Admins can manage who is in those groups and attach billing policies.

It does not depend on JSON policy for the initial toggle—it’s account configuration.

🟢 How IAM Billing Access Actually Works
✅ Step 1: Root-only toggle

Root logs in.

Goes to Account Settings (My Account).

Finds this option:

“Activate IAM Access to the Billing Console.”

Toggles this ON.
🔒 Only root can do this step.

✅ This toggle does not grant access itself.
It simply tells AWS:

“OK, IAM policies are now allowed to control billing access.”

Until this is ON:
❌ No IAM user can see billing info, even if they have AdministratorAccess.

✅ Step 2: Policies and groups (done by root or any IAM admin)

Now that the toggle is ON, you can create policies to grant billing permissions.

You can use:

AWS managed policy:

arn:aws:iam::aws:policy/job-function/Billing (Read-only billing access)

Or a custom policy (e.g., read & write billing permissions).

Attach these policies to:

A group (e.g., BillingReadOnlyGroup).

Individual IAM users.

✅ Any IAM user with AdministratorAccess can:

Create these groups and policies.

Attach the policies to users.

Add or remove users from the groups.

🟢 Summary of Permissions
✅ Root:

Turns the toggle ON or OFF.

✅ Any IAM user with AdministratorAccess:

Creates groups/policies that allow read or write billing access.

Adds/removes users to those groups (once the toggle is enabled).

🟢 Example Timeline
1️⃣ Root enables “Activate IAM access to billing.”
2️⃣ Root or Admin creates a group called BillingReadOnlyGroup.
3️⃣ Admin attaches Billing policy to that group.
4️⃣ Admin adds gexter1 and gexter2 to the group.
✅ Now gexter1 and gexter2 can log in and see billing info (but only read-only).

✅ So your statement is correct:

The IAM accounts must have the toggle enabled first (account-level switch), and then their permissions depend on policies/groups.

---

## Let's test this out on AWS lab

### Special groups & policies that needs to be created by the root account

This group is called the AdministratorAccess group.

Q: Why is this group so special?
A: It is because technically any IAM account with AdministratorAccess policy can create this group, but the best practice is to create a policy and attach it to a group. This means if the group is created by the root account, then, there is no need to re-create this group again even if in theory the IAM user with AdministratorAccess are trusted by the root account, therefore, have the rights to create any group or policy including creating another AdministratorAccess group. By following this best practice, you can avoid getting confuse and able to manage other IAM users with AdministratorAccess in a more organized and more structured way than manually attached the policy to each one of them. More on this in part 2.

---

**Author:** Sangsongthong C.
**Publish Date:**
