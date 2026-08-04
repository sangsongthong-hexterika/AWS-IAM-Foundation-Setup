# AWS IAM Foundation Setup

This repository supports two parallel goals:

+ **Hexterika Cyberlab:** Present the deliverable templates and technical sample work supporting the AWS IAM Foundation Setup service.
+ **Cloud security portfolio:** Demonstrate hands-on AWS account security, IAM configuration, least-privilege reasoning, security verification, technical documentation, and client handover.

Prospective clients can review the documentation structure associated with the service. Recruiters and technical reviewers can examine the sample work as evidence of the underlying technical capability.

## Repository Structure

```text
AWS-IAM-Security/
├── README.md
├── LICENSE
├── deliverables/
│   ├── aws-iam-foundation-configuration-report-template-ClientName.md
│   └── aws-iam-foundation-handover-guide-template-ClientName.md
└── sample-work/
    └── 00-account-setup/
        ├── 00-account-setup.md
        └── images/
```

## Start Here: Sample Work

The sample-work/ directory contains hands-on work completed in authorized AWS lab environments.

The sample work documents technical implementation, security decisions, verification, and supporting evidence rather than presenting only finished templates.

The current sample begins here:

`00-account-setup/00-account-setup.md` — Documents the initial account-owner preparation completed before temporary administrator access is provided, including AWS account creation, root-account protection, and early security decisions.

This directory is the primary starting point for recruiters and technical reviewers. Prospective clients may also review it to see the technical work supporting the service.

Additional sample work can be added as the AWS IAM foundation lab develops.

## Deliverable Templates

The `deliverables/` directory contains public Markdown templates showing the documents prepared for AWS IAM Foundation clients.

### `aws-iam-foundation-configuration-report-template-ClientName.md`

The **AWS IAM Foundation Configuration Report** explains what was configured in the client’s AWS account.

It may document:

+ IAM users, groups, and roles created
+ Permissions assigned
+ Security settings configured
+ Root MFA verification
+ Billing alerts configured
+ Security checks completed
+ Confirmation that temporary access was removed

In simple terms:

> “Here is what Hexterika Cyberlab configured and verified in your AWS account.”

### `aws-iam-foundation-handover-guide-template-ClientName.md`

The **AWS IAM Foundation Handover Guide** explains how the client can continue using the IAM foundation safely after the work is complete.

It may explain:

+ How to add a new user
+ Which group a user should be added to
+ How to remove a user who no longer needs access
+ How to check whether MFA is enabled
+ How to avoid unnecessary root-account use
+ How to handle access keys safely
+ What should not be changed without reviewing the permissions first

In simple terms:

> “Here is how to continue using the IAM foundation safely after handover.”

The templates are published in Markdown so they can be reviewed directly on GitHub. Completed client versions are customized for the client’s AWS environment and converted to PDF before delivery.

## Client Deliverables

The client documentation package represented by this repository consists of:

+ `AWS-IAM-Foundation-Configuration-Report-[ClientName].pdf`
+ `AWS-IAM-Foundation-Handover-Guide-[ClientName].pdf`

The **Configuration Report** explains what Hexterika Cyberlab configured and verified in the client’s AWS account.

The **Handover Guide** explains how the client can continue using the IAM foundation safely after delivery, including how to add or remove users within the established access model.

The guide is limited to the IAM foundation covered by this service. It is not a comprehensive AWS security manual.

The public Markdown files show the reusable document structures. Completed client documents contain environment-specific information, are converted to PDF, and are delivered privately.

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
