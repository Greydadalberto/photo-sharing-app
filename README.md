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

