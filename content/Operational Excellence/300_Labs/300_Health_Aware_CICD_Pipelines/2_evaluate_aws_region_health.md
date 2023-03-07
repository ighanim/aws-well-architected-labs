---
title: "Evaluate AWS Region health"
date: 2020-04-24T11:16:09-04:00
chapter: false
weight: 2
pre: "<b>2. </b>"
---

In this section, you will inject AWS Region Health insights into the AWS CodePipeline workflow. In CodePipeline, you will create a new stage with a single action to asynchronously invoke a Lambda function. The function will call AWS Health `DescribeEvents` API to retrieve the list of active health incidents. Then, the function will complete the event analysis and decide whether or not it can impact the running deployment. Finally, the function will call back CodePipeline with evaluation results through either `PutJobSuccessResult` or `PutJobFailureResult` API operations.

![Overall architecture ](/Operations/300_Health_Aware_CICD_Pipelines/Images/architecture.jpeg)


#### Actions items in this section:

1. You will create an AWS Lambda function that integrates with the AWS Health API and AWS CodePipeline.
2. You will update the AWS CodePipeline workflow to call the AWS Lambda function before the **"Deploy"** stage.

### 1. Create an AWS Lambda function

Using the AWS Console, you will create an AWS Lambda function as well as a Lambda Execution Role. The function will require IAM Permissions to access the AWS Health API as well as AWS CodePipeline. 

#### 1.1. Manage Lambda permissions

First, create an IAM Role that allows the Lambda function to access the AWS Health API, callback CodePipeline and publish CloudWatch logs. 

a. In the [IAM Policy Console](https://us-east-1.console.aws.amazon.com/iam/home#/policies$new?step=edit), click on the **"JSON"** tab and then copy the following policy document:

![Create IAM policy ](/Operations/300_Health_Aware_CICD_Pipelines/Images/iam-policy-1.png)

```
{
  "Version": "2012-10-17", 
  "Statement": [
    {
      "Action": [ 
        "logs:CreateLogStream",
        "logs:CreateLogGroup",
        "logs:PutLogEvents"
      ],
      "Effect": "Allow", 
      "Resource": "*"
    },
    {
      "Action": [
        "codepipeline:PutJobSuccessResult",
        "codepipeline:PutJobFailureResult"
        ],
        "Effect": "Allow",
        "Resource": "*"
     },
     {
        "Effect": "Allow",
        "Action": "health:DescribeEvents",
        "Resource": "*"
    }
  ]
}
```



b. Click **Next: Tags** and then **Next: Review**.

c. In the **Review policy** screen set the **Name** field to `region-health-lambda-policy`. Finally, click **Create policy**

![Create IAM policy 2 ](/Operations/300_Health_Aware_CICD_Pipelines/Images/iam-policy-2.png)

d. In the [IAM Roles Console](https://us-east-1.console.aws.amazon.com/iamv2/home?region=ap-southeast-2#/roles/create?step=selectEntities), create a new role with the following specifications and then click next:

* **Trusted entity type**: AWS Service
* **Use case**: Lambda

![Create IAM role ](/Operations/300_Health_Aware_CICD_Pipelines/Images/iam-role-step1.png)

e. In the "Add permissions" step, select the policy that you created in step a and click **Next**. 

![Create IAM role and attached permissions ](/Operations/300_Health_Aware_CICD_Pipelines/Images/iam-role-step2.png)

f. Finally, set the **Role Name** field to `region-health-lambda-role` and click **"Create role"**

![Create IAM role and set the Role Name ](/Operations/300_Health_Aware_CICD_Pipelines/Images/iam-role-step3.png)

#### 1.2. Create the Lambda function

In this section, you will create a new Lambda function that queries the AWS Health API for running incidents, evaluates the relevance of detected issues and then updates AWS CodePipeline with evaluation results. 

In the [AWS Lambda console](https://ap-southeast-2.console.aws.amazon.com/lambda/home?region=ap-southeast-2#/create/function), author a new function **"from scratch"** with the following options:

* **Function name**: `region-health-evaluation`
* **Runtime**: `Python 3.9`
* **Architecture**: `x86_64`
* **Permissions**: select the "Use an existing role" option and pick the role created in step 1.1.

and then click **"Create function"**

![Create a Lambda Function ](/Operations/300_Health_Aware_CICD_Pipelines/Images/lambda-create-step01.png)

#### 1.3. Upload the Lambda code

In this section, you'll upload the Python code package to the newly created Lambda function and understand how it works, and interacts with the surrounding dependencies. 

a. To download the Lambda Package, click [here](/Operations/300_Health_Aware_CICD_Pipelines/Code/function-validate-aws-health.zip).

b. Click **"Upload from"** a **".zip file"** in the Lambda function, `region-health-evaluation` console. 

![Upload a the Lambda package ](/Operations/300_Health_Aware_CICD_Pipelines/Images/lambda-upload.png)

c. Select the .zip package downloaded in step a, and then click **"Save"**

Now, your Python code is uploaded to the Lambda function and ready for use.

#### 1.4. Understanding the evaluation code

The Lambda function evaluates whether or not a running AWS Health event may impact the deployment. In this case, the following criteria must be met to consider it as safe to deploy:

* Deployment will take place in the Sydney Region and accordingly the Lambda function will filter on the `ap-southeast-2` Region. The Lambda function expects the region parameter to be passed from the AWS Codepipeline pipeline. If the parameter is missing, the default is `us-east-1`.
* A closed event is irrelevant. The Lambda function will filter events with only the open status.
* AWS Health API can return different event types that may not be relevant, such as: Scheduled Maintenance, and Account and Billing notifications. The Lambda function will filter only “Issue” type events.

The function will call AWS Health `DescribeEvents` API to retrieve the list of active health incidents. Then, the function will complete the event analysis and decide whether or not it can impact the running deployment. Finally, the function will call back CodePipeline with evaluation results through either `PutJobSuccessResult` or `PutJobFailureResult` API operations.

The AWS Health API follows a multi-Region application architecture and has two regional endpoints in an active-passive configuration. To support active-passive DNS failover, AWS Health provides a global endpoint. 

The Python code is available on [GitHub](https://github.com/aws-samples/building-health-aware-cicd-pipelines).

## 2. Integrate CodePipeline and Lambda

In this step, you will create a new stage in the CodePipeline workflow. The stage has a single action that calls the Lambda function to evaluate the region's health. The CodePipeline workflow execution is blocked until it receives a callback from the Lambda function indicating either success or failure of the health check. 

a. In the AWS CodePipeline console, select the previously deployed workflow `health-aware-pipeline`

b. To Edit the workflow, click on the **"Edit"** button. 

![Edit CodePipeline workflow ](/Operations/300_Health_Aware_CICD_Pipelines/Images/codepipeline-edit-pipeline.png)

c. Click the **"Add stage"** button between the **"Source"** and **"Deploy"** steps. 

![CodePipeline add stage ](/Operations/300_Health_Aware_CICD_Pipelines/Images/codepipeline-add-stage.png)

d. Name the new stage `evaluate-region-health` and then save. 

![CodePipeline stage name ](/Operations/300_Health_Aware_CICD_Pipelines/Images/codepipeline-stage-name.png)

e. In the newly added stage, click **"Add action group"** and fill in the following option values:

![CodePipeline add action group ](/Operations/300_Health_Aware_CICD_Pipelines/Images/codepipeline-add-action-group.png)

* **Action name**: `lambda-health-check`
* **Action provider**: Invoke/AWS Lambda
* **Region**: Asia Pacific (Sydney)
* **Input artifacts**: `health-demo-file`
* **Function name**: `region-health-evaluation`
* **User parameters**: `ap-southeast-2`

Click **"Done"** to save. 

![CodePipeline edit action ](/Operations/300_Health_Aware_CICD_Pipelines/Images/codepipeline-edit-action.png)

f. To finalise, click the **"Done"** button in the in the `evaluate-region-health` section. Next, click the **"Save"** button in the `health-aware-pipeline` section. 

![CodePipeline edit final ](/Operations/300_Health_Aware_CICD_Pipelines/Images/codepipeline-edit-final.png)

g. A pop-up window will show informing you with further implicit changes related to AWS CloudWatch events and AWS CloudTrail, click **"Save"**.

![CodePipeline edit final ](/Operations/300_Health_Aware_CICD_Pipelines/Images/codepipeline-save-pipeline-changes.png)

## Congratulations! 

You have now created a Lambda function with the right permissions with logic that evaluates the region health. You've also updated the CodePipeline workflow to integrate with the newly created Lambda function to decide whether to proceed with or block the execution. 

Click on **Next Step** to continue to the next section.

{{< prev_next_button link_prev_url="../1_deploy_sample_cicd_pipeline/" link_next_url="../3_simulate_aws_region_issue/" />}}


