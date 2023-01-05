---
title: "Deploy sample CI/CD pipeline"
date: 2020-04-24T11:16:09-04:00
chapter: false
weight: 1
pre: "<b>1. </b>"
---

In this section, you will prepare a sample CI/CD pipeline. The CI/CD pipeline is based on [AWS CodePipeline](https://aws.amazon.com/codepipeline/). The workflow downloads a file from S3, evaluates an AWS region health and then re-uploads the same file under a different S3 prefix.  

The CodePipeline flow consists of three steps:

1. Source stage that downloads the file `file-to-edit` from an S3 bucket.
2. Custom stage that invokes the AWS Lambda function to evaluate the AWS Health. The Lambda function calls the AWS Health API, evaluates the health risk, and calls back CodePipeline with the assessment result.
3. Deploy stage that uploads the S3 file `file-to-edit` into the same source bucket under a different prefix `/output`.

![CodePipeline workflow ](/Operations/300_Health_Aware_CICD_Pipelines/Images/health-aware-cicd-pipelines-war-workflow.png)


#### Actions items in this section:

1. Deploy a CloudFormation template that create the AWS CodePipeline workflow.
2. Test the AWS CodePipeline workflow.


### 1. Create AWS CodePipeline workflow.

In this step you will provision a [CloudFormation](https://aws.amazon.com/cloudformation/) stack that builds the AWS CodePipeline workflow as well as an empty S3 bucket. In this lab, we will deploy infrastructure in the Sydney region (`ap-southeast-2`).

1. Click on the link below to deploy the stack. This will take you to the CloudFormation console in your account. Use `health-aware-pipeline` as the stack name, and take the default values for all options:

[Deploy CloudFormation template in ap-southeast-2](https://console.aws.amazon.com/cloudformation/home?region=ap-southeast-2#/stacks/create/review?stackName=health-aware-pipeline&templateURL=https://aws-walab-health-aware-cicd-pipelines-2023.s3.ap-southeast-2.amazonaws.com/deployment.yml)

2. Once the template is deployed, wait until the CloudFormation stack reaches the **CREATE_COMPLETE** state.

![CodePipeline workflow ](/Operations/300_Health_Aware_CICD_Pipelines/Images/cloudformation-create-complete.png)

3. In AWS CloudFormation, under the "Resources" tab you will find 3 new resources:

![Cloudformation Resources ](/Operations/300_Health_Aware_CICD_Pipelines/Images/cloudformation-resources.png)

* An AWS CodePipeline workflow named `health-aware-pipeline-HealthPipelineDemo-<RANDOM_NUMBER>`
* An S3 bucket named `health-aware-pipeline-s3buckethealthdemo-<RANDOM_NUMBER>`. This is where the source object (`file-to-edit`) will be uploaded.
* An IAM Role named `health-aware-pipeline-PipelineExecutionRole-<RANDOM_NUMBER>` that will be assumed by the AWS CodePipeline workflow to call AWS services on your behalf. 

### 2. Test the AWS CodePipeline workflow

To verify that the deployment in step 1 is valid, you will have to create a sample source file (`file-to-edit`) and upload to the S3 bucket created in the previous step. Once uploaded, go to the AWS CodePipeline console and click the "Release Change" button.

![CodePipeline release change ](/Operations/300_Health_Aware_CICD_Pipelines/Images/codepipeline-release-change.png)

If the workflow operated as designed, the "Deploy" stage will be marked as "succeeded". In addition to that, the object `file-to-edit` will be now copied into the same S3 bucket under the `/output` prefix. 

![CodePipeline deployment complete ](/Operations/300_Health_Aware_CICD_Pipelines/Images/codepipeline-deployment-complete.png)


## Congratulations! 

You have now completed the first section of the Lab. An AWS CodePipeline workflow now downloads and then re-uploads an object from and into S3. In the next section, you will include AWS region health insights into the pipeline. 

Click on **Next Step** to continue to the next section.

{{< prev_next_button link_prev_url="..//" link_next_url="../2_evaluate_aws_region_health/" />}}


