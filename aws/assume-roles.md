# Create and Assume Roles in AWS 

AWS Identity and Access Management (IAM) is a service that allows AWS customers to manage user access and permissions for the accounts and available APIs/services within AWS. IAM can manage users, security credentials (such as API access keys), and allow users to access AWS resources.

In this lab, we discover how security policies affect IAM users and groups, and we go further by implementing our own policies while also learning what a role is, how to create a role, and how to assume a role as a different user.

By the end of this lab, you will understand IAM policies and roles, and how assuming roles can assist in restricting users to specific AWS resources.


## **Create 4 S3 Bucket** 

Navigate to S3 dashboard and Click create Bucket following this image 

![image](https://s3.brilliant.com.bd/blog-bucket/thumbnail/4ab647a8-823f-4783-9f07-a6c5b3302cd5.png)



# Create IAM Users with Email Tag

This guide provides step-by-step instructions to create two IAM users in AWS without assigning any AWS managed policies. Each user will be tagged with an email tag (`Email:poridhistudent@gmail.com`).


---

## Steps to Create IAM Users

### Method 1: Using AWS Management Console

1. **Sign in to the AWS Management Console**  
   Navigate to IAM by using AWS console 

2. **Create the First IAM User**  
   - Navigate to **Users** in the left-hand menu and click **Add users**.
   - Enter a username for the first user (e.g., `User1`).
   - Click **Next: Permissions**. 



3. **Set Permissions**  
   - Select `Attach Policies Directly ` but Do not attach any policies. Click **Next: Tags**.

![iam-permisiion](https://s3.brilliant.com.bd/blog-bucket/thumbnail/d184d67c-2575-41a2-ba49-6327dfcf2d8b.png)


4. **Add Tags**  
   - Click **Add tag**.
   - Enter `Email` as the key and `poridhistudent@gmail.com` as the value.
   - Click **Next: Review**.

![tag](https://s3.brilliant.com.bd/blog-bucket/thumbnail/579226c4-ca27-40f2-81b3-eb8c95762d3d.png)

5. **Review and Create**  
   - Review the details and click **Create user**.
   - Save the credentials (Access Key ID and Secret Access Key) securely.
6. **Enable console Access**
   - Select `user1` and go to user1 details page 
   - Then select Security Credentails tab and click Enable console access 

![image](https://s3.brilliant.com.bd/blog-bucket/thumbnail/6ace1158-6d61-4df9-a4d8-61fee352c167.png)

7. **Repeat for the Second User**  
   - Follow the same steps to create the second user (e.g., `User2`).

---

### Method 2: Using AWS CLI

1. **Install and Configure AWS CLI**  
   If not already installed, follow the [AWS CLI installation guide](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html).  
   Configure the CLI with your credentials using:  
   ```bash
   aws configure
   ```

2. **Create the First IAM User**  
   Run the following command to create the first user:  
   ```bash
   aws iam create-user --user-name User1
   ```

3. **Tag the First User**  
   Add the email tag to the first user:  
   ```bash
   aws iam tag-user --user-name User1 --tags Key=Email,Value=poridhistudent@gmail.com
   ```

4. **Create the Second IAM User**  
   Run the following command to create the second user:  
   ```bash
   aws iam create-user --user-name User2
   ```

5. **Tag the Second User**  
   Add the email tag to the second user:  
   ```bash
   aws iam tag-user --user-name User2 --tags Key=Email,Value=poridhistudent@gmail.com
   ```

6. **Verify the Users**  
   To verify the users and their tags, run:  
   ```bash
   aws iam list-users
   aws iam get-user --user-name User1
   aws iam get-user --user-name User2
   ```

---

## Notes
- Ensure that the users have no permissions initially. You can attach custom policies later if needed.
- The email tag (`Email:poridhistudent@gmail.com`) is used for identification and organization purposes.
- Securely store the credentials for programmatic access.

---

## Troubleshooting
- If you encounter permission issues, ensure your IAM user or role has the necessary permissions (`iam:CreateUser`, `iam:TagUser`).
- If the AWS CLI commands fail, verify that the CLI is configured correctly with valid credentials.


Below is a modified `README.md` document that provides step-by-step instructions for creating the `S3RestrictedPolicy` IAM policy, restricting access to specific S3 buckets, and attaching the policy to a user.

---

# Create and Attach the S3RestrictedPolicy IAM Policy

This guide provides instructions to create an IAM policy named `S3RestrictedPolicy` that restricts access to specific S3 buckets (`bucketprod1` and `bucketprod2`). The policy will then be attached to an IAM user (`user1`).

---


## Steps to Create and Attach the S3RestrictedPolicy

### Step 1: Navigate to S3 and Review Buckets
1. Go to the [S3 Console](https://s3.console.aws.amazon.com/).
2. Review the four provisioned buckets:
   - Two buckets with `bucketprod3 and bucketprod4` in the name.
   - Two buckets with `bucketprod` in the name (`bucketprod1` and `bucketprod2`).

### Step 2: Open IAM in a New Tab
1. In the top search bar, search for **IAM**.
2. Right-click on IAM and open it in a new tab.

### Step 3: Review IAM Users
1. In the IAM console, under **Access Management**, click **Users**.
2. Review the available users. Ensure `user1` exists.

### Step 4: Create the S3RestrictedPolicy
1. In the IAM console, under **Access Management**, click **Policies**.
2. Click **Create Policy**.
3. Under **Select a service**, click **S3**.
4. Under **Actions allowed**, select **All S3 actions**.
5. Under **Resources**, configure the following:

![policy](https://s3.brilliant.com.bd/blog-bucket/thumbnail/992780b2-f356-4f06-846c-4cb8444e502c.png)

   - For **bucket**:
     - Uncheck **Any in this account**.
     - Click **Add ARNs**.
     - Copy the bucket name for `bucketprod1` from the S3 console and paste it into the **Resource bucket name** field.
     - Click **Add ARNs**.

[image](https://s3.brilliant.com.bd/blog-bucket/thumbnail/bb3b7e73-f948-4296-bbab-582f325969bb.png)

     - Repeat the process for `bucketprod2`.
   - For **object**:
     - Select the **Any** checkbox.
6. Click **Next**.

### Step 5: Name and Create the Policy
1. On the **Review and create** page, enter `S3RestrictedPolicy` as the policy name.
2. Click **Create policy**.

### Step 6: Verify the Policy
1. Use the search bar in the IAM Policies section to search for `S3RestrictedPolicy`.
2. Click on the policy to review its configuration.
3. Ensure the policy restricts access to only the `bucketprod1` and `bucketprod2` buckets.

### Step 7: Attach the Policy to `user1`
1. In the `S3RestrictedPolicy` policy details, click the **Entities attached** tab.
2. Under **Attached as a permissions policy**, click **Attach**.
3. Select the checkbox next to `user1`.
4. Click **Attach policy**.

---

## Verification
1. Log in as `user1` and verify that the user can only access the `appconfigprod1` and `appconfigprod2` buckets.
2. Ensure `user1` cannot access the `customerdata` buckets.

---

## Notes
- The `S3RestrictedPolicy` grants full S3 access but restricts it to only the `appconfigprod1` and `appconfigprod2` buckets.
- Ensure there are no trailing whitespaces when adding bucket ARNs.
- Modify the policy as needed to further restrict or grant permissions.

# Create the IAM Role and Allow `user2` to Assume It

This guide provides instructions to create an IAM role named `S3RestrictedRole`, attach the `S3RestrictedPolicy` to it, and configure the role's trust relationship to allow `user2` to assume it.

---



## Steps to Create the IAM Role

### Step 1: Navigate to IAM Roles
1. Go to the [IAM Console](https://console.aws.amazon.com/iam/).
2. In the left navigation menu, click **Roles**.

### Step 2: Create the Role
1. Click **Create role**.
2. Under **Trusted entity type**, select **AWS account**.
3. Under **An AWS account**, select **This account**.
4. Copy the account ID displayed in parentheses and save it to a text file for later use.
5. Click **Next**.

![image](https://s3.brilliant.com.bd/blog-bucket/thumbnail/d285de8d-6311-48ca-87a0-e0a4766bc6d9.png)

### Step 3: Attach the S3RestrictedPolicy
1. Use the search bar to look for the `S3RestrictedPolicy`.
2. Click the checkbox next to the `S3RestrictedPolicy`.
3. Click **Next**.

![image](https://s3.brilliant.com.bd/blog-bucket/thumbnail/561ecd7d-4e6f-4f7f-b88a-4c0b23273ccb.png)

### Step 4: Name and Create the Role
1. In the **Role name** field, enter `S3RestrictedRole`.
2. Review the trusted entity (your account number) to confirm that any entity in this account can assume this role.
3. Click **Create role**.

---

## Steps to Allow `user2` to Assume the Role

### Step 1: Copy `user2` ARN
1. In the IAM console, click **Users** in the left navigation menu.
2. Click on `user2`.
3. Under the **Summary** section, copy the **ARN** of `user2`.

### Step 2: Modify the Trust Relationship
1. In the IAM console, click **Roles** in the left navigation menu.
2. In the search bar, enter `S3` and select the `S3RestrictedRole`.
3. Click the **Trust relationships** tab.
4. Click **Edit trust policy**.
5. In line 7 of the trust policy, delete the existing ARN and paste the ARN you copied for `user2`. Ensure the ARN is enclosed in quotation marks.
6. Click **Update policy**.

![image](https://s3.brilliant.com.bd/blog-bucket/thumbnail/0f8a5386-a9e3-4a91-8c8f-396da6bf6b5f.png)

---

## Verification
1. Log in as `user2` and attempt to assume the `S3RestrictedRole`.
2. Verify that `user2` can successfully assume the role and access the `appconfigprod1` and `appconfigprod2` buckets.


## Notes
- The `S3RestrictedRole` allows entities in the same AWS account to assume it, but the trust policy has been modified to restrict this to only `user2`.
- Ensure the ARN in the trust policy is correctly formatted and enclosed in quotation marks.
- Modify the trust policy further if additional users or roles need to assume the role.

Below is a `README.md` document that provides step-by-step instructions for testing the IAM policy and role configuration for `user1` and `user2`.

---

# Test IAM Policy and Role Configuration

This guide provides instructions to test the IAM policy and role configuration for `user1` and `user2`. It verifies that:
- `user1` has access only to the `bucketprod1 and bucketprod2` buckets.
- `user2` can assume the `S3RestrictedRole` and access the `bucketprod1 and bucketprod2` buckets.


## Steps to Test `user1` Configuration

### Step 1: Log in as `user1`
1. Navigate to the [AWS Management Console](https://aws.amazon.com/console/).
2. In the upper right corner, copy the **Account ID** to your clipboard.
3. Click **Sign out**.
4. Click **Log back in**.
5. For **Account ID**, paste the account ID you copied.
6. For **IAM user name**, enter `user1`.
7. For **Password**, enter the password provided for `user1`.
8. Click **Sign in**.

### Step 2: Verify EC2 Access
1. Navigate to the **EC2** service.
2. You should see an **API Error**, indicating that `user1` does not have access to EC2.

### Step 3: Verify S3 Access
1. Navigate to the **S3** service.
2. Click on one of the `customerdata` buckets.
   - You should see an **Access Denied** message.
3. Click the **Buckets** breadcrumb at the top of the screen.
4. Navigate to one of the `appconfigprod` buckets and open it.
   - You should have access to the bucket.

---

## Steps to Test `user2` Configuration

### Step 1: Log in as `user2`
1. Sign out of the AWS Management Console.
2. Log back in using the **Account ID**, **IAM user name** (`user2`), and **Password** provided for `user2`.

### Step 2: Verify S3 Access
1. Navigate to the **S3** service.
2. Attempt to access any of the buckets (`customerdata` or `appconfigprod`).
   - You should see an **Access Denied** message for all buckets.

### Step 3: Assume the `S3RestrictedRole`
1. In the upper right corner, copy the **Account ID** to your clipboard.
2. Click **Switch role**.
3. For **Account**, paste the account ID you copied.
4. For **Role**, enter `S3RestrictedRole` (ensure it is spelled correctly).
5. Select a color of your choice and click **Switch Role**.

### Step 4: Verify S3 Access with the Role
1. In the **S3** console, confirm that you can now see the `appconfigprod` buckets.
2. Click on one of the `customerdata` buckets.
   - You should see an **Insufficient permissions** message.
3. Click the **Buckets** breadcrumb at the top of the screen.
4. Click on one of the `appconfigprod` buckets to confirm you have access.

---



## Notes
- `user1` should only have access to the `appconfigprod` buckets due to the `S3RestrictedPolicy`.
- `user2` should only have access to the `appconfigprod` buckets after assuming the `S3RestrictedRole`.
- Ensure the role name and account ID are entered correctly when switching roles.


