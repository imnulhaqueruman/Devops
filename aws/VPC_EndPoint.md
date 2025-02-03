# Create a VPC Endpoint and S3 Bucket in AWS

![alt text](https://s3.brilliant.com.bd/blog-bucket/thumbnail/26192cb5-a3d1-42b4-84f3-c7ef08d0c354.gif)

Traditionally, accessing AWS services required routing traffic through the internet, which meant relying on Internet Gateways or NAT Gateways. However, with VPC Endpoints, everything stays within your private network. This means you’re not exposing your data to the public internet, creating a much safer environment.

## What are VPC Endpoints?

A **VPC Endpoint** is a way to privately connect your VPC to AWS services without requiring internet access. 

For example, if you’re running a website on an EC2 instance and want to store user uploads in an S3 bucket, without a VPC Endpoint, your EC2 instance would have to go through the public internet to access S3. This could expose your data to risks. With a **VPC Gateway Endpoint**, your EC2 instance can securely and privately access the S3 bucket without leaving your VPC.

## How Does the VPC Endpoint Work?

1. **Private Connection**: The VPC Endpoint creates a private link between your VPC and the AWS service (like S3 or DynamoDB). This means your data never travels over the public internet.
2. **No Internet Gateway Needed**: Normally, you’d need an Internet Gateway to access AWS services outside your VPC. With a VPC Endpoint, you don’t need one. It’s like having a direct, private road to your destination.
3. **Secure and Fast**: Since the connection is private, it’s more secure and often faster because there’s no traffic or interference from the public internet.

## Types of VPC Endpoints

1. **Gateway Endpoints**: Used for specific AWS services like Amazon S3 and DynamoDB. These endpoints are free to use and provide a direct route for private communication between your VPC and these services.
2. **Interface Endpoints**: Used for other AWS services, powered by AWS PrivateLink. These endpoints use an Elastic Network Interface (ENI) with private IP addresses in your VPC.

In this guide, we’ll focus on **Gateway Endpoints**.

## Gateway Endpoints

Gateway Endpoints are used to connect your VPC to specific AWS services, namely **Amazon S3** and **Amazon DynamoDB**. These endpoints are free to use and provide a direct route for private communication between your VPC and these services.

When you interact with a service that uses a Gateway Endpoint, you actually send requests to a public IP address, which gets routed internally by AWS. This means your request never reaches the internet.

![image](https://s3.brilliant.com.bd/blog-bucket/thumbnail/57421f2c-4722-4cf9-8f98-d55ba480a649.png)

---

## Step-by-Step Guide: Create an S3 Bucket and VPC Endpoint

### 1. Create an S3 Bucket

1. Open the **AWS Management Console** and navigate to the **S3** service.
2. Click **Create bucket**.
3. Name your bucket `vpcendpointbucket` followed by random numbers to ensure uniqueness (e.g., `vpcendpointbucket12345`).
4. Select the region **ap-southeast-1**.
5. Leave all other settings as default and click **Create bucket**.

### 2. Set Up a Gateway Endpoint for S3

1. Open the **AWS Management Console** and navigate to the **VPC Dashboard**.
2. In the left-hand menu, select **Endpoints** and click **Create Endpoint**.
3. Configure the endpoint as follows:
   - **Service Category**: AWS services
   - **Service Name**: Select **com.amazonaws.ap-southeast-1.s3** (S3 service in the ap-southeast-1 region).
   - **VPC**: Select your VPC.
   - **Route Tables**: Select the route tables associated with your private subnets.
   - **Policy**: Leave as default (Full Access) or customize as needed.
4. Click **Create Endpoint**.

![image](https://s3.brilliant.com.bd/blog-bucket/thumbnail/50cfbdfb-f869-4032-ac7e-c1e58ff667c2.png)

### 3. Verify VPC Endpoint Access to S3

1. Navigate to the **Route Tables** section in the VPC Dashboard.
2. Select the route table associated with your private subnets.
3. Under the **Routes** tab, verify that a route to the S3 service has been added with the target as the VPC endpoint (e.g., `vpce-xxxxxxxx`).

![image](https://s3.brilliant.com.bd/blog-bucket/thumbnail/9e72bf44-076c-44a1-8c57-c9de24cf5697.png)

4. Under the **Subnet Associations** tab, ensure that your private subnets are associated with the route table.

![image](https://s3.brilliant.com.bd/blog-bucket/thumbnail/b3d3baa1-f22a-4059-b2da-689f03980c5a.png)

### 4. Configure Security Groups

1. Ensure that your private instances have an outbound rule in their security group allowing HTTPS (port 443) traffic to the S3 service.
2. If you’re using a custom security group, add the following outbound rule:
   - **Type**: HTTPS
   - **Protocol**: TCP
   - **Port Range**: 443
   - **Destination**: 0.0.0.0/0 (or restrict to the S3 service IP range).

### 5. Test the Setup

1. **SSH into your Public Instance**:
   ```bash
   ssh -i "key.pem" ubuntu@<public-ip>
   ```

2. **SSH into your Private Instance** from the public instance:
   ```bash
   ssh -i "key.pem" ubuntu@<private-ip>
   ```

3. **Configure AWS CLI** on the private instance:
   - Install the AWS CLI if not already installed:
     ```bash
     sudo apt update
     sudo apt install awscli
     ```
   - Configure AWS credentials:
     ```bash
     aws configure
     ```
     Provide your AWS Access Key, Secret Key, region (`ap-southeast-1`), and output format (`json`).

4. **Test Access to S3**:
   Run the following command to list S3 buckets:
   ```bash
   aws s3 ls
   ```
   You should see your `vpcendpointbucket` listed.

![image](https://s3.brilliant.com.bd/blog-bucket/thumbnail/3b232650-48af-4828-9d01-2c9311c0cf58.png)

---

## Best Practices for Production

1. **IAM Policies**: Use fine-grained IAM policies to restrict access to the S3 bucket. For example, only allow specific roles or users to access the bucket.
2. **VPC Endpoint Policies**: Customize the VPC endpoint policy to restrict access to specific S3 buckets or actions.
3. **Monitoring**: Enable **VPC Flow Logs** to monitor traffic to and from the VPC endpoint.
4. **Multi-Region Setup**: If your application is multi-region, create VPC endpoints in each region and configure cross-region access as needed.
5. **Encryption**: Enable **Server-Side Encryption (SSE)** for your S3 bucket to ensure data is encrypted at rest.
6. **Backup and Versioning**: Enable **S3 Versioning** and **Cross-Region Replication** for disaster recovery and data redundancy.

---

By following this guide, you’ve successfully created a VPC endpoint and S3 bucket, ensuring secure and private access to your AWS resources. This setup is ideal for production environments where security and performance are critical.