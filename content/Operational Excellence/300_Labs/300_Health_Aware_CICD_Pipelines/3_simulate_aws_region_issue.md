---
title: "Simulate an AWS Region issue"
date: 2020-04-24T11:16:09-04:00
chapter: false
weight: 3
pre: "<b>3. </b>"
---

In this section, you will simulate a successful and a failed AWS Region health evaluation. 

#### Action items in this section:

1. Test the AWS CodePipeline workflow to simulate a successful AWS Region Health check.
2. Test the AWS CodePipeline workflow to simulate a failed AWS Region Health check. 

{{% notice note %}}
**Note:** This section assumes there are no running AWS Health events at the time you're running this lab. If there is a running event at the time, the output of this section will be opposite to what it is intended to be. For more information on AWS Region health, visit [health.aws.amazon.com](https://health.aws.amazon.com/)
{{% /notice %}}

### 1. Simulate a successful AWS Region Health check

To simulate a successful health evaluation, go to the CodePipeline console and click the **"Release change"** button. 

![CodePipeline release change ](/Operations/300_Health_Aware_CICD_Pipelines/Images/codepipeline-release-change.png)


If the workflow operated as designed, "Source", "evaluate-region-health" and "Deploy" stages will be marked as "succeeded". In addition to that, the object `health-demo-file.txt` will be now copied into the same S3 bucket under the `/output` prefix.

![CodePipeline release change ](/Operations/300_Health_Aware_CICD_Pipelines/Images/codepipeline-deployment-complete-with-lambda.png)

### 2. Simulate a failed AWS Region Health check

To simulate a failed health evaluation, you will introduce a modification to `lambda_function.py` in the Lambda Code source editor. Replace the lines from 35 to 42 with the following:

```
    job = event["CodePipeline.job"]["id"]
    if(response["events"] == []):
        incidentInProgress = False
        #code_pipeline.put_job_success_result(jobId=job)
        code_pipeline.put_job_failure_result(jobId=job, failureDetails={'message': 'Incident In Progress', 'type': 'JobFailed'})
    else:
        incidentInProgress = True
        code_pipeline.put_job_success_result(jobId=job)
        #code_pipeline.put_job_failure_result(jobId=job, failureDetails={'message': 'Incident In Progress', 'type': 'JobFailed'})
```

This change returns a failed evaluation result to CodePipeline in the situation of no health events being detected. Once done, click the "Deploy" button to update Lambda with your recent change. 

Now, go to the CodePipeline console and click the **"Release change"** button. 

![CodePipeline release change ](/Operations/300_Health_Aware_CICD_Pipelines/Images/codepipeline-release-change.png)

If the workflow operated as designed, "Source" stage will be marked as succeeded while the following "evaluate-region-health" stage will be marked as "Failed". The workflow execution will be blocked and the "Deploy" stage won't be triggered. 

![CodePipeline failed execution ](/Operations/300_Health_Aware_CICD_Pipelines/Images/codepipeline-deployment-failed-with-lambda.png)

Now, let's revert the Lambda to the original state; simulating that the previously running health event is now cleared. In `source_code.py`, replace the lines from 35 to 43 with the following and click **"Deploy"**:

```
    job = event["CodePipeline.job"]["id"]
    if(response["events"] == []):
        incidentInProgress = False
        code_pipeline.put_job_success_result(jobId=job)
        'Incident In Progress', 'type': 'JobFailed'})
    else:
        incidentInProgress = True
        code_pipeline.put_job_failure_result(jobId=job, failureDetails={'message': 'Incident In Progress', 'type': 'JobFailed'})
```

Now, go to the CodePipeline console and click the **"Retry"** button in the "evaluate-region-health" stage. 

![CodePipeline release change ](/Operations/300_Health_Aware_CICD_Pipelines/Images/codepipeline-retry.png)

The stage status will be change to **"In progress"** and then shortly changes to **"Succeeded"**. Once complete, The **"Deploy"** stage will be marked **"Succeeded"** as well and execution is complete. 

{{< prev_next_button link_prev_url="../2_evaluate_aws_region_health/" link_next_url="../4_bonus_content/" />}}

