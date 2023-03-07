---
title: "Deploy sample CI/CD pipeline"
date: 2020-04-24T11:16:09-04:00
chapter: false
weight: 1
pre: "<b>1. </b>"
---

In this section, you will prepare a sample CI/CD pipeline. The CI/CD pipeline is based on [AWS CodePipeline](https://aws.amazon.com/codepipeline/). The workflow downloads a file from S3, evaluates an AWS region health and then re-uploads the same file under a different S3 prefix.  

The CodePipeline flow consists of three steps:

1. Source stage that downloads the file `health-demo-file.txt` from an S3 bucket.
2. Custom stage that invokes the AWS Lambda function to evaluate the AWS Health. The Lambda function calls the AWS Health API, evaluates the health risk, and calls back CodePipeline with the assessment result.
3. Deploy stage that uploads the S3 file `health-demo-file.txt` into the same source bucket under a different prefix `/output`.

![CodePipeline workflow ](/Operations/300_Health_Aware_CICD_Pipelines/Images/health-aware-cicd-pipelines-war-workflow.png)


#### Actions items in this section:

1. Deploy a CloudFormation template that create the AWS CodePipeline workflow.
2. Test the AWS CodePipeline workflow.


### 1. Create AWS CodePipeline workflow.

In this step you will provision a [CloudFormation](https://aws.amazon.com/cloudformation/) stack that builds the AWS CodePipeline workflow as well as an empty S3 bucket. In this lab, we will deploy infrastructure in the Sydney region (`ap-southeast-2`).

1. Click on the link below to deploy the stack. This will take you to the CloudFormation console in your account. Use `health-aware-pipeline` as the stack name, and **keep the default values for all options** (estimated deployment time is 2 minutes):

[Deploy CloudFormation template in ap-southeast-2](https://console.aws.amazon.com/cloudformation/home?region=ap-southeast-2#/stacks/create/review?stackName=health-aware-pipeline&templateURL=https://aws-walab-health-aware-cicd-pipelines-2023.s3.ap-southeast-2.amazonaws.com/deployment.yml)

2. Once the template is deployed, wait until the CloudFormation stack reaches the **CREATE_COMPLETE** state.

![CodePipeline workflow ](/Operations/300_Health_Aware_CICD_Pipelines/Images/cloudformation-create-complete.png)

3. In AWS CloudFormation, under the "Resources" tab you will find 3 new resources:

![Cloudformation Resources ](/Operations/300_Health_Aware_CICD_Pipelines/Images/cloudformation-resources.png)

* An AWS CodePipeline workflow named `health-aware-pipeline`
* An S3 bucket named `<STACK_NAME>-s3buckethealthdemo-<RANDOM_NUMBER>`. This is where the source object (`health-demo-file.txt`) will be uploaded.
* An IAM Role named `health-aware-pipeline-role` that will be assumed by the AWS CodePipeline workflow to call AWS services on your behalf. 

### 2. Upload the sample file to S3

To verify that the deployment in step 1 is valid, you will have to create a sample source file (`health-demo-file.txt`) and upload to the S3 bucket created in the previous step. This file can be an empty file or you may use [this sample file](/Operations/300_Health_Aware_CICD_Pipelines/Code/health-demo-file.txt).

* Open the [AWS S3 console](https://s3.console.aws.amazon.com/s3/buckets) and select the newly created bucket.
* In the **Objects** tab, click the **Upload** button.

![S3 Bucket Click Upload ](/Operations/300_Health_Aware_CICD_Pipelines/Images/s3-upload.png)

* In the **Upload** screen, click **Add files** and select `health-demo-file.txt` from your local storage. Finally, click **Upload**.

![S3 Bucket Click Add files ](/Operations/300_Health_Aware_CICD_Pipelines/Images/s3-addfiles.png)

### 3. Test the AWS CodePipeline workflow

In this step, we will validate a successful pipeline execution. Go to the [AWS CodePipeline console](https://ap-southeast-2.console.aws.amazon.com/codesuite/codepipeline/pipelines?region=ap-southeast-2), select the health aware workflow you just created and click the “Release Change” button, then click “Release” when prompted.

In this step, we will validate a successful pipeline execution. Go to the [AWS CodePipeline console](https://ap-southeast-2.console.aws.amazon.com/codesuite/codepipeline/pipelines?region=ap-southeast-2), select the workflow you just created and click the “Release Change” button, then click “Release” when prompted.

![CodePipeline release change ](/Operations/300_Health_Aware_CICD_Pipelines/Images/codepipeline-release-change.png)

If the workflow operated as designed, the "Deploy" stage will be marked as "succeeded". In addition to that, the object `health-demo-file.txt` will be now copied into the same S3 bucket under the `/output` prefix. 

![CodePipeline deployment complete ](/Operations/300_Health_Aware_CICD_Pipelines/Images/codepipeline-deployment-complete.png)

{{% notice note %}}
The pipeline expects a source file with the name `health-demo-file.txt` to be available in the source bucket. If the file doesn't exist, pipeline execution will fail. Also, it is expected that AWS CodePipeline creates input and output artefacts in the same S3 bucket for the pipeline to operate correctly. 
{{% /notice %}}

## Congratulations! 

You have now completed the first section of the Lab. An AWS CodePipeline workflow now downloads and then re-uploads an object from and into S3. In the next section, you will include AWS region health insights into the pipeline. 

Click on **Next Step** to continue to the next section.

{{< prev_next_button link_prev_url="..//" link_next_url="../2_evaluate_aws_region_health/" />}}


