# AWS-SCS-C01

## Domain 1

### Security Basics

**CIA**

* __Confidentiality__: IAM, MFA, bucket policies, security groups, ACL's within VPC's, KMS encryption etc.
* __Integrity__: Certificate Manager (SSL), IAM, bucket policies, S3 version control, MFA for S3 deletion etc.
* __Availability__: Autoscaling, Multi-AZs, Route53 health checks etc.

**AAA**

* __Authentication__: auth into IAM entity (user/role)
* __Authorization__: IAM policies to define access
* __Accounting__: audit trail i.e. CloudTrail

**Non-repudiation**

* Not being able to deny something has happened.
* CloudTrail, CloudWatch.


### How does AWS Secure their Stuff?

Physical and Environmental Security
* AWS consist of regions, with 2+ availability zones, each made up of multiple data centres.
* Secured by fire-detection/suppression, power (2 feeds / different power sources), climate and temperature, management (ex-soldiers / physical access in-out), storage device decommissioning (zero out all data from disks and then shredding/smashing disk).

Business Continuity Management
* Monitor availability, incident response
* Perform company-wide executive reviews when an incident has occurred + communicating issues out to customers

Network Security
* Secure network architecture
* Secure access points - everything is available as a public API, so access points but be secured.
* Transmission protection e.g. TLS security on console login, S3 bucket access etc.
* Amazon corporate segregation - Amazon.com network is completely different network to AWS. Bastion is required for employee access from Amazon.com -> AWS
* Fault-tolerant design - multiple AZ's in multiple regions
* Network monitoring and protection - DDoS mitigation via. AWS shield + advanced DDoS mitigation is available (more costly)

AWS Access
* Account Review and Audit - AWS users' accounts are audited/reviewed every 90days. If account has not been used, it will be revoked and reapplying for access is required.
* Background Checks
* Credentials Policy - Amazon's password policies (very complex, changed every 90 days).

Secure Design Principles
* Formal design reviews by AWS security team, threat modelling, completion of risk assessments, static code analysis tools run as part of build process, all deployed software undergoes re-occurring penetration testing prepared by carefully selected industry experts.

Change Management
* AWS performs routine emergency and configuration changes to AWS infra.
* It is all authorized, tested, logged, approved and documented in accordance with industry norms for similar systems.
* AWS communicates to customers via. email / service health dashboard.

Why should we trust AWS?
* AWS meets a whole bunch of compliance programs / IT security standards.
* Big ones are ISO27001, PCIDSS compliant, HIPAA (medical records).
* Your own software/infrastructure requires a GAP-AUDIT.

Exam Tips
* Remember different security controls around: physical and environmental security, business continuity, network security, AWS access, secure design principles, change management.
* Remember that the corporate Amazon.com network is completely segregated from the AWS network. Permissions / reviews are required when an employee wants to access AWS. Permissions are revoked as soon as nologin for 90 days.


### Shared Responsibility Model

What is it?
* Security WITHIN the cloud is the responsibility of the customer.
* E.g. House example:
    * Landlord is responsible for installing fire alarms, fences.
    * You are responsible for locking your door, making sure windows are shut etc.

AWS Security Responsibilities
* Global infrastructure - their data centres
* Hardware, software, networking and facilities - all their hardware, software such as RDS / AWS operation systems etc.
* Managed services - S3, DynamoDB etc.

Customer Security Responsibilities
* Infrastructure as a Service
* Updates and security patches e.g. EC2 instances etc.
* Configuration of AWS-provided firewall - VPC rules, security groups, network ACLs etc.

Diagram of the Shared Responsibility Model:
https://aws.amazon.com/compliance/shared-responsibility-model/

Basically, if the customer has no access to the underlying OS/software/infrastructure, then it is AWS's responsibility.


### Shared Responsibility - AWS service types

Infrastructure services - compute services such as EC2, EBS, Auto Scaling, VPC
* You can architect and build cloud infrastructure, control the OS, configure and operate any identity management system that provides access to the user layer of the virtualization stack.
* EC2 - AMIs, OS, applications, data in transit, data at rest, data stores, credentials, policies and configuration.

Container services - services such as RDS, Elastic Map Reduce (EMR) and Elastic Beanstalk.
* RDS example - you have a DB that you can install/access but you don't manage the underlying OS. AWS is responsible for patching for the RDS instance.
* Services that are typically run on separate EC2s or other infrastructure instances. Sometimes you don't manage the OS or platform layer.
* Customer is responsible for setting up and managing network controls such as firewall rules, managing platform-level identity and access management separately from IAM.

Abstracted services - services such as S3, Glacier, DynamoDB, SQS, SES.
* Services that abstract the platform or management layer on which you can build and operate cloud applications.
* Customer can access the endpoints of these abstracted services using AWS APIs.
* AWS is responsible for managing the underlying service components or OS in which these services reside.

Exam Tips: Have a STRONG understanding of the shared responsibility model.
* The model changes for the three different service types:
1. Infrastructure services (EC2, EBS, VPC)
2. Container services (RDS, EMR, Elastic Beanstalk) - AWS responsible for the OS, container itself.
3. Abstracted services (S3, Glacier, DynamoDB, SQS, SES) - AWS responsible for almost everything, except for the application layer e.g. TLS / access controls.


### Security IN AWS

Controls that you need:
* __Visibility__: AWS Config - managed and custom rules
* __Auditability__: AWS CloudTrail - records every API call in the environment
* __Controllability__:
    * AWS KMS - multi-tenant. Underlying hardware is shared, but strict controls.
    * AWS CloudHSM (hardware security module) - dedicated. Underlying hardware is NOT shared. __Exam: Which service is required for FIPS 140-2 Compliance? - CloudHSM as KMS being multi-tenant/shared does not comply.__
* __Agility__:
    * AWS CloudFormation - deploy templates to any regions
    * AWS Elastic Beanstalk - AWS provision resources for you, rather than you doing it each service manually
* __Automation__:
    * AWS OpsWorks - operate alongside CF / EB
    * AWS CodeDeploy - operate alongside CF / EB
* __Scale__: Every customer gets the same AWS security foundations, from a startup to a Fortune 500 company.

Other services applying to all controls
* AWS IAM - creating users, password policies, MFA, groups
* AWS CloudWatch - monitor environment, see breaches, CPU runtime
* AWS Trusted Advisor - advises on security, budgeting, system performance and reliability


## Domain 2

### IAM, S3 & Security Policies

IAM provides:
* Centralised control of your AWS account
* Shared access to your AWS account
* Granular permissions
* Identity Federation (Active Directory, Facebook, Linkedin etc.)
* MFA
* Provide temporary access for users/devices and services where necessary.
* Allows you to set up your own password rotation policy
* Integrates with many AWS services
* Supports PCI DSS Compliance

Critical Terms:
* Users - end users
* Groups - a collection of users under one set of permissions (apply one policy to group, users inherit policy)
* Roles - assign roles to AWS resources (e.g. EC2, Lambdas)
* Policies - document that defines permissions

__IAM is global__ - users, groups, roles, policies are done on a global level, not region-specific.

IAM Permissions Boundary for IAM Entities (users/roles)
* A Permissions Boundary is using a managed policy to set the _maximum permissions_ that an identity-based policy can grant to an IAM entity.
* https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_boundaries.html
* https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_iam-condition-keys.html#ck_PermissionsBoundary


### IAM Root User Scenario

_Scenario: You have have started as a sysadmin at a cloud-based company. Previous admin used only the root-user._

First thing to do = rotate everything
* Change password
* De-activate then re-activate MFA
* Delete Access Key ID / Secret Access Key (don't create new access keys via. root user)
* Verify and delete IAM users that are not-legitimate.

### IAM Policies

**Policy contains PARC**

  - **Principal**
    - WHO/WHAT is Allowed/Denied access, indicated by ARN.
    - If IAM Policy, this is implicit based on what you attach it too
    - Can be Everyone (AWS:*.*), Specific account/s, IAM user, Federated user, Specific role, Specific service
  - **Action**
    - Action or NotAction (everything but)
    - ec2:StartInstances
    - iam:ChangePassword
    - s3:getObject
    - sqs:SendMessage, sqs:ReceiveMessage
    - iam:*AccessKey*
  - **Resource**
    - Object(s) being requested
    - Resource or NotResource (everything but)
    - Resource: arn:aws:s3:::mybucket/*
    - NotResource: arn:aws:s3:::securityloggingbucket/*
    - Resource: aws:sqs:us-west-2:12312312312312:queue1
    - Resource: arn:aws:dynamodb:us-west-2:1231232:table/books_table
    - Resource: arn:aws:ec2:us-east-1:1:12312312312312:instance/*
  - **Condition**
    - DateGreaterThan : aws:CurrentTime : "2018-12-25T00:00:00Z"
    - DateLessThan: aws:CurrentTime : "2018-12-26T08:00:00Z"
    - IPAddress: aws:SourceIP : [192.0.2.0/24, 10.0.0.0/8]
    - The above is AND, the OR is in the values (e.g. see IP address)
    - All conditions must be true for condition to be met

IAM policies specify what you are allowed to do with any AWS resource. You attach IAM policies to users, groups or roles.

- Policy types (all use PARC model)
  - **Service Control Policies (SCPs)** -> AWS Organizations / OU
    - Use case: Disable access to services
  - **Inline policies &  Managed policies** -> IAM
    - Use case: Set granular permissions based on functions that users or application need to perform
  - **Scoped-down policies** -> AWS Security Token Service (STS)
    - Use case: Reduce general shared permissions further
  - **Resource-based policies** (e.g. S3 bucket policies / Queues/ Vaults) -> AWS Service
    - Use case: Control access at the resource
    - Use case: Cross-Account access
 - How these policies work together....
   - **Same AWS account:**
   **SCP** -> Union(Intersect(Union(IAM Managed Policy, IAM Inline policy), Scoped-Down policy), Resource-based policies)
   - Cross account:
   **SCP** -> Intersect(Intersect(Union(IAM Managed Policy, IAM Inline policy), Scoped-Down policy), Resource-based policies)
 - Cross Account access:
    - To establish cross-account access, in the trusting account (Account A), you create an IAM policy that grants the trusted account (Account B) access to specificresources. Account B can then delegate this access to its IAM users. Account B cannot delegate more access to its IAM users than the permissions that it has been granted by Account A.


### S3 Bucket Policies

S3 bucket policies specify what actions are allowed or denied on the bucket.
* They are attached only to S3 buckets.
* They are BUCKET-LEVEL only (not bucket object-level).

Why use S3 policy instead of IAM policy
* You want to grant cross-account access to your S3 environment, without using IAM roles.
* Your IAM policies reach the size limit (2kb for users, 5kb for groups, 10kb for roles). S3 supports bucket policies of up to 20kb.
* You prefer to keep access control policies in the S3 environment.

__Management of individual S3 buckets__ = best use case of S3 bucket policies.
* Having a deny policy for a specific bucket is easier than creating an IAM policy that denies access to a specific bucket, then rolling that out to every user in your organisation.
* Example scenario: bucket could contain everyone's performance reviews in it.

Use the __AWS Policy Generator__ to generate a S3 bucket policy.

In any AWS policy (IAM, S3, Key), a __DENY will always override an ALLOW__.


### S3 Object Access Control Lists (ACLs)

S3 ACLs are a legacy access control mechanism. AWS recommends sticking to IAM policies and S3 bucket policies.
However, S3 ACLs can be applied to individual objects/files as opposed to S3 bucket policies.

S3 ACL use cases:
* If you need __fine grained permissions__ on individual files/objects.
* Reaching __size limit of 20kb__ for S3 bucket policies.

Managing S3 object permissions
* Click on object itself -> permissions
* Applying S3 object policies to individual IAM users - possible but can only be done via. CLI or AWS API (not console).
* Add S3 object access for other AWS Accounts by adding Account ID.

__Conflict policy example__: IAM user policy denying all S3 read vs. S3 bucket with object open to the public.
* Even though an explicit DENY overrides all ALLOW policies... the user would still be able to access the object. WHY??? =>
* The user CAN access objects in the public bucket via. the public bucket link (as an anonymous user).
* The user CANNOT access objects in the public bucket via. opening the object within AWS console/CLI/API (as an AWS user).

EXAM: Best exam practise is by creating your own S3 Bucket Policies, S3 Object ACLs, IAM User Policies etc.

### Policy Conflicts (EXAM ESSENTIAL TOPIC)

What happens if an IAM policy conflicts with an S3 policy which conflicts with an S3 ACL?

Whenever an AWS principal (user, group or role) issues a request to S3, the authorization decision depends on the union of all the IAM policies, S3 bucket policies and S3 ACLs that apply.

Least-privilege:
* Decisions ALWAYS default to DENY.
* An explicit DENY ALWAYS trumps an ALLOW.
* So if you DENY access to something somewhere and then something else allows access the DENY will override the ALLOW.

ACCESS DENIED EXAMPLES:
* IAM policy grants access to an object + S3 bucket policy denies access to object + no S3 ACL exists.
* No method specifies an ALLOW, request is denied by default.

ACCESS ALLOWED EXAMPLE:
* No method specifies a DENY + one or more methods specify an ALLOW.

__Policy Conflict flow__:
1. Decision starts at DENY by default.
2. Any applicable policies? ( YES = CONTINUE | NO = DENY )
3. Does a policy have an EXPLICIT DENY? ( YES = DENY | NO = CONTINUE )
4. Does a policy have an ALLOW? ( YES = ALLOW | NO = DENY)

This flow will be examined heavily with scenarios containing 2-3 different policies.


### Forcing Encryption on S3

Use S3 bucket policy to enforce encryption - prevent read without SSL enabled:
```javascript
// If secure transport is false, DENY read.
// Alternative policy, if secure transport is true, ALLOW read.
"Sid":"PublicReadGetObject",
"Effect":"Deny",
"Principal":{
    "AWS":"*"
}
"Action":"s3:GetObject",
"Resource":"arn:aws:s3:::bucketname/*",
"Condition":{
    "Bool":{
        "aws:SecureTransport":false
    }
}
```

### Cross-Region Replication

Cross-region replication replicates objects from one region to another. By default, this is done using SSL. You don't need to enable encryption.

You can replicate objects from a source bucket to only one destination bucket (1-1 relationship).
After S3 replicates an object, the object can't be replicated again.

Cross-Region Replication (CRR) requirements:
* Src/dest buckets must have __versioning enabled__.
* Src/dest buckets must be in __different AWS regions__.
* Amazon S3 must have permissions to replicate objects from src/dest bucket on your behalf. When you enable CRR for the first time, a __role will be created__ for you + a __customer-managed policy__ will be assigned.
* If src bucket owner also owns the object, the bucket owner has full permissions to replicate the object. If not, object owner must grant the bucket owner `READ`/`READ_ACP` permissions via. the object ACL.

CRR + Cross Accounts:
* The IAM role must have permissions to replicate objects in the destination bucket.
* In CRR config, you can optionally direct AWS S3 to change ownership of object replicas to the AWS account that owns the destination bucket.
* __AUDIT account Cross-Region Replication use case__:
1. CloudTrail logs AWS accounts XYZ.
2. Turn on CRR to replicate logs to AUDIT account.
3. AWS accounts XYZ can only replicate logs, but not read/write logs in AUDIT account.

What is replicated?
* New objects created after you add a replication config.
* S3 replicates objects encrypted using S3 managed keys (SSE-S3) or KMS managed keys (SSE-KMS) + unencrypted objects.
* Object metadata
* Object ACL updates
* Object tags
* S3 replicates only objects in the src bucket for which the bucket owner has permissions to read objects and read access control lists.

DELETE marker replication
* Delete markers on an object are replicated. Deletion of versions of objects are NOT replicated.
* A delete marker only hides an object via. versioning, not actually delete it.

What is NOT replicated
* Anything created __BEFORE CRR is turned on__.
* Objects created with SSE using __customer-provided (SSE-C)__ encryption keys.
* Objects created with SSE using __AWS KMS-managed encryption (SSE-KMS)__ keys, unless you explicitly enable this option.
* Objects in the src bucket for which the __bucket owner does NOT have permissions__ (happens when the obj owner is different from the bucket owner).
* Delete replication of a particular VERSION of an object. Source bucket deletion != dest bucket deletion. This is to stop malicious deletion of a specific version on an object.

Resources:
* Cross-Region Replication: https://docs.aws.amazon.com/AmazonS3/latest/dev/replication.html
* What does S3 replicate: https://docs.aws.amazon.com/AmazonS3/latest/dev/replication-what-is-isnot-replicated.html


### Securing S3 Using CloudFront

Force users to only access S3 via. CloudFront instead of direct access via. S3 URL.

Steps to create a new CF distribution:
1. CloudFront service
2. Create a new distribution -> Web Distribution
3. Origin Domain Name: the source S3 bucket URL
4. Restrict Bucket Access -> NO (exam will test how to restrict AFTER a distribution has already been created)
5. Everything as default

Steps to secure S3 bucket via. CF:
1. Goto CloudFront -> __Origins and Origin Groups__
2. Turn on __Restrict Bucket Access__ -> Create an __Origin Access Identity__
3. Turn on __Grant Read Permissions on Bucket__ to allow CloudFront OAI to perform `s3:GetObject`

S3 bucket policy to restrict access via. CloudFront:
```javascript
{
	"Sid": "1",
	"Effect": "Allow",
	"Principal": {
		"AWS": "arn:aws:iam::cloudfront:user/CloudFront Origin Access Identity EAF5XXXXXXXXX"
		},
	"Action": "s3:GetObject",
	"Resource": "arn:aws:s3:::AWSDOC-EXAMPLE-BUCKET/*"
}
```

### Using SSL Certificates using CloudFront

DEFAULT SSL CERTIFICATE: If you are happy for users to access your content using *.cloudfront.net domain name.

CUSTOM SSL CERTIFICATE: If you want to use a domain name that you own example.com.

You must store your custom SSL Certificate using:
* IAM API
* AWS Certificate Manager (ACM)
* Only in the `us-east-1` region = US East (N. Virginia)

### Secure S3 Using Pre-Signed URLs

Another method of accessing objects inside S3 - done via. SDKs (Python, Java, Go) or CLI.

```bash
$ aws s3 mb s3://acloudgurupresigned                # Make bucket
$ echo "Hello Cloud Gurus" > hello.txt
$ aws s3 cp hello.txt s3://acloudgurupresigned      # Upload object to bucket
$ aws s3 ls s3://acloudgurupresigned                # Check object is in bucket
$ aws s3 presign s3://acloudgurupresigned/hello.txt --expires-in 300 # presign URL with 300 sec expiration (default expiry = 1 hr)
https://acloudgurupresigned.s3.amazonaws.com/hello.txt?AWSACcessKeyId=XXX&Expires=XXX&x-amz-security-token=XXX&Signature=XXX
```

### Security Token Service (STS) (IMPORTANT EXAM TOPIC)

STS grants users limited and temporary access to AWS resources.

These users can come from:
* Federation (typically Active Directory)
  * Uses SAML
  * Grants temp access based off user's AD credentials. Does not need to be a user in IAM.
  * SSO allows users to log into AWS console without assigning IAM credentials.
* Federation with Mobile Apps
  * Facebook / Amazon / Google or other OpenID providers.
* Cross Account Access
  * Lets users from one AWS account access resources in another.

Key Terms:
* Federation - combining or joining a list of users in one domain (such as IAM) with a list of users in another domain (such as AD, Facebook etc.)
* Identity Broker -  service that allows you to take an identity from point A and join it (federate it) to point B.
* Identity Store - services like AD, Facebook, Google etc.
* Identities - a user of a service like Facebook etc.

An Identity is a user, that is stored in an Identity Store (like Active Directory/Facebook). You create an Identity broker that allows you take those Identities in your Identity Store and join them up to IAM -> This is essentially the federation/joining of IAM with AD/Facebook. The service that allows this is the Security Token Service.

Scenario: _You are hosting a company website on EC2 web servers in your VPC. Users of the site must login to the site, which authenticates against the company's AD servers which are based on-site at the company HQ. Your VPC is connected to the company HQ via. a secure IPSEC VPN. Once logged in, the user can only have access to their own S3 bucket._

How to set this up:
1. Develop an Identity Broker (join AD -> IAM).
2. Identity Broker will authenticate (using client/appid, secret) against AD:
    * Authenticate to obtain an AD token.
    * Pass AD token to STS.
    * STS will provide us with another token.
3. Pass STS to the web application to authenticate against S3.
4. S3 uses IAM to check if user has access to S3.
5. User is able to access S3.

Scenario:
1. Employee enters username / password
2. Application calls Identity Broker. Broker captures username/password.
3. Identity Broker uses the organisation's LDAP directory to validate the employee's identity.
4. Identity Broker calls the `sts:GetFederationToken` API using IAM credentials.
    * GetFederationToken(DurationSeconds, Name, Policy, PolicyArn) where:
    * _DurationSeconds_: duration of the STS token (1 to 36 hours).
    * _Name_: name of the federated user.
    * _Policy_: inline IAM policy.
    * _PolicyArn_: ARN referencing an IAM policy.
5. STS confirms that the policy of the IAM user making the call to GetFederationToken gives permission to create new tokens.
6. STS returns the temp STS token to the Identity Broker.
7. Identity Broker returns the STS token to the application.
8. Application uses the STS token to make requests to S3.
9. S3 uses IAM to verify STS token and to allow requested operation on the given S3 bucket.
10. IAM provides S3 with go-ahead to perform requested operation.

High-Level Summary:
1. Authenticate (as Identity/User) against 3rd-party (Identity Store: AD/Facebook/Google).
2. Authenticate (as Identity Broker) against STS.
3. Authenticate (as Application) against AWS service to obtain access to resource.

## Web Identity Federation / Amazon Cognito

Web Identity Federation lets you give users access to AWS resources after they have successfully authenticated with a web-based identity provider like Amazon/Facebook/Google. User trades authentication code from Web ID provider for an AWS STS token.

Suggested use case: mobile app which you want to make available to Facebook users. (recommended for social accounts)

Amazon Cognito
* Sign-up / Sign-in to your apps
* Provides guest access
* Acts as identity broker between your app / Web ID provider
* Synchronises user data across multiple devices (mobile, desktop data sync)
* Recommended for mobile apps running on AWS.

Amazon Cognito scenario:
* Mobile shopping app: S3 for product data, DynamoDB for customer data.
* User logs into Facebook, Facebook provides web token.
* Cognito takes web token and exchanges it for STS token.
* Cognito passes STS token to mobile app.
* Mobile app uses STS token to get access to resources for user.

Amazon Cognito benefits:
* No need for mobile app to embed or store AWS credentials locally on the device = increased security.
* Provides users a seamless experience across all devices.

Cognito User Pools: user directories used to manage sign-up and sign-in functionality for mobile/web apps.
* User sign-in directly via. User Pool or indirectly via. identity provider (Amazon/Facebook/Google)
* Cognito acts as identity broker between ID provider and AWS.

## Glacier Vault Lock

Glacier is a low-cost storage service for data archiving and long-term backup.
* _Archives_: a single file or multiple files stored in a .tar or .zip.
* _Vault_: containers which store one or more Archives
* _Vault Lock Policy_: similar to an IAM policy to configure and enforce compliance controls - configure write-once-read-many archives / create data retention policies

Example Vault Lock Policy: Enforce archive retention for 1 year (deny archive delete for all archives <365 days old)
```javascript
"Version":"2012-10-17",
"Statement":[
    {
        "Sid":"deny-based-on-archive-age",
        "Principal":"*",
        "Effect":"Deny",
        "Action":"glacier:DeleteArchive",
        "Resource":[
            "arn:aws:glacier:us-west-2:XXXaccountidXXX:vaults/examplevault"
        ],
        "Condition":{
            "NumericLessThan":{
                "glacier:ArchiveAgeInDays":"365",
            }
        }
    }
]
```

Steps to configuring Vault Locks:
* Create Vault Lock policy.
* Initiate lock by attaching Vault Lock policy to your vault = in-progress state.
* You have 24 hours to validate the lock policy. You can abort within 24 hours.
* Once validated, Vault Lock policies are immutable.

Vault Lock Policy vs. Vault Access Policy:
* https://docs.aws.amazon.com/amazonglacier/latest/dev/vault-lock.html

## AWS Organisations

AWS Organisations is an account management service that lets you consolidate multiple AWS accounts into an organisation so that you can consolidate billing, group your AWS accounts into logical groupings for access control and attach Service Control Policies.

SCPs enable you to restrict, at the account level of granularity, what services and actions the users, groups, and roles in those accounts can do. However, an SCP never grants permissions. The SCP limits permissions for entities in member accounts, including each AWS account root user. SCPs are available only in an AWS organization that has all features enabled, SCPs aren't available if your organization has enabled only the consolidated billing features.

https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scp.html


## IAM Credential Report

IAM Credential Report is a CSV-formatted report which lists all users in the accounts + status of their various credentials, including
* Passwords: enabled, last used, last rotated, next rotation.
* Access Keys: similar to above + last used.
* MFA devices: similar to above.

CLI commands for IAM Credential Reports
```bash
# generate a credential report
aws iam generate-credential-report
# download a credential report / same API call but base64 decode
aws iam get-credential-report
aws iam get-credential-report --output text --query Content | base64 -D
```

Required permissions to generate IAM Credential Reports
* `GenerateCredentialReport`: create report
* `GetCredentialReport`: download report

An IAM Policy with permissions to generate IAM Credential Reports
```javascript
{
    "Version": "2012-10-17",
    "Statement": [{
        "Sid": "VisualEditor0",
        "Effect": "Allow",
        "Action": [
            "iam:GenerateCredentialReport"
        ],
        "Resource": "*"
    }]
}
```


## Summary / Exam Tips

Resetting Root Users
* Create new root user password / strong password policy.
* Delete 2FA / re-create.
* Delete Access Key ID / Secret Access Key.
* Check existing user accounts, delete if not legit.

IAM policies
* IAM is Global.
* Three different types: (1) Managed Policies (2) Customer Managed Policies (3) Inline Policies

S3 policies
* S3 policies are attached only to S3 buckets (NOT objects). They specify what is ALLOWED/DENIED on the bucket.
* Broken down to the user-level.
* _EXPLICIT DENY ALWAYS OVERRIDES AN ALLOW_.
* S3 ACL's: Legacy access control for enforcing access to S3 OBJECTS.
* S3 policy conflicts: see _policy conflict diagram_ above (IMPORTANT).
* aws:SecureTransport: restrict S3 bucket access to only HTTPS.
* Cross-Region-Replication (CRR):
    * Delete markers are replicated, deleted versions of files are NOT replicated.
    * Versioning must be enabled.
    * Possible to use CRR from one AWS account to another
    * SSL is enabled by default when you configure CRR
    * IAM role must have permissions to replicate objects in destination bucket.
    * Scenario: replicate CloudTrail logs to separate AWS audit account (can only send data there, not read/write).

Pre-signed URLs (CLI/SDK only):
* Access objects using pre-signed URL's
* Exist only for a certain length of time.
* Change TTL by using `expires-in`

STS / Identity Provider
* User provides credentials to Identity Provider (AD/FB/Google) -> AWS STS -> User accesses AWS resource -> AWS resource checks IAM -> access is provided to user.
