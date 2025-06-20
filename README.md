Photo Sharing App (AWS S3 + Lambda + API Gateway)
A simple serverless photo-sharing web application where users can upload images and view them in a thumbnail gallery.

Architecture Overview
User (Browser) ↔ S3 (Static Website Hosting)
                     ↕
                 API Gateway (GET Images)
                     ↕
                Lambda Function (List thumbnails)
                     ↕
              S3 Bucket (Thumbnails)

Project Components
2 S3 Buckets

photo-sharing1-app-bucket: Original uploaded images.

photo-sharing1-thumbnails: Stores resized thumbnails.

Lambda Function

Lists thumbnail images for the gallery.

API Gateway

Exposes the Lambda function via HTTP GET request.

Frontend (index.html)

Uploads images to S3.

Loads thumbnails via API Gateway.


  Create S3 Buckets
Bucket 1: photo-sharing1-app-bucket
Purpose: Store original uploaded images.

Bucket 2: photo-sharing1-thumbnails
Purpose: Store resized images (thumbnails).

Enable Static Website Hosting for photo-sharing1-app-bucket:
Go to Properties → "Static website hosting" → Enable.

Set Index document: index.html


Configure CORS on photo-sharing1-app-bucket:
[
  {
    "AllowedHeaders": ["*"],
    "AllowedMethods": ["GET", "PUT", "POST"],
    "AllowedOrigins": ["*"],
    "ExposeHeaders": []
  }
]

Upload index.html to S3 Bucket
Upload your index.html to photo-sharing1-app-bucket.

wrote a lambda function to list all thumbnails:
Lambda Python Code:
import json
import boto3

s3 = boto3.client('s3')
bucket_name = 'photo-sharing1-thumbnails'

def lambda_handler(event, context):
    try:
        response = s3.list_objects_v2(Bucket=bucket_name)
        files = []

        if 'Contents' in response:
            for obj in response['Contents']:
                file_url = f"https://{bucket_name}.s3.{boto3.session.Session().region_name}.amazonaws.com/{obj['Key']}"
                files.append(file_url)

        return {
            'statusCode': 200,
            'headers': {
                'Access-Control-Allow-Origin': '*',
                'Access-Control-Allow-Headers': '*'
            },
            'body': json.dumps({'files': files})
        }

    except Exception as e:
        return {
            'statusCode': 500,
            'body': json.dumps({'error': str(e)})
        }

included an IAM Role for Lambda
Grant Lambda these permissions:

{
  "Effect": "Allow",
  "Action": ["s3:ListBucket", "s3:GetObject"],
  "Resource": [
    "arn:aws:s3:::photo-sharing1-thumbnails",
    "arn:aws:s3:::photo-sharing1-thumbnails/*"
  ]
}

Create API Gateway
Type: HTTP API (or REST API).

Integration: Lambda Function (GET Images).

Enable CORS: Allow all origins (*).

Invoke URL : https://33pfwjids1.execute-api.eu-west-1.amazonaws.com/prod/images

index.html is updated!

to test end to end, 
visit : https://photo-sharing1-app-bucket.s3.eu-west-1.amazonaws.com/index.html
Upload an image.

Image is stored in photo-sharing1-app-bucket.

Lambda resizes image to thumbnail & saves in photo-sharing1-thumbnails.

API Gateway + Lambda lists the thumbnails.

in conclusion I have built and deployed a fully serverless Photo Sharing App on AWS using:

S3 (for storage + static hosting)

Lambda (backend)

API Gateway (public API)

HTML / JS Frontend.
