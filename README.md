## Hosting a Website on AWS S3 with CloudFront and Route 53

This guide walks you through the process of hosting a static website on Amazon S3 using AWS CloudFront for improved performance and security. We'll also cover setting up HTTPS using an SSL certificate and managing the domain using Amazon Route 53.

### Step 1: Setting Up S3 Buckets

1. Create an S3 bucket with the name of your domain (e.g., `hamza-salam.click`).
2. Create another bucket with the prefix `www.` and your domain (e.g., `www.hamza-salam.click`).
3. Choose the main bucket where you want to host the static website. Enable static website hosting and specify the index document (e.g., `index.html`). Save the provided endpoint (e.g., `http://hamza-salam.click.s3-website.eu-west-2.amazonaws.com`).
4. Remove block public access settings.

### Step 2: Configure Bucket Policy

Upload a bucket policy to allow public read access to your main bucket. Replace `Bucket-Name` with the actual bucket name:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::Bucket-Name/*"
            ]
        }
    ]
}
```

### Step 3: Configure www.(domain) Bucket

1. Enable static website hosting for the `www.` bucket. Choose to redirect requests, and set the hostname to your main domain (e.g., `hamza-salam.click`).
2. Upload all your website content to the main domain bucket. Ensure there's no additional file before `index.html`.

### Step 4: Set Up Route 53

1. In the AWS Management Console, navigate to Route 53 and create a hosted zone for your domain.
2. Define a simple record, adding the endpoint of the main domain bucket as the value to route traffic.

### Step 5: Setting Up HTTPS

1. Request a public SSL certificate from AWS Certificate Manager (make sure to choose `us-east-1` as the region).
2. Add both domain names (with and without `www.`) to the certificate request (e.g., `hamza-salam.click` and `www.hamza-salam.click`).
3. After the certificate is issued, go to its details and create records to Route 53.
4. Wait for the certificate validation process to complete.

### Step 6: CloudFront Distribution

1. Create a CloudFront distribution.
2. For the origin settings, use the static bucket website endpoint from S3 as the origin.
3. Configure viewer protocol policy to redirect HTTP to HTTPS.
4. Add your domain and `www` subdomain to the CNAMEs, and select the custom SSL certificate you've created.
5. Skip Web Application Firewall (WAF) for now and create the distribution.
6. Wait for the distribution's state to be deployed and ensure the domain name works properly.

### Step 7: Update Route 53 Records

1. In Route 53, create a simple record for your main domain, routing traffic to the CloudFront distribution you created earlier.
2. Create another simple record for the `www` subdomain, routing traffic to the `www` bucket's endpoint.

### Step 8: Testing and Maintenance

You're all set! Test your website by accessing your domain and `www` subdomain. If you need to update your website, remember to upload changes to the S3 bucket and create an invalidation using `/*` in CloudFront to ensure changes propagate effectively.

Congratulations, your website is now securely hosted on AWS with enhanced performance and HTTPS support! 🚀🌐
