---
title: "Bonus content"
date: 2020-04-24T11:16:09-04:00
chapter: false
weight: 4
pre: "<b>4. </b>"
---

In this section, we will integrate the AWS CodePipeline workflow with a ChatOps channel such as Slack. When a pipeline execution is paused, you need to notify your DevOps teams and provide sufficient information on why it was paused. 

**[Review]: Could you itemise the steps as below, so to let users easy to follow?**
#### Actions items in this section:

1. You will create a notification rule in CodePipeline.

### 1. Create a notification rule

**[Review]: Suggest to give it an example name for users to copy/paste**
In the AWS CodePipeline console, select **"Settings"** from the left menu and then select **"Notifications"** tab from the top menu. To create a new rule, click on the **"Create notification rule"** button. 

![CodePipeline settings ](/Operations/300_Health_Aware_CICD_Pipelines/Images/codepipeline-settings.png)

In the **"Create notification rule"** screen, give a name to your notification rule and under **"Detail type"** select **"Full"**.

Now, choose the correct event types that triggers the notifiction rule. In our scenario, it is the **"Failed Stage execution"**. Finally, under **"Targets"** create a new target of the type AWS ChatBot (Slack). As a prerequisite, you will need to create a Slack client in the [AWS ChatBot console](https://us-east-2.console.aws.amazon.com/chatbot/home?region=ap-southeast-2#/home). For more information, see [Configure an AWS Chatbot client for a slack channel](https://docs.aws.amazon.com/dtconsole/latest/userguide/notifications-chatbot.html#notifications-chatbot-configure-client).


![CodePipeline notifications settings ](/Operations/300_Health_Aware_CICD_Pipelines/Images/codepipeline-notification.png)

When you're done, click **"Submit"**. To test the integration, simulate a failed region health check as per the steps in section 3. If everything works as configured, you will receive a similar notification on the Slack channel:

![Slack notification ](/Operations/300_Health_Aware_CICD_Pipelines/Images/slack-notification.jpeg)

{{< prev_next_button link_prev_url="../3_simulate_aws_region_issue/" link_next_url="../5_tear_down_lab/" />}}

