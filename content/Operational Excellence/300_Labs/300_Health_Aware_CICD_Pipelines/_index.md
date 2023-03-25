---
title: "300 - Building health aware CI/CD pipelines"
## menutitle: "Lab #1"
date: 2020-04-24T11:16:08-04:00
chapter: false
weight: 1
hidden: false
---

## Authors
* **Islam Ghanim**, Senior Technical Account Manager.

## Contributors
* **Jerry Chen**, Well-Architected Solutions Architect.

## Well-Architected Best Practices
This lab helps you to exercise the following Well-Architected Best Practices in your operations change process:

* [OPS06-BP01](https://docs.aws.amazon.com/wellarchitected/latest/framework/ops_mit_deploy_risks_plan_for_unsucessful_changes.html) Plan for unsuccessful changes


## Introduction

> Everything fails all the time — Werner Vogels, AWS CTO

At the moment of imminent failure, you want to avoid an unlucky deployment. I’ll start here with a short story that demonstrates the purpose of this lab.

The DevOps team has just started a database upgrade with a planned outage of 30 minutes. The team automated the entire upgrade flow, triggered a CI/CD pipeline with no human intervention, and the upgrade is progressing smoothly. Then, 20 minutes in, the pipeline is stuck, and your upgrade isn’t progressing. The maintenance window has expired and customers can’t transact. You’ve created a support case, and the AWS engineer confirmed that the upgrade is failing because of a running AWS Health incident in the us-west-2 Region. The engineer has directed the DevOps team to continue monitoring the [status.aws.amazon.com](https://status.aws.amazon.com) page for updates regarding incident resolution. The event continued running for three hours, during which time customers couldn’t transact. Once resolved, the DevOps team retried the failed pipeline, and it completed successfully.

After the incident, the DevOps team explored the possibilities for avoiding these types of incidents in the future. The team was made aware of AWS Health API that provides programmatic access to AWS Health information. In this post, we’ll help the DevOps team make the most of the [AWS Health API](https://aws.amazon.com/health/) to proactively prevent unintended outages.

AWS provides Business and Enterprise Support customers with access to the AWS Health API. Customers can have access to running events in the AWS infrastructure that may impact their service usage. Incidents could be Regional, AZ-specific, or even account specific. During these incidents, it isn’t recommended to deploy or change services that are impacted by the event.

In this lab, I will walk you through how to embed AWS Health API insights into your CI/CD pipelines to automatically stop deployments whenever an AWS Health event is reported in a Region that you’re operating in. Furthermore, I will demonstrate how you can automate detection and remediation.

## Goals: 

* Build a solution that automatically evaluates an AWS region health with new deployments. 
* Simulate a regional health impairment and verify the solution behaviour. 
* Integrate the solution with a ChatOps channel. 


## Prerequisites:

> This lab requires access to the AWS Health API which is exclusive for [Business Support](https://aws.amazon.com/premiumsupport/plans/business/), [Enterprise On-Ramp Support](https://aws.amazon.com/premiumsupport/plans/enterprise-onramp/) and [Enterprise Support](https://aws.amazon.com/premiumsupport/plans/enterprise/) customers. For more information, see [AWS Support Plans](https://aws.amazon.com/premiumsupport/plans/)

* An [AWS account](https://portal.aws.amazon.com/gp/aws/developer/registration/index.html) that you are able to use for testing. The account should not be used for production purposes.  
**[Review] suggest to add the IAM Role as well:**
* An [IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html) or an [IAR role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) in your AWS account with full access to [CloudFormation,](https://aws.amazon.com/cloudformation/) [Amazon Lambda,](https://aws.amazon.com/lambda/)[AWS CodePipeline,](https://aws.amazon.com/codepipeline/) [AWS Identity and Access Management (IAM),](https://aws.amazon.com/iam/) 

## Costs

{{% notice note %}}
NOTE: You will be billed for any applicable AWS resources used if you complete this lab that are not covered in the [AWS Free Tier](https://aws.amazon.com/free/).
{{% /notice %}}

{{< prev_next_button link_next_url="./1_deploy_sample_cicd_pipeline/" button_next_text="Start Lab" first_step="true" />}}

Steps:
{{% children  /%}}
