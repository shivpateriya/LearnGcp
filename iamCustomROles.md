### Run the following to get the list of permissions available for your project.:


```
gcloud iam list-testable-permissions //cloudresourcemanager.googleapis.com/projects/$DEVSHELL_PROJECT_ID
```

### To view the role metadata, use command below, replacing [ROLE_NAME] with the role. For example: roles/viewer or roles/editor:

```
gcloud iam roles describe [ROLE_NAME]
```

### Use the gcloud iam list-grantable-roles command to return a list of all roles that can be applied to a given resource.

```
gcloud iam list-grantable-roles //cloudresourcemanager.googleapis.com/projects/$DEVSHELL_PROJECT_ID
```
---
## To create a custom role using a YAML file
Create a YAML file that contains the definition for your custom role. The file must be structured in the following way:

```
title: [ROLE_TITLE]
description: [ROLE_DESCRIPTION]
stage: [LAUNCH_STAGE]
includedPermissions:
- [PERMISSION_1]
- [PERMISSION_2]
```
Each of the placeholder values is described below:

**[ROLE_TITLE]** is a friendly title for the role, such as <b>Role Viewer</b>.

**[ROLE_DESCRIPTION]** is a short description about the role, such as <b>My custom role description.</b>

**[LAUNCH_STAGE]** indicates the stage of a role in the launch lifecycle, such as ALPHA, BETA, or GA.

includedPermissions specifies the list of one or more permissions to include in the custom role, such as <b>iam.roles.get.</b>

## Example
```
title: "Role Editor"
description: "Edit access for App Versions"
stage: "ALPHA"
includedPermissions:
- appengine.versions.create
- appengine.versions.delete
```
# Command

```
gcloud iam roles create editor --project $DEVSHELL_PROJECT_ID \
--file role-definition.yaml
```
# Listing the custom roles

```
gcloud iam roles list --project $DEVSHELL_PROJECT_ID
```
## To list deleted roles, you can also specify the --show-deleted flag.

## Execute the following gcloud command to list predefined roles:
```
gcloud iam roles list
```

---

# Editing an existing custom role

A common pattern for updating a resource's metadata, such as a custom role, is to read its current state, update the data locally, and then send the modified data for writing. This pattern could cause a conflict if two or more independent processes attempt the sequence simultaneously.

For example, if two owners for a project try to make conflicting changes to a role at the same time, some changes could fail.

Cloud IAM solves this problem using an etag property in custom roles. This property is used to verify if the custom role has changed since the last request. When you make a request to Cloud IAM with an etag value, Cloud IAM compares the etag value in the request with the existing etag value associated with the custom role. It writes the change only if the etag values match.

Use the gcloud iam roles update command to update custom roles. You can use this command in two ways:

- *By providing a YAML file that contains the updated role definition*
- *By using flags to specify the updated role definition*
When updating a custom role, you must specify whether it applies to the organization level or project level by using the ``` --organization [ORGANIZATION_ID] or --project [PROJECT_ID] ``` flags. Each example below creates a custom role at the project level.

The ```describe``` command returns the role's definition and includes an etag value that uniquely identifies the current version of the role. The etag value should be provided in the updated role definition to ensure that any concurrent role changes are not overwritten.

# To update a custom role using a YAML file
1.Get the current definition for the role by executing the following gcloud command, replacing [ROLE_ID] with editor.
```
gcloud iam roles describe [ROLE_ID] --project $DEVSHELL_PROJECT_ID
```

The describe command returns the following output:

```
description: [ROLE_DESCRIPTION]
etag: [ETAG_VALUE]
includedPermissions:
- [PERMISSION_1]
- [PERMISSION_2]
name: [ROLE_ID]
stage: [LAUNCH_STAGE]
title: [ROLE_TITLE
```
# Create a new-role-definition.yaml file with your editor:
nano new-role-definition.yaml

```
description: Edit access for App Versions
etag: BwVxIAbRq_I=
includedPermissions:
- appengine.versions.create
- appengine.versions.delete
- storage.buckets.get
- storage.buckets.list
name: projects/[PROJECT_ID]/roles/editor
stage: ALPHA
title: Role Editor
```

# command

```
gcloud iam roles update [ROLE_ID] --project $DEVSHELL_PROJECT_ID \
--file new-role-definition.yaml
```

# To update a custom role using flags

You can use the following flags to add or remove permissions:

- *--add-permissions: Adds one or more comma-separated permissions to the role.*
- *--remove-permissions: Removes one or more comma-separated permissions from the role*
Alternatively, you can simply specify the new permissions using the ```--permissions [PERMISSIONS]``` flag and providing a comma-separated list of permissions to replace the existing permissions list.

Execute the following gcloud command to add permissions to the viewer role using flags:

```
gcloud iam roles update viewer --project $DEVSHELL_PROJECT_ID \
--add-permissions storage.buckets.get,storage.buckets.list
```

# Disabling a custom role
When a role is disabled, any policy bindings related to the role are inactivated, meaning that the permissions in the role will not be granted, even if you grant the role to a user.

The easiest way to disable an existing custom role is to use the --stage flag and set it to DISABLED.

- Execute the following gcloud command to disable the viewer role:
```
gcloud iam roles update viewer --project $DEVSHELL_PROJECT_ID \
--stage DISABLED
```
# Deleting a custom role
- Use the gcloud iam roles delete command to delete a custom role. Once deleted the role is inactive and cannot be used to create new IAM policy bindings: 

```gcloud iam roles delete viewer --project $DEVSHELL_PROJECT_ID```

#Example ouput:

```
deleted: true
description: Custom role description.
etag: BwVxLkf_epw=
includedPermissions:
- compute.instances.get
- compute.instances.list
- storage.buckets.get
- storage.buckets.list
name: projects/[PROJECT_ID]/roles/viewer
stage: DISABLED
title: Role Viewer
```
After the role has been deleted, existing bindings remain, but are inactive. The role can be undeleted within 7 days. After 7 days, the role enters a permanent deletion process that lasts 30 days. After 37 days, the Role ID is available to be used again.

```
Note: If a role is being phased out, change its role.stage property to DEPRECATED, and set the `deprecation_message` to let users know what alternative roles should be used or where to get more information.
```
# Undeleting a custom role

- Within the 7 days window you can undelete a role. Deleted roles are in a DISABLED state. You can make it available again by updating the --stage flag:

```
gcloud iam roles undelete viewer --project $DEVSHELL_PROJECT_ID
```