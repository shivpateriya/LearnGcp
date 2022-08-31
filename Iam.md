# Configuring IAM Permissions with gcloud
 **Lab Link** https://www.cloudskillsboost.google/focuses/7678?parent=catalog

 ## Starting Environment 
 You start with two user accounts and two projects; user1 is the "owner" of both projects and user2 is the "viewer" of only the first project. There is a Linux virtual machine (vm) running in the first project.

![IMAGE ](https://cdn.qwiklabs.com/YehpjydLBb6Xc1XP%2BLw9OpDXMkVQwkQYUqV27Vkt61w%3D)

# What is IAM?
<p>Google Cloud offers Cloud Identity and Access Management (IAM), which lets you manage access control by defining who (identity) has what access (role) for which resource.</p>

With Cloud IAM you can grant granular access to specific Google Cloud resources and prevent unwanted access to other resources. Cloud IAM lets you adopt the security principle of least privilege, so you grant only the necessary access to your resources.

![iam](https://cdn.qwiklabs.com/SUcA5Cwo3NVw0UXmnxQ01ujmrWeTYID2C4EpNp8GmcI%3D)


## Identities
In Cloud IAM, you grant access to members. Members can be of the following types:

- Google account
- Service account
- Google group
- G Suite domain
- Cloud Identity domain

# Roles

A role is a collection of permissions. You cannot assign a permission to the user directly; instead you grant them a role. When you grant a role to a user, you grant them all the permissions that the role contains.

<b>There are three kinds of roles in Cloud IAM:</b>

- **Primitive roles**: The roles historically available in the Cloud Console will continue to work. These are the Owner, Editor, and Viewer roles.

- **Predefined roles**: Predefined roles are the Cloud IAM roles that give finer-grained access control than the primitive roles. For example, the predefined role Pub/Sub Publisher (roles/pubsub.publisher) provides access to only publish messages to a Cloud Pub/Sub topic.

- **Custom roles**: Roles that you create to tailor permissions to the needs of your organization when predefined roles don't meet your needs.

# Download and install the Cloud SDK

There are two basic ways to install the Cloud SDK on Linux, and both need Python 2.7 to be installed <br>
The easiest way to install the Cloud SDK is to install into a directory under your own logged in user. This allows you to install the latest version without the need to be an admin of the system. The disadvantage is that only your account is setup to use the installed SDK.<br>
If you want all users of the system to have access to the installed Cloud SDK, you need admin rights to install into the shared software packages for the system.