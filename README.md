# AWS IAM Foundation Setup

This repository supports two parallel goals:

**Hexterika Cyberlab:** Present the deliverable templates and technical sample work supporting the AWS IAM Foundation Setup service.
**Cloud security portfolio:** Demonstrate hands-on AWS account security, IAM configuration, least-privilege reasoning, security verification, technical documentation, and client handover.

Prospective clients can review the documentation structure associated with the service. Recruiters and technical reviewers can examine the sample work as evidence of the underlying technical capability.

## Repository Structure

```text
AWS-IAM-Security/
├── README.md
├── LICENSE
├── deliverables/
│   ├── aws-iam-foundation-configuration-report-template-clientName.md
│   └── aws-iam-foundation-handover-guide-template-clientName.md
└── sample-work/
    └── 00-account-setup/
        ├── 00-account-setup.md
        └── images/
```

## Start Here: Sample Work

The sample-work/ directory contains hands-on work completed in authorized AWS lab environments.

The sample work documents technical implementation, security decisions, verification, and supporting evidence rather than presenting only finished templates.

The current sample begins here:

`00-account-setup/00-account-setup.md` — Documents the creation and initial preparation of a clean AWS account, including root-account protection and early security decisions.

This directory is the primary starting point for recruiters and technical reviewers. Prospective clients may also review it to see the technical work supporting the service.

Additional sample work can be added as the AWS IAM foundation lab develops.

## Deliverable Templates

The deliverables/ directory contains the public Markdown templates used to structure client documentation.

`aws-iam-foundation-configuration-report-template.md`

The AWS IAM Foundation Configuration Report template records the environment-specific IAM foundation configured and verified during an engagement.

It provides a structured place to document:

+ The resulting IAM access structure
+ Security configurations and verification results
+ Billing-alert confirmation
+ Relevant environment-specific notes
+ Confirmation that temporary access was removed

`aws-iam-foundation-handover-guide-template.md`

The `AWS IAM Foundation Handover Guide template` provides a plain-language reference for the client after delivery.

It explains the configured access structure and provides guidance for safely managing users and access after handover.

The templates are published in Markdown so their structure can be reviewed directly on GitHub. Completed client versions are customized for the client’s environment and converted to PDF before delivery.

## Client Deliverables

The client documentation package represented by this repository consists of:

+ `aws-iam-foundation-configuration-report-template-clientName.md`
+ `aws-iam-foundation-handover-guide-template-clientName.md`

The Configuration Report documents what was configured and verified.

The Handover Guide helps the client understand and safely maintain the IAM foundation after the engagement.

The public Markdown files show the reusable document structures. Completed client documents contain environment-specific information and are delivered privately.

## Repository Scope

The Hexterika Cyberlab website explains the client-facing AWS IAM Foundation Setup service. This repository provides the corresponding technical evidence, sample work, and deliverable structures.

The repository is limited to material supporting the AWS IAM Foundation Setup service. Any broader AWS service or later service tier should be documented separately.

---

## Third-Party Content

Sample work in this repository may include screenshots, interface elements, product names, trademarks, logos, or other materials belonging to Amazon Web Services or other third parties.

Such materials remain the property of their respective owners and are included solely for educational, technical-documentation, service-demonstration, and portfolio purposes.

The MIT License applies only to original content created for this repository and does not grant rights to third-party intellectual property.

## License

This repository is licensed under the MIT License. See [`LICENSE`](LICENSE) for the full license text.

Client-specific reports and confidential client information are not part of this public repository.

---

### Hexterika Cyberlab — operated by Sangsongthong Chantaranothai
