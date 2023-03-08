---
title: "Tear down the lab"
date: 2020-04-24T11:16:09-04:00
chapter: false
weight: 5
pre: "<b>5. </b>"
---

The following instructions will remove the resources you've created in this lab. 

### 1. Empty the S3 bucket

{{% notice note %}}
**Note:** In order to be able to delete the CloudFormation stack in step 3, the S3 bucket must be emptied.
{{% /notice %}}

Go to the [S3 console](https://s3.console.aws.amazon.com/s3/buckets?region=ap-southeast-2&region=ap-southeast-2) and select the bucket `<STACK_NAME>-s3buckethealthdemo-<RANDOM_NUMBER>`. AWS CodePipeline requires S3 versioning in order to operate. To clean up an S3 bucket that has versioning enabled; you have to delete all object versions. To do so, turn on the **"Show versions"** toggle and delete all object versions by clicking the **Delete** button. To delete an S3 Folder, you will have to delete all objects in that folder first. 

![Delete S3 Objects ](/Operations/300_Health_Aware_CICD_Pipelines/Images/s3-cleanup.png)

Once the S3 bucket is empty, go back to the [S3 buckets console screen](https://s3.console.aws.amazon.com/s3/buckets?region=ap-southeast-2); select the S3 bucket and click **"Delete"**.

![Delete S3 Bucket ](/Operations/300_Health_Aware_CICD_Pipelines/Images/s3-delete-bucket.png)

### 2. Delete the CloudFormation stack

Go to the [AWS CloudFormation console](https://console.aws.amazon.com/cloudformation) and click on the `health-aware-pipeline`. Now, click on **Delete** and then **Delete stack**

### 3. Delete the Lambda function

Go to the [AWS Lambda console](https://console.aws.amazon.com/lambda) and select the function `region-health-evaluation`. Now, click on **Actions** and then select **Delete**

![Delete Lambda function ](/Operations/300_Health_Aware_CICD_Pipelines/Images/lambda-delete.png)

### 4. Delete the ChatBot client
Go the [AWS ChatBot console](https://us-east-2.console.aws.amazon.com/chatbot/home?region=ap-southeast-2#/chat-clients) and select the client you've created. Now, click on **Delete**. 


{{< prev_next_button link_prev_url="../4_bonus_content/"  title="Congratulations!" final_step="true" >}}
Now that you have completed the lab, if you have implemented this knowledge in your environment or workload,
you should complete a milestone in the Well-Architected tool. This lab specifically helps you with
[OPS 6: How do you mitigate deployment risks?"](https://wa.aws.amazon.com/wellarchitected/2020-07-02T19-33-23/wat.question.OPS_6.en.html)
{{< /prev_next_button >}}