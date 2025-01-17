# IAM Policies for Querying Amazon Pinpoint Analytics Data<a name="analytics-permissions"></a>

By using the Amazon Pinpoint API, you can query analytics data for a subset of standard metrics, also referred to as *key performance indicators \(KPIs\)* that apply to Amazon Pinpoint projects and campaigns\. These metrics can help you monitor and assess the performance of projects and campaigns\.

To manage access to this data, you can create AWS Identity and Access Management \(IAM\) policies that define permissions for IAM roles or users who are authorized to access the data\. To support granular control of access to this data, Amazon Pinpoint provides several distinct actions that you can specify in IAM policies\. There is a distinct action for viewing analytics data on the Amazon Pinpoint console \(`mobiletargeting:GetReports`\), and there are other actions for accessing analytics data programmatically by using the Amazon Pinpoint API\.

To create IAM policies that manage access to analytics data, you can use the AWS Management Console, the AWS CLI, or the IAM API\. Note that the **Visual editor** tab on the AWS Management Console doesn’t currently include actions for viewing or querying Amazon Pinpoint analytics data\. However, you can add the necessary actions to IAM policies manually by using the **JSON** tab on the console\.

For example, the following policy allows programmatic access to all the analytics data for all of your projects and campaigns in all AWS Regions\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "mobiletargeting:GetApplicationDateRangeKpi",
                "mobiletargeting:GetCampaignDateRangeKpi"
            ],
            "Resource": [
                "arn:aws:mobiletargeting:*:accountId:apps/*/kpis/*",
                "arn:aws:mobiletargeting:*:accountId:apps/*/campaigns/*/kpis/*"
            ]
        }
    ]
}
```

Where *accountId* is your AWS account ID\.

However, as a best practice, you should create policies that follow the principle of *least privilege*\. In other words, you should create policies that include only the permissions that are required to perform a specific task\. To support this practice and implement more granular control, you can restrict programmatic access to the analytics data for only a particular project, for example:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "mobiletargeting:GetApplicationDateRangeKpi",
                "mobiletargeting:GetCampaignDateRangeKpi"
            ],
            "Resource": [
                "arn:aws:mobiletargeting:region:accountId:apps/projectId/kpis/*",
                "arn:aws:mobiletargeting:region:accountId:apps/projectId/campaigns/*/kpis/*"
            ]
        }
    ]
}
```

Where:
+ *region* is the name of the AWS Region that hosts the project\.
+ *accountId* is your AWS account ID\.
+ *projectId* is the identifier for the project that you want to provide access to\.

Similarly, the following example policy allows programmatic access to the analytics data for only a particular campaign:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "mobiletargeting:GetCampaignDateRangeKpi",
            "Resource": "arn:aws:mobiletargeting:region:accountId:apps/projectId/campaigns/campaignId/kpis/*"
        }
    ]
}
```

Where:
+ *region* is the name of the AWS Region that hosts the project\.
+ *accountId* is your AWS account ID\.
+ *projectId* is the identifier for the project that’s associated with the campaign\.
+ *campaignId* is the identifier for the campaign that you want to provide access to\.

For a complete list of Amazon Pinpoint API actions that you can use in IAM policies, see [IAM Policies for Amazon Pinpoint Users](permissions-actions.md)\. For detailed information about creating and managing IAM policies, see the [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\.