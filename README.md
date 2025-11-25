# Portfolio Website -- CI/CD with GitHub Actions & AWS S3

This project contains a personal portfolio website deployed using a
CI/CD pipeline powered by GitHub Actions.\
Whenever code is pushed to the `main` branch, the workflow automatically
uploads the updated website to an AWS S3 bucket.

## Features

-   Automatic deployment on every push to `main`
-   Website hosted on AWS S3 Static Website Hosting
-   Public-read access for all website assets
-   Clean folder structure for code and assets

## Project Structure

    /
    ├── index.html
    └── src/
        ├── styles/
        ├── img/
        └── app.js

## GitHub Actions Workflow

    name: Upload Portfolio

    on:
      push:
        branches:
          - main

    jobs:
      deploy:
        runs-on: ubuntu-latest
        steps:
          - name: Checkout Repository
            uses: actions/checkout@v4

          - name: Sync Files to S3
            uses: jakejarvis/s3-sync-action@master
            with:
              args: --acl public-read --follow-symlinks --delete
            env:
              AWS_S3_BUCKET: ${{ secrets.AWS_S3_BUCKET }}
              AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
              AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
              AWS_REGION: ${{ secrets.AWS_REGION }}
              SOURCE_DIR: 'website'

## AWS S3 Setup

### 1. Enable Static Website Hosting

Go to:

    S3 → Bucket → Properties → Static website hosting

Set:

    index document: index.html

### 2. Bucket Policy (Public Read Access)

    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Sid": "PublicReadGetObject",
                "Effect": "Allow",
                "Principal": "*",
                "Action": "s3:GetObject",
                "Resource": "arn:aws:s3:::portfolio-bucket-cicd-25-11/*"
            }
        ]
    }

### 3. Disable Block Public Access

Go to:

    S3 → Permissions → Block Public Access

Turn OFF public access blocking.

## GitHub Secrets Required

Add these in:

    GitHub → Repository → Settings → Secrets → Actions

-   `AWS_S3_BUCKET`
-   `AWS_ACCESS_KEY_ID`
-   `AWS_SECRET_ACCESS_KEY`
-   `AWS_REGION`

## Final Hosted URL

Format:

    http://<bucket-name>.s3-website-<region>.amazonaws.com/

Example:

    http://portfolio-bucket-cicd-25-11.s3-website-ap-south-1.amazonaws.com/

## How It Works

1.  Edit code and push to `main`
2.  GitHub Action runs automatically
3.  Files sync to S3 with public-read access
4.  Website updates instantly
