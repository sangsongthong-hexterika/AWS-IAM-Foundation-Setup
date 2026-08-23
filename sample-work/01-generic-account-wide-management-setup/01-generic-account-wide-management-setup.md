# Generic Account Wide IAM Management Policies Setup

This is the setup of account wide IAM management. This will contains the information that I setup to manage my AWS account unrelated to the labs. This will not be removed nor changes per labs like in 02-06.

## Groups

While I am currently the sole operator of my AWS account, the best practice is to attach the permissions to the role-based groups so once I need to expand my account to have more than one person to manage it, it can expand easier. Moreover, if I attach 1 permission at a time to one IAM user directly, let's say I have added 100+ permissions, it will be more difficult to manage the permissions. Adding the permissions according to their roles makes organizing the permissions easier in long term.

### hexterika-admins

This is the administrator group that contains the functional administrator level permission to manage all resources in this IAM account as well as grant access to the console.

### hexterika-finance

This is the finance group that contains the functional permission for me to manage my own AWS account and make sure I can control my account budget properly.

## Note to self

Attach screenshots and remove this section.
