
Deploying a Web Application to AWS: S3, CloudFront, and CI/CD with GitHub Actions

Introduction

In this blog, I’ll walk you through the steps of deploying a web application on AWS using S3, setting up a CDN with CloudFront, and automating the deployment process with GitHub Actions for CI/CD.

Key Objectives:

Create an S3 bucket to store and serve static files.

Push code to the S3 bucket for deployment.

Configure CloudFront to use the S3 bucket as an origin for CDN delivery.

Set up a CI/CD pipeline using GitHub Actions for automatic deployments.

Step 1: Setting Up an S3 Bucket for Static Hosting

Amazon S3 (Simple Storage Service) is perfect for hosting static websites. We will create a bucket and configure it for web hosting.

Steps:

Login to AWS Management Console

Navigate to S3 in the AWS console.

Create a New S3 Bucket:

Click on "Create Bucket."

Choose a bucket name (it must be globally unique).

Select a region (preferably close to your users).

Leave the other options as default for now and click Create.

Enable Static Website Hosting:

Once the bucket is created, go to Properties.

Scroll down to Static Website Hosting and enable it.

Specify an index document (like index.html) and an error document (like error.html).

Bucket Permissions:

Go to the Permissions tab.

Under Bucket Policy, add the following policy to make the content publicly accessible:

Step 2: Uploading Code to S3

Once the S3 bucket is ready, we need to upload our application (HTML, CSS, JS files) to it.

Steps:

Upload Files Manually:

In the S3 bucket, go to the Objects tab and click Upload.

Select all the files of your static site (HTML, CSS, JS, etc.).

Review and upload the files.

Upload Files via AWS CLI:

Install AWS CLI and configure it by running:

aws configure

Enter your AWS Access Key, Secret Key, Region, and Output format.

Upload your local files with the following command:

aws s3 sync ./my-website s3://YOUR_BUCKET_NAME

Step 3: Configuring CloudFront for Content Delivery

AWS CloudFront is a Content Delivery Network (CDN) service that delivers your content globally with low latency.

Steps:

Create a CloudFront Distribution:

In the AWS Console, navigate to CloudFront.

Click Create Distribution.

Choose Web as the delivery method.

For Origin Domain Name, select your S3 bucket.

Under Default Cache Behavior, set Viewer Protocol Policy to Redirect HTTP to HTTPS.

Update Bucket Permissions for CloudFront:

Ensure your bucket policy allows CloudFront to access the bucket. You might need to add permissions for the CloudFront OAI (Origin Access Identity) if you're securing access.

Finalize Distribution Settings:

Under Default Root Object, set it to index.html.

Create the distribution and wait for the status to change to Deployed.

Point Your Domain (Optional):

If you own a domain, you can point it to the CloudFront distribution by updating the DNS settings in Route 53 or another DNS provider.

Step 4: Setting Up CI/CD Pipeline with GitHub Actions

To automate the deployment process, we’ll use GitHub Actions. Every time you push code to GitHub, the CI/CD pipeline will automatically deploy the updated files to S3.

Steps:

Create a GitHub Repository:

If you haven't already, create a GitHub repository and push your code.

Configure GitHub Secrets:

In the GitHub repo, go to Settings > Secrets.

Add the following secrets:

AWS_ACCESS_KEY_ID

AWS_SECRET_ACCESS_KEY

AWS_REGION

S3_BUCKET_NAME

Create the GitHub Actions Workflow:

In your repo, create a folder called .github/workflows.

Inside it, create a file named deploy.yml and add the following YAML configuration:

Inside it, create a file named deploy.yml and add the following YAML configuration:

yamlCopy codename: Deploy to S3

on:
  push:
    branches:
      - main  # Change this to your default branch

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v1
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ secrets.AWS_REGION }}

      - name: Sync S3 Bucket
        run: |
          aws s3 sync . s3://$S3_BUCKET_NAME --delete

Test the Workflow:

Push your changes to GitHub. GitHub Actions will automatically trigger the deployment.

Monitor the Actions tab in GitHub to see if the pipeline executes successfully.

Conclusion

By following these steps, you've created a scalable and reliable infrastructure using AWS. You’ve hosted your web application in an S3 bucket, optimized it for delivery with CloudFront, and automated your deployment pipeline using GitHub Actions. This setup provides both a secure and efficient way to handle static site deployments and updates.

Feel free to reach out if you have any questions or need further clarifications!
