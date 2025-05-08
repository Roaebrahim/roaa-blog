# MERN Stack Blog App Deployment on AWS

## Overview
This project involves deploying a MERN stack blog application on AWS using free-tier eligible services. The backend (Express) runs on EC2, while the frontend (React) is hosted via S3 static website hosting. MongoDB Atlas is used as the database, and media uploads are handled through a separate S3 bucket with the necessary IAM permissions.

## Table of Contents
1. [MongoDB Atlas Configuration](#part-1-mongodb-atlas-configuration)
2. [S3 Bucket for Frontend](#part-2-s3-bucket-for-frontend)
3. [S3 for Media Uploads](#part-3-s3-for-media-uploads)
4. [IAM User and Policy for S3 Media Bucket Access](#part-4-iam-user-and-policy-for-s3-media-bucket-access)
5. [EC2 Backend Setup](#part-5-ec2-backend-setup)
6. [Deployment Steps](#deployment-steps)
7. [Success Criteria](#success-criteria)
8. [Cleanup Reminder](#cleanup-reminder)

## Part 1: MongoDB Atlas Configuration (OPTIONAL)
1. Create a MongoDB Atlas account.
2. Set up a free cluster.
3. Allow EC2 IP in network access settings.
4. Create a database user and obtain the connection string.
5. Add the connection string to your backend .env file.

## Part 2: S3 Bucket for Frontend
1. Create an S3 bucket named yourname-blogapp-frontend in eu-north-1.
2. Disable "Block all public access".
3. Enable static website hosting.
4. Add a bucket policy for public access:
    json
    {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Principal": "*",
          "Action": "s3:GetObject",
          "Resource": "arn:aws:s3:::yourname-blogapp-frontend/*"
        }
      ]
    }
    
5. Deliverables:  
   - Screenshot of S3 public URL loading index.html.
   - curl -I output showing 200 OK.

## Part 3: S3 for Media Uploads
1. Create a second bucket: yourname-blogapp-media.
2. Disable "Block all public access".
3. Configure CORS for browser upload support:
    json
    [
      {
        "AllowedHeaders": ["*"],
        "AllowedMethods": ["GET", "PUT", "POST", "DELETE", "HEAD"],
        "AllowedOrigins": ["*"],
        "ExposeHeaders": ["ETag"]
      }
    ]
    
4. Test uploading and retrieving a file.
5. Deliverables:
   - Screenshot of file upload.

## Part 4: IAM User and Policy for S3 Media Bucket Access
1. Go to IAM Console > Users > Add users.
2. Username: blog-app-user.
3. Attach existing policies directly and create a custom policy:
    json
    {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Action": [
            "s3:PutObject",
            "s3:GetObject",
            "s3:DeleteObject",
            "s3:ListBucket"
          ],
          "Resource": [
            "arn:aws:s3:::roaa-blogapp-media",
            "arn:aws:s3:::roaa-blogapp-media/*"
          ]
        }
      ]
    }
    
4. Attach this policy to the user.
5. Deliverables:
   - Screenshot of IAM user creation.

## Part 5: EC2 Backend Setup
1. Launch a t3.micro instance in eu-north-1 using Ubuntu 22.04 LTS.
2. Allow incoming traffic only on necessary ports (SSH, HTTP, HTTPS, and Custom TCP port 5000).
3. SSH into the instance and run the User Data script to set up the environment:
    bash
    # Install dependencies
    apt update -y
    apt install -y git curl unzip tar gcc g++ make unzip
    
4. Clone the MERN app and configure the environment:
    bash
    git clone https://github.com/cw-barry/blog-app-MERN.git /home/ubuntu/blog-app
    cd /home/ubuntu/blog-app
    

5. Set up environment variables in the backend .env file and frontend .env file with the appropriate configurations for MongoDB, AWS, and JWT.

## Deployment Steps
1. Deploy the backend using PM2.
2. Configure the frontend using the latest version of PNPM.
3. Deploy the frontend to the S3 bucket.
4. Deliverables:
   - Screenshot of the backend server running via PM2.
   - Screenshot of the frontend S3 webpage.

## Success Criteria
1. The app runs with full MERN functionality: create blog, upload media.
2. Public access via S3 for frontend and media files.
3. Secure backend access using environment variables.
4. MongoDB Atlas reachable and storing blog data.

## Cleanup Reminder
1. Stop the EC2 instance.
2. Remove IAM credentials.
3. Delete S3 buckets if no longer needed.