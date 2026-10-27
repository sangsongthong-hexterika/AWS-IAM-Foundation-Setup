# Part 4 — Planned Phases

[Back to lab overview](./fictional-hospital.md)

This part records the hospital requirements and access rules for Phases 2 and 3. Nothing in this part is implemented or tested yet. The groups named here exist in IAM with no permissions attached.

## Phase 2 — Duplicate-Record Verification and Merge

### Record-Merge Decision

Real hospitals handle unidentified patients and duplicate-record merging according to their jurisdiction, staffing, and information systems. A New York State model policy registers unidentified patients under placeholder names such as "Male Unidentified I" and has the records updated once the patient is identified, with the Medical Records department notified when multiple patient record numbers need to be merged. ASPR TRACIE describes hospitals giving each unidentified patient a unique placeholder name so the patients can be told apart and duplicate records are avoided. Keeping reporting, approval, and execution in separate hands follows the separation-of-duties control in NIST SP 800-53 (AC-5).

These practices are references, not universal requirements. Because Hexterika Hospital is a fictional small hospital with approximately 10 workers, duplicate-record responsibilities are assigned to existing authorized roles instead of a dedicated Health Information Management department. Reporting, approval, and technical execution stay separated so that one person does not control the entire process.

The control chain:

Patient registration flags → selected verifier approves or rejects → selected IT merge operator invokes the merge function → the function merges and clears the flag, or clears the flag only → verifier confirms an executed merge

No individual holds both verification and merge-operation permissions.

### AWS Lambda Merge Function

One Lambda function handles the outcome of a merge decision.

It is required because the merge operator must move records without holding read access to record contents. The function holds the access; the operator holds only the permission to invoke it.

The function reads the verifier's decision and takes one of two branches. An approved decision merges the records and clears the `merge-review` tag. A rejected decision clears the tag only. Both branches close the verifier's read window automatically, and the operator cannot choose which one runs.

### Record-Review Flag

Suspected duplicates are marked with an S3 object tag, `merge-review=open`, applied to the administrative and clinical records of both reported profiles.

The tag is what opens the verifier's read access. Verifier permissions use the `s3:ExistingObjectTag/merge-review` condition, so membership in the verifier group grants no standing access to any record. Without a flag raised by patient registration, a verifier can open nothing.

The flag lives on the records themselves rather than only in `merge-requests/` because IAM evaluates conditions against the object being requested. There is no cross-object lookup at policy-evaluation time, so a request record sitting elsewhere in the bucket cannot gate access to a patient record.

Object tagging is a feature of S3, not an additional service, so the resource count does not change.

### hexterika-patient-registration (Phase 2 additions)

| Hospital task | Business justification |
| ------------- | ---------------------- |
| Flag a suspected duplicate | Mark two patient profiles as potentially belonging to the same person and submit them for independent verification. |
| Raise the review flag on both reported profiles by tagging their administrative and clinical records | Registration staff meet patients and maintain identity details, so they are the ones who notice that two records look like one person. Raising the flag is also what opens the verifier's read access, so a verifier can never open a patient's records on their own initiative. Registration can set only this one tag and cannot read clinical data, so raising a flag gives them nothing they did not already have. |
| Cannot merge records | The merge is reviewed by a person with permission to authorize it. |
| Cannot clear the review flag | Registration opens the window and nothing more. Closing it belongs to the merge function, which runs on both an approval and a rejection, so the window never stays open because someone forgot to close it by hand. |

### hexterika-duplicate-record-merge-operators

- Contains only selected IT personnel.
- Membership is in addition to their normal IT group membership.
- Not every IT worker receives merge authority.
- Operators act on an already-decided merge request through a controlled function.
- Operators cannot approve or reject merge requests.
- Operators do not receive routine direct access to patient-record contents because they operate the merge.

| Hospital task | Business justification |
| ------------- | ---------------------- |
| Read the verifier's decision on a merge request | The operator has to know which request was decided, by whom, and which two records it covers. They act on the written decision, not on someone walking over and asking. The decision also exists as a record before anything happens to the data. |
| Invoke the merge function for an approved or a rejected decision | The merge is technical work, so it goes to someone who understands what happens to the data. Routing it through a function means the operator never holds the underlying record access. The function reads the decision and either merges the records and clears the review flag, or clears the flag only, so the operator holds one permission and no choice over which branch runs. Rejections go through the same function so that closing the review window never depends on anyone remembering to do it. |
| Cannot approve or reject a merge, including one they will act on | If the same person can decide and act, the decision step is decoration. This is the control the whole merge chain exists to create. |
| Cannot invoke the function without a recorded verifier decision | Stops an operator from merging two records because someone asked them to as a favor, or because they believe the records match. Deciding that two records are the same patient is a clinical and administrative judgment, not a technical one. |
| No routine read access to patient-record contents | Operating the merge does not require reading a diagnosis, a prescription, or a lab result. Granting record access to the person who runs the tooling would give one technician a reason-free view of every patient in the hospital. |
| Cannot raise or clear the review flag directly | The flag is cleared by the function, not by a separate action the operator chooses to take. An operator who could clear flags by hand could also leave one open, or close one before a verifier had finished reading. |
| Membership is separate from the IT group | Merge authority is given to specific people, not to everyone who works in IT. Keeping it in its own group means it can be granted and removed without changing anyone's IT access. |

### hexterika-duplicate-record-verifiers

- Contains selected hospital personnel trusted to verify and approve merges.
- Membership is permission-based, not tied to a department or profession.
- A doctor may handle ordinary cases.
- Another suitable authorized person could handle exceptional cases involving legal, forensic, or administrative concerns.
- Verifiers approve or reject; they do not execute the merge.

A real hospital could choose the appropriate verifier case by case, but this lab needs a concrete setup. One fictional doctor is assigned as the ordinary verifier, while exceptional cases may require a different authorized verifier.

| Hospital task | Business justification |
| ------------- | ---------------------- |
| Read a reported duplicate-record request | The verifier has to see what was reported and why before deciding anything. The request carries the two record identifiers and the reason registration believes they describe one person. |
| Read the administrative records of both flagged profiles, only while the review flag is set | Deciding that two records are the same patient needs identity, demographic, and contact details, which live on the administrative side. This is wider access than the verifier's own profession normally gets, so it is not standing access: it opens when registration raises the flag and closes when the flag clears. |
| Read the clinical records of both flagged profiles, only while the review flag is set | Clinical history is the other half of the match. Two people can share a name and a birth year, and administrative details alone will produce a wrong merge. The group grants this half as well as the administrative half so that membership means the same access for every member, including a non-doctor verifier handling a legal or forensic case who holds no clinical read from anywhere else. |
| Approve or reject a merge request | The approval is the decision point. Registration reports what they noticed, but registration cannot see clinical data and so cannot confirm it. The verifier can see both and is accountable for the call. |
| Cannot execute the merge | Keeps the decision and the action in two different hands. A verifier who could also execute would only need to convince themselves. |
| Cannot create a merge request | The report comes from registration, who maintain the administrative records and encounter duplicates during their normal work. Separating the report from the approval means the verifier is always reviewing someone else's observation. |
| Cannot raise or clear the review flag | If a verifier could set the flag, they would be granting themselves the access the flag controls, and the prerequisite would mean nothing. The flag has to be raised by someone who gains nothing from raising it. |
| Read the record of an executed merge and confirm it matched the approval | Without this, approval and execution are never compared and nobody would notice a merge that ran against the wrong pair of records. The verifier is checking the operator's work, not their own, so this does not collapse the separation. |

### hexterika-dentists (Phase 2 addition)

| Hospital task | Business justification |
| ------------- | ---------------------- |
| Cannot approve a merge, including one based on their own dental finding | The verifier approves a merge by reviewing someone else's observation. A dentist who could also approve would be deciding on the strength of their own finding, which puts the evidence and the decision in one person's hands. |

### Phase 2 Limitations

The review flag has no automatic expiry. IAM date conditions compare the current time against a value written in the policy, not against a value stored on an object, and S3 Lifecycle removes objects rather than tags. Both decided outcomes close through the merge function, so the remaining case is a verifier who never records a decision at all, which leaves the flag open. This is deliberately left outside the AWS boundary and handled as a hospital working procedure. A scheduled sweeper to close stale flags would add application code that no permission test could demonstrate.

IAM evaluates allows as a union, so the flag condition constrains only the access a member does not already hold from another group. A verifier who is also a doctor keeps ungated clinical read from the doctors group, and the flag effectively gates their administrative access alone. For a verifier who holds no other clinical access, the flag gates both halves. This is the intended result, not a gap in the condition.

Registration and a verifier acting together can open a review window with no genuine duplicate behind it. Two-person collusion is accepted and recorded rather than controlled at this hospital size.

## Phase 3 — Hospital IT Technical Operations and Controlled Deletion

Phase 3 covers hospital IT responsibilities separately from account-wide administration: controlled object deletion in the isolated `system-testing/` prefix, S3 version-history inspection, and the behavior of delete markers versus permanent version deletion. Clinical-record deletion would need an agreed protocol, appropriate safeguards, and a separate decision on whether IT holds direct authority or uses a privileged process. Real healthcare systems can require record retention and controlled deletion; this lab does not claim to implement healthcare retention requirements.

### hexterika-hospital-it

This group operates the hospital technology inside the AWS environment the hospital was given. It does not create AWS resources, does not administer identities, and does not read patient-record contents.

Hospital IT has no dedicated `it/` prefix because its role is to operate and test the hospital's technical infrastructure, not to maintain a separate category of patient records. The `system-testing/` prefix provides an isolated area for authorized technical testing.

| Hospital task | Business justification |
| ------------- | ---------------------- |
| Operate the hospital's record storage without reading record contents | IT keeps storage available, configured, and recoverable. None of that requires reading a diagnosis or a lab result. Granting record access to keep storage working would hand one technician a view of every patient in the hospital with no clinical reason attached to it. |
| Use a separate testing area to verify configuration and access | IT needs somewhere to upload, retrieve, and delete objects to confirm that settings and permissions behave as intended. Doing that against live patient records risks damaging or exposing them, so `system-testing/` exists to make technical verification possible without touching real records. |
| Cannot create AWS resources or add new services | The hospital is a tenant in an AWS account it does not own. Resource creation belongs to the account owner. This also keeps the hospital's resource count a deliberate decision rather than something that grows whenever a technician decides something would be handy. |
| Cannot create, modify, or delete IAM users, groups, or policies | Identity administration belongs to the account owner. If IT could edit IAM, every separation of duties in this lab would be advisory only, because IT could write itself whatever permission it was missing. |
| Cannot add itself or anyone else to a group | Stated separately from the policy restriction because it is the specific failure this lab is built to prevent. An IT member who can add themselves to the verifier group breaks the merge control chain without ever touching a policy document. |
| Cannot approve a record merge | Approving a merge is a judgment about whether two records describe the same person. It needs the record contents IT deliberately does not have, and clinical and administrative context IT does not hold. |
| Supplies the merge operators, but merge authority is a separate group | Executing a merge is technical work, so operators come from IT. Not every IT worker gets it, so it lives in its own group rather than being attached to the IT group by default. |
| Cannot view or change account-wide security, logging, or billing settings | These belong to the account owner under Walkthrough 01. A tenant does not configure logging, findings, or cost controls for an account it does not own. |

### Hospital Security

The hospital does not have an internal security group.

Every AWS security surface in this lab — activity logging, findings, configuration review, and cost controls — is account-wide and belongs to `gexter-boss` under Walkthrough 01. A hospital group monitoring those would need account-wide read access, which duplicates the account owner and breaks least privilege, or it would receive nothing and exist as a group name with no access behind it.

For a hospital of approximately 10 workers, security assessment and audit are engagements rather than permanent staff duties. They are granted under temporary external-access groups.

The internal oversight duty that would otherwise sit with a security group — confirming that an executed merge matched its approval — is assigned to `hexterika-duplicate-record-verifiers`. That keeps the check internal and in different hands from the operator who performed the merge.

### Temporary External-Access Groups

Separate groups or roles are added according to engagement type. AWS administration, auditing, vulnerability assessment, and penetration testing are never combined into one permission set.

External personnel are not eligible for `hexterika-duplicate-record-merge-operators`. Executing a patient-record merge is a standing internal duty and is not granted through a temporary engagement.
