# Step-by-Step Guide to Build Serverless File Sharing Platform

### Project Description:

The Serverless File Sharing Platform allows users to securely upload and download files via a simple HTTP API. It leverages AWS Lambda for serverless compute, API Gateway for RESTful API management, and Amazon S3 for durable and scalable object storage. Users can interact with the platform using any HTTP client (like Postman), making it versatile for various use cases that involve file sharing and storage.


Use Cases: 

* File Sharing: Users can upload files to the platform using a POST request, specifying the file name and content. This can be useful for sharing documents, images, or any other digital assets securely. 
* File Distribution: Content creators can distribute files (e.g., software updates, media files) to consumers by generating download links through the platform. 
* Collaborative Work: Teams can share project resources, documents, and data securely, facilitating collaboration across different locations.

### Project Architecture:

<img width="1132" alt="Screenshot 2024-07-09 at 10 38 15 PM" src="https://github.com/yeshwanthlm/Serverless-File-Sharing-Platform/assets/66474973/702f29d8-8eca-4d17-9842-e291ff945801">


### Steps to Build the Project:

## Prerequisites

 AWS Account with appropriate permissions to create Lambda functions, API Gateway, and S3 buckets.


## Steps to Deploy

* Step 1: Create an S3 bucket to store uploaded files:
  ```bash
  filesharing.bucket
  ```
 ![img](https://github.com/riyaj-2002/serverless-file-sharing/blob/e46852148d0960e2eb73612d4a20aef5c30c925d/Screenshot%202025-09-08%20180008.png)

  
* Step 2: Create the Lambda function to handle file uploads (UploadFunction): \
  Name: filesharing_uploadfunction \
  Runtime: Python 3.x \
  Execution role: IAM role with S3 write permissions \
  Code: Use the UploadFunction Python code.

* Step 3: Create a lambda role with custom policy (iam_policy.json):
   ```bash
  lambda_s3role
  ```
![img](https://github.com/riyaj-2002/serverless-file-sharing/blob/04222116f769473b34657ccd98c8a8620792163e/Screenshot%202025-09-08%20180129.png)


* Step 4: Create the Lambda function to handle file downloads (DownloadFunction): \
  Name: filesharing_downloadfunction \
  Runtime: Python 3.x \
  Execution role: IAM role with S3 write permissions \
  Code: Use the DownloadFunction Python code.

  ![img](https://github.com/riyaj-2002/serverless-file-sharing/blob/e46852148d0960e2eb73612d4a20aef5c30c925d/Screenshot%202025-09-08%20180030.png)
  
* Step 5: Create an API Gateway \
  Name: filesharing.api \
  Create two resources: /files with POST and GET methods. \
  For each method, configure Lambda integration with UploadFunction and DownloadFunction respectively. 

![img](https://github.com/riyaj-2002/serverless-file-sharing/blob/e46852148d0960e2eb73612d4a20aef5c30c925d/Screenshot%202025-09-08%20180333.png)

* Step 6: Configure GET Method: \
  Method Request --> Edit --> Request validator --> Validate Query String Parameters and Headers \
  Method Request --> Edit --> Request Body --> text/plain \
  Integration Request --> Edit --> Mapping Templates --> Content Type: application/json --> Content Body: 

  ```bash
  {
    "queryStringParameters": {
        "fileName": "$input.params('fileName')"
    }
  }
  ```
  
* Step 7: Configure POST Method: \
  Integration Request --> Edit --> Mapping Templates --> Content Type: text/plain --> Content Body: \

  ```bash
  {
    "body" : "$input.body",
    "queryStringParameters" : {
        "fileName" : "$input.params('fileName')"
    }
  }
  ```
  
* Step 8: Deploy API Gateway: \
  Deploy the API to a stage (e.g., dev): \
  Click on "Actions" > "Deploy API". \
  Choose your stage (e.g., dev) and deploy. 

* Step 9: Testing \
    1. Upload a File \
       Use Postman or another HTTP client: \
       POST to https://<api-id>.execute-api.<region>.amazonaws.com/dev/files?fileName=test.txt \
       Set request body to raw and content type to text/plain. \
       Enter file content (e.g., Hello World!) and send the request.

       or

       Use CURL Utility:
       ```bash
       curl --location 'https://<api-id>.execute-api.<region>.amazonaws.com/dev/files?fileName=test.txt' \
       --header 'Content-Type: text/plain' \
       --data 'This is a serverless file sharing platform!!'
       ```

    3. Download a File \
       Use Postman or another HTTP client: \
       GET to https://<api-id>.execute-api.<region>.amazonaws.com/dev/files?fileName=test.txt \
       Verify file content is returned correctly.

       or

       Use CURL Utility
       ```bash
       curl --location 'https://<api-id>.execute-api.<region>.amazonaws.com/dev/files?fileName=test.txt'
       ```
  

