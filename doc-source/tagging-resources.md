# Tagging Amazon Pinpoint Resources<a name="tagging-resources"></a>

A *tag* is a label that you optionally define and associate with projects \(applications\), campaigns, message templates, and segments in Amazon Pinpoint\. Tags can help you categorize and manage these types of resources in different ways, such as by purpose, owner, environment, or other criteria\. For example, you can use tags to apply policies or automation, or to identify resources that are subject to certain compliance requirements\. A resource can have as many as 50 tags\.

**Topics**
+ [Managing Tags](#tags-manage)
+ [Adding Tags to Resources](#tags-add)
+ [Displaying Tags for Resources](#tags-display)
+ [Updating Tags for Resources](#tags-update)
+ [Removing Tags from Resources](#tags-remove)
+ [Related Information](#tags-related-information)

## Managing Tags<a name="tags-manage"></a>

Each tag consists of a required *tag key* and an optional *tag value*, both of which you define\. A tag key is a general label that acts as a category for more specific tag values\. A tag value acts as a descriptor for a tag key\. For example, if you have two versions of an Amazon Pinpoint project, one for internal testing and another for external use, you might assign a `Stack` tag key to both projects\. The value of the `Stack` tag key might be `Test` for one project and `Production` for the other project\.

A tag key can contain as many as 128 characters\. A tag value can contain as many as 256 characters\. The characters can be Unicode letters, digits, white space, or one of the following symbols: \_ \. : / = \+ \-\. The following additional restrictions apply to tags:
+ Tag keys and values are case sensitive\.
+ For each associated resource, each tag key must be unique and it can have only one value\.
+ The `aws:` prefix is reserved for use by AWS; you can’t use it in any tag keys or values that you define\. In addition, you can't edit or remove tag keys or values that use this prefix\. Tags that use this prefix don’t count against the limit of 50 tags per resource\.
+ You can't update or delete a resource based only on its tags\. You must also specify the Amazon Resource Name \(ARN\) or resource ID, depending on the operation that you use\.
+ You can associate tags with public or shared resources, but the tags are available only for your AWS account, not any other accounts that share the resource\. In addition, the tags are available only for resources that are located in the specified AWS Region for your AWS account\.

To add, display, update, and remove tag keys and values from Amazon Pinpoint resources, you can use the AWS Command Line Interface \(AWS CLI\), the Amazon Pinpoint API, the AWS Resource Groups Tagging API, or an AWS SDK\. To manage tag keys and values across all the AWS resources that are located in the specified AWS Region for your AWS account, including Amazon Pinpoint resources, you can use the AWS Resource Groups Tagging API\.

After you start implementing tags, you can apply tag\-based, resource\-level permissions to AWS Identity and Access Management \(IAM\) policies and API operations, including operations that support the addition of tags to resources when resources are created\. This can help you implement granular control of which groups and users in your AWS account have permission to create and tag resources, and which groups and users have permission to create, update, and remove tags more generally\. For example, you might create a policy that allows users to have full access to the Amazon Pinpoint resources that they’ve tagged:

```
{ 
   "Action": "mobiletargeting:*",
   "Effect": "Allow", 
   "Resource": "*", 
   "Condition": { 
      "StringEqualsIgnoreCase": {
         "aws:ResourceTag/Owner":"${aws:username}"
      } 
   } 
}
```

If you define tag\-based, resource\-level permissions, the permissions take effect immediately\. This means that your resources are more secure as soon as they are created and you can quickly start enforcing the use of tags for new resources\. You can also use resource\-level permissions to control which tag keys and values can be associated with new and existing resources\. For more information, see [Controlling Access Using Tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_tags.html) in the *AWS IAM User Guide* and [IAM Policies for Amazon Pinpoint Users](permissions-actions.md) in this guide\.

## Adding Tags to Resources<a name="tags-add"></a>

The following examples show you how to add a tag to an Amazon Pinpoint project \(application\), campaign, message template, or segment by using the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide//) and the [Amazon Pinpoint API](https://docs.aws.amazon.com/pinpoint/latest/apireference//)\.

To add a tag to multiple Amazon Pinpoint resources in a single operation, use the resource groups tagging operations of the AWS CLI or the [AWS Resource Groups Tagging API](https://docs.aws.amazon.com/resourcegroupstagging/latest/APIReference/Welcome.html)\.

------
#### [ AWS CLI ]

You can use Amazon Pinpoint by running commands with the AWS CLI\.

To add a tag to an existing resource, use the `tag-resource` command:

```
$ aws pinpoint tag-resource \
> --resource-arn resource-arn \
> --tags-model '{"tags": {"key": "value"}}'
```

Where:
+ *resource\-arn* is the Amazon Resource Name \(ARN\) of the resource that you want to add a tag to\. To get a list of ARNs for Amazon Pinpoint resources, you can [display a list of all Amazon Pinpoint resources that have tags](#tags-display)\.
+ *key* is the tag key that you want to add to the resource\. The `key` argument is required\.
+ *value* is the optional tag value that you want to add for the specified tag key \(`key`\)\. The `value` argument is required\. If you don’t want the resource to have a specific tag value, don’t specify a value for the `value` argument\. Amazon Pinpoint will set the value to an empty string\.

To create a new resource and add a tag to it, use the appropriate `create` command for the resource and include the `tags` parameter\. For example, the following command creates a new project named `MyApp` and adds a `Stack` tag key with a `Test` tag value to the project:

```
$ aws pinpoint create-app \
> --create-application-request '[
       {"Name": "MyApp"},
       {"tags": {"Stack": "Test"}}
]'
```

For information about the commands that you can use to create an Amazon Pinpoint resource, see the [AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/)\.

------
#### [ HTTP ]

You can use Amazon Pinpoint by sending HTTP\(S\) requests directly to the Amazon Pinpoint API\.

To create a new resource and add a tag to it, send a POST request to the appropriate resource URI and include the `tags` parameter and attributes in the body of the request\. For example, the following request creates a new project named `MyApp` and adds a `Stack` tag key with a `Test` tag value to the project:

```
POST /v1/apps HTTP/1.1
Host: pinpoint.us-east-1.amazonaws.com
Content-Type: application/x-www-form-urlencoded
Accept: application/json
Cache-Control: no-cache

{
   "Name": "MyApp",
   "tags": {
      "Stack": "Test"
   }
}
```

To add a tag to an existing resource, send a POST request to the [Tags](https://docs.aws.amazon.com/pinpoint/latest/apireference/rest-api-tags.html) URI, including the Amazon Resource Name \(ARN\) of the resource\. For example, the following request adds a `Stack` tag key with a `Test` tag value to the specified project \(*resource\-arn*\):

```
POST /v1/tags/resource-arn HTTP/1.1
Host: pinpoint.us-east-1.amazonaws.com
Content-Type: application/json
Accept: application/json
Cache-Control: no-cache

{
      "tags": {
      "Stack": "Test"
   }
}
```

Alternatively, you can add a tag to an existing campaign or segment by sending a PUT request to the [Campaign](https://docs.aws.amazon.com/pinpoint/latest/apireference/rest-api-campaign.html) URI or the [Segment](https://docs.aws.amazon.com/pinpoint/latest/apireference/rest-api-segment.html) URI, respectively, and including the `tags` parameter and attributes in the body of the request\. For example, the following request adds a `Stack` tag key with a `Test` tag value to the specified campaign:

```
PUT /v1/apps/application-id/campaigns/campaign-id HTTP/1.1
Host: pinpoint.us-east-1.amazonaws.com
Content-Type: application/x-www-form-urlencoded
Accept: application/json
Cache-Control: no-cache

{
   "tags": {
      "Stack": "Test"
   }
}
```

Where:
+ *application\-id* is the ID of the project that contains the campaign\.
+ *campaign\-id* is the ID of the campaign\.

------

## Displaying Tags for Resources<a name="tags-display"></a>

The following examples show you how to use the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide//) and the [Amazon Pinpoint API](https://docs.aws.amazon.com/pinpoint/latest/apireference//) to display a list of the tags that are associated with an Amazon Pinpoint project \(application\), campaign, message template, or segment\.

------
#### [ AWS CLI ]

You can use Amazon Pinpoint by running commands with the AWS CLI\.

To display a list of all the tags that are associated with a resource, use the `list-tags-for-resource` command:

```
$ aws pinpoint list-tags-for-resource \
> --resource-arn resource-arn \
```

Where *resource\-arn* is the Amazon Resource Name \(ARN\) of the resource\.

To display a list of all the Amazon Pinpoint resources that have tags and all the tags that are associated with each of those resources, use the `get-resources` command and set the `resource-type-filters` parameter to `mobiletargeting`, for example:

```
$ aws resourcegroupstaggingapi get-resources \
> --resource-type-filters "mobiletargeting"
```

The output of the command is a list of ARNs for all the Amazon Pinpoint resources that have tags\. The list includes all the tag keys and values that are associated with each resource\.

------
#### [ HTTP ]

You can use Amazon Pinpoint by sending HTTP\(S\) requests directly to the Amazon Pinpoint API\.

To display all the tags that are associated with a specific resource, send a GET request to the [Tags](https://docs.aws.amazon.com/pinpoint/latest/apireference/rest-api-tags.html) URI, including the Amazon Resource Name \(ARN\) of the resource\. For example, the following request gets all tag information for the specified campaign \(*resource\-arn*\):

```
GET /v1/tags/resource-arn HTTP/1.1
Host: pinpoint.us-east-1.amazonaws.com
Content-Type: application/json
Accept: application/json
Cache-Control: no-cache
```

In the JSON response to the preceding request, the `tags` attribute lists all the tag keys and values that are associated with the campaign\.

To display all the tags that are associated with more than one resource of the same type \(project, campaign, message template, or segment\), send a GET request to the appropriate resource URI\. For example, the following request gets information about all the campaigns in the specified project \(*application\-id*\):

```
GET /v1/apps/application-id/campaigns HTTP/1.1
Host: pinpoint.us-east-1.amazonaws.com
Content-Type: application/json
Accept: application/json
Cache-Control: no-cache
```

The JSON response to the preceding request lists all the campaigns in the project\. The `tags` attribute of each campaign lists all the tag keys and values that are associated with the campaign\.

------

## Updating Tags for Resources<a name="tags-update"></a>

There are several ways to update \(overwrite\) a tag for an Amazon Pinpoint resource\. The best way to update a tag depends on:
+ Whether you want to update a tag key, a tag value, or both\.
+ The type of resource that you want to update tags for\.
+ Whether you want to update a tag for one resource or multiple resources at the same time\.

To update a tag key for a resource, you can [remove the current tag](#tags-remove) and [add a new tag](#tags-add) by using the AWS CLI or the Amazon Pinpoint API\.

To update only the tag value of a tag key for a resource, you can use the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide//) or the [Amazon Pinpoint API](https://docs.aws.amazon.com/pinpoint/latest/apireference//)\. The examples in this section show you how\.

To update a tag key or value for multiple resources at the same time, you can use the resource groups tagging operations of the AWS CLI or the [AWS Resource Groups Tagging API](https://docs.aws.amazon.com/resourcegroupstagging/latest/APIReference/Welcome.html)\.

------
#### [ AWS CLI ]

You can use Amazon Pinpoint by running commands with the AWS CLI\.

To update a tag value for a resource by using a resource ID, use the appropriate `update` or `write request` command for the resource type and include the `tags` parameter and arguments\. For example, the following command changes the tag value from `Test` to `Production` for the `Stack` tag key that's associated with the specified campaign:

```
$ aws pinpoint update-campaign \
> --application-id application-id \
> --campaign-id campaign-id \
> --write-campaign-request '{"tags": {"Stack": "Production"}}'
```

Where:
+ *application\-id* is the ID of the project that contains the campaign\.
+ *campaign\-id* is the ID of the campaign whose tags you want to update\.

To update a tag value for a resource by using an Amazon Resource Name \(ARN\), use the `tag-resource` command and include the `tags-model` parameter and arguments:

```
$ aws pinpoint tag-resource \
> --resource-arn resource-arn \
> --tags-model '{"tags": {"key": "value"}}'
```

Where:
+ *resource\-arn* is the ARN of the resource whose tags you want to update\. To get a list of ARNs for Amazon Pinpoint resources, you can [display a list of all Amazon Pinpoint resources that have tags](#tags-display)\.
+ *key* is the tag key\. The `key` argument is required\.
+ *value* is the tag value that you want to use for the specified tag key \(`key`\)\. The `value` argument is required\. To remove the tag value, don’t specify a value for the `value` argument\. Amazon Pinpoint will set the value to an empty string\.

For more information about the commands that you can use to update an Amazon Pinpoint resource, see the [AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/)\.

------
#### [ HTTP ]

You can use Amazon Pinpoint by sending HTTP\(S\) requests directly to the Amazon Pinpoint API\.

To update a tag value for a project, use the `TagResources` action of the [AWS Resource Groups Tagging API](https://docs.aws.amazon.com/resourcegroupstagging/latest/APIReference/Welcome.html)\. The Amazon Pinpoint API currently doesn’t support updates to tags for projects\.

To update a tag value for a campaign, message template, or segment, send a PUT request to the appropriate resource URI of the Amazon Pinpoint API\. In the body of the request, include the `tags` parameter and attributes\. In the `tags` parameter, specify the corresponding tag key for the `tag` attribute and, for the `tagvalue` attribute, do one of the following:
+ To use a new value, specify the value\.
+ To remove the value, don’t specify a value\. Amazon Pinpoint will set the tag value to an empty string\.

For example, the following request changes the tag value of the `Stack` tag key from `Test` to `Production` for the specified campaign:

```
PUT /v1/apps/application-id/campaigns/campaign-id HTTP/1.1
Host: pinpoint.us-east-1.amazonaws.com
Content-Type: application/json
Accept: application/json
Cache-Control: no-cache

{
    "tags": { 
        "Stack": "Production"
    }
}
```

Where:
+ *application\-id* is the ID of the project that contains the campaign\.
+ *campaign\-id* is the ID of the campaign whose tags you want to update\.

------

## Removing Tags from Resources<a name="tags-remove"></a>

The following examples show you how to remove a tag from an Amazon Pinpoint project \(application\), campaign, message template, or segment by using the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide//) and the [Amazon Pinpoint API](https://docs.aws.amazon.com/pinpoint/latest/apireference//)\.

To remove a tag from multiple Amazon Pinpoint resources in a single operation, use the resource groups tagging operations of the AWS CLI or the [AWS Resource Groups Tagging API](https://docs.aws.amazon.com/resourcegroupstagging/latest/APIReference/Welcome.html)\.

------
#### [ AWS CLI ]

You can use Amazon Pinpoint by running commands with the AWS CLI\.

To remove a tag, both the tag key and its associated value, from a resource, use the `untag-resource` command and include the `tag-keys` parameter and argument:

```
$ aws pinpoint untag-resource \
> --resource-arn resource-arn \
> --tag-keys 'key'
```

Where:
+ *resource\-arn* is the Amazon Resource Name \(ARN\) of the resource that you want to remove a tag from\. To get a list of ARNs for Amazon Pinpoint resources, you can [display a list of all Amazon Pinpoint resources that have tags](#tags-display)\.
+ *key* is the tag that you want to remove from the resource\. The `key` argument is required\.

To remove multiple tags, both tag keys and their associated values, from a resource, use the `untag-resource` command and include the `tag-keys` parameter and arguments:

```
$ aws pinpoint untag-resource \
> --resource-arn resource-arn \
> --tag-keys '["key1","key2" ...]'
```

Where:
+ *resource\-arn* is the ARN of the resource that you want to remove tags from\.
+ *key\#* is each tag that you want to remove from the resource\.

To remove only a specific tag value, not a tag key, from a resource, you can [update the tags for the resource](#tags-update)\.

------
#### [ HTTP ]

You can use Amazon Pinpoint by sending HTTP\(S\) requests directly to the Amazon Pinpoint API\.

To remove a tag, both the tag key and its associated value, from a resource, send a DELETE request to the [Tags](https://docs.aws.amazon.com/pinpoint/latest/apireference/rest-api-tags.html) URI\. In the URI, append a query string that includes the Amazon Resource Name \(ARN\) of the resource that you want to remove a tag from, followed by the `tagKeys` parameter and the tag to remove\. For example:

```
https://endpoint/v1/tags/resource-arn?tagKeys=key
```

 Where:
+ *endpoint* is the Amazon Pinpoint endpoint for the AWS Region that hosts the resource\.
+ *resource\-arn* is the ARN of the resource that you want to remove a tag from\.
+ *key* is the tag that you want to remove from the resource\.

All the parameters should be URL encoded\.

To remove multiple tags, both tag keys and their associated values, from a resource, append the `tagKeys` parameter and argument for each additional tag to remove, separated by an ampersand \(&\)\. For example: 

```
https://endpoint/v1/tags/resource-arn?tagKeys=key1&tagKeys=key2
```

To remove only a specific tag value, not a tag key, from a tag for a resource, you can [update the tags for the resource](#tags-update)\.

------

## Related Information<a name="tags-related-information"></a>

For more information about the CLI commands that you can use to manage Amazon Pinpoint resources, see the Amazon Pinpoint section of the [AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/)\.

For more information about resources in the Amazon Pinpoint API, including supported HTTP\(S\) methods, parameters, and schemas, see the [Amazon Pinpoint API Reference](https://docs.aws.amazon.com/pinpoint/latest/apireference/)\.