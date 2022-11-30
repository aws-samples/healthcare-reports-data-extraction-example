## Automated Deployment of AWS Resources for healthcare reports data extraction solution Using AWS CloudFormation

This repository contains the sample CloudFormation template for the AWS  blog post - Automate email attachment storage and processing in healthcare. The template provisions required AWS Services ( Lambda functions, DynamoDB Tabes, SNS Topics, S3 buckets, SES) for the solution. 

## Solution Architecture

![image](https://user-images.githubusercontent.com/111926227/204926030-60dc8484-3cc1-44eb-9007-243c9aa4f1fb.png)

The following section describes the architecture components in the above architecture and how each service functions within the solution. 

1.	Emails with attachments sent to healthcare facilities are received by Amazon SES and stored in Amazon S3 in their raw format
2.	Amazon SES is configured to create a notification to an Amazon SNS topic when an email is received. Amazon SNS is setup to trigger an AWS Lambda function. 
3.	The Lambda function performs three main activities
-	processes the raw text of emails received, extracts and saves attachments in another Amazon S3 bucket
-	stores metadata about the emails received in Amazon DynamoDB 
-	Calls Amazon Textract  Asynchronous API to process the attachments 
4.	The call to Amazon Textract is made asynchronously so that Lambda does not have to wait till the result of the operation to complete processing. This is especially useful for processing multipage documents which may take time, thereby avoiding Lambda timeout issues. The response to the Asynchronous operation is a job identifier (JobId). Amazon Textract asynchronous API is configured to send a notification to Amazon SNS when the processing of attachments is completed.
5.	The notification to Amazon SNS triggers an AWS Lambda function that uses the JobId to retrieve processed content from Amazon Textract and save the results to Amazon S3.
6.	Finally, an AWS Lambda function is used to process the results from Amazon S3 Bucket and upload the data (raw data, Key/value pairs and table values) to Amazon DynamoDB. Applications in Healthcare facilities can use this data to extract required and relevant information and submit this information to public health agencies.


## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

