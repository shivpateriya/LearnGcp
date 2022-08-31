# Overview
Service accounts are a special type of Google account that grant permissions to virtual machines instead of end users. Service accounts are primarily used to ensure safe, managed connections to APIs and Google Cloud services. Granting access to trusted connections and rejecting malicious ones is a must-have security feature for any Google Cloud project. 


# What are service accounts ?

A service account is a special Google account that belongs to your application or a virtual machine (VM) instead of an individual end user. Your application uses the service account to call the Google API of a service, so that the users aren't directly involved.

For example, a Compute Engine VM may run as a service account, and that account can be given permissions to access the resources it needs. This way the service account is the identity of the service, and the service account's permissions control which resources the service can access.

A service account is identified by its email address, which is unique to the account.

# Types of service accounts

## User-managed service accounts

When you create a new Cloud project using Cloud Console and if Compute Engine API is enabled for your project, a Compute Engine Service account is created for you by default. It is identifiable using the email:

```
PROJECT_NUMBER-compute@developer.gserviceaccount.com
```

If your project contains an App Engine application, the default App Engine service account is created in your project by default. It is identifiable using the email:

```
PROJECT_ID@appspot.gserviceaccount.com
```
# Google-managed service accounts

In addition to the user-managed service accounts, you might see some additional service accounts in your project’s IAM policy or in the Cloud Console. These service accounts are created and owned by Google. These accounts represent different Google services and each account is automatically granted IAM roles to access your Google Cloud project

# Google APIs service account

An example of a Google-managed service account is a Google API service account identifiable using the email:
```
PROJECT_NUMBER@cloudservices.gserviceaccount.com
```
This service account is designed specifically to run internal Google processes on your behalf and is not listed in the Service Accounts section of Cloud Console. By default, the account is automatically granted the project editor role on the project and is listed in the IAM section of Cloud Console. This service account is deleted only when the project is deleted.


# Task 1. Creating and managing service accounts

When you create a new Cloud project, Google Cloud automatically creates one Compute Engine service account and one App Engine service account under that project. You can create up to 98 additional service accounts to your project to control access to your resources.

## Creating a service account

Creating a service account is similar to adding a member to your project, but the service account belongs to your applications rather than an individual end user.

- To create a service account, run the following command in Cloud Shell:

```
gcloud iam service-accounts create my-sa-123 --display-name "my service account"
```
# Granting roles to service accounts

When granting IAM roles, you can treat a service account either as a resource or as an identity.

Your application uses a service account as an identity to authenticate to Google Cloud services. For example, if you have a Compute Engine Virtual Machine (VM) running as a service account, you can grant the editor role to the service account (the identity) for a project (the resource).

At the same time, you might also want to control who can start the VM. You can do this by granting a user (the identity) the serviceAccountUser role for the service account (the resource).


# Granting roles to a service account for specific resources

You grant roles to a service account so that the service account has permission to complete specific actions on the resources in your Cloud Platform project. For example, you might grant the storage.admin role to a service account so that it has control over objects and buckets in Cloud Storage.

- Run the following in Cloud Shell to grant roles to the service account you just made:

```
gcloud projects add-iam-policy-binding $DEVSHELL_PROJECT_ID \
    --member serviceAccount:my-sa-123@$DEVSHELL_PROJECT_ID.iam.gserviceaccount.com --role roles/editor
```


#  Understanding roles
When an identity calls a Google Cloud API, Google Cloud Identity and Access Management requires that the identity has the appropriate permissions to use the resource. You can grant permissions by granting roles to a user, a group, or a service account.

# Types of roles 
 There are three types of roles in Cloud IAM:

- Primitive roles, which include the Owner, Editor, and Viewer roles that existed prior to the introduction of Cloud IAM.
- Predefined roles, which provide granular access for a specific service and are managed by Google Cloud.
- Custom roles, which provide granular access according to a user-specified list of permissions.

# Task 3. Use the client libraries to access BigQuery from a service account

You will first create a new service account from the Cloud Console.

- Go to Navigation menu > IAM & Admin, select Service accounts and click on + Create Service Account.

- Fill necessary details with:

- **Service account name: bigquery-qwiklab**

- Now click Create and Continue and then add the following roles:
- Role: BigQuery Data Viewer and BigQuery User

## create your vm with the following information

| Configuration | Description          |
| ------------- | ----------- |
| NAME      | Bigquery-instance|
| Region    | us-west4    |
| Zone      | us-west4-b |
|Series  | N1|
|Machine Type | n1-standard-2|
|Boot Disk | Debian GNU/Linux 10 (buster) |
| Service account | bigquery-qwiklab |

#SSH into machine

```
sudo apt-get install -y git python3-pip

pip3 install --upgrade pip

pip3 install google-cloud-bigquery
 

pip3 install pyarrow

pip3 install pandas

pip3 install db-dtypes
```

# Now create the example Python file:
```
echo "
from google.auth import compute_engine
from google.cloud import bigquery
credentials = compute_engine.Credentials(
    service_account_email='YOUR_SERVICE_ACCOUNT')
query = '''
SELECT
  year,
  COUNT(1) as num_babies
FROM
  publicdata.samples.natality
WHERE
  year > 2000
GROUP BY
  year
'''
client = bigquery.Client(
    project='YOUR_PROJECT_ID',
    credentials=credentials)
print(client.query(query).to_dataframe())
" > query.py
```

# Add the Project ID to query.py with:

```
sed -i -e "s/YOUR_PROJECT_ID/$(gcloud config get-value project)/g" query.py
```
# Add the service account email to query.py with:

```
sed -i -e "s/YOUR_SERVICE_ACCOUNT/bigquery-qwiklab@$(gcloud config get-value project).iam.gserviceaccount.com/g" query.py
```

```
python3 query.py
```
You should be returned with a similar output:

```
Row year  num_babies
0   2008  4255156
1   2006  4273225
2   2003  4096092
3   2004  4118907
4   2002  4027376
5   2005  4145619
6   2001  4031531
7   2007  4324008
```

