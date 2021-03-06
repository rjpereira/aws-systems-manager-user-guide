# Working with parameter versions<a name="sysman-paramstore-versions"></a>

Each time you edit the value of a parameter, Parameter Store creates a new *version* of the parameter and retains the previous versions\. When you initially create a parameter, Parameter Store assigns version `1` to that parameter\. When you change the value of the parameter, Parameter Store automatically iterates the version number by one\. You can view the details, including the values, of all versions in a parameter's history\. 

You can also specify the version of a parameter to use in API commands and SSM documents; for example: `ssm:MyParameter:3`\. You can specify a parameter name and a specific version number in API calls and SSM documents\. If you don't specify a version number, the system automatically uses the latest version\. 

You can use parameter versions to see how many times a parameter changed over a period of time\. Parameter versions also provide a layer of protection if a parameter value is accidentally changed\. 

You can create a maximum of 100 versions of a parameter\.

The following procedures show you how to edit a parameter and then verify that a new version was created\.

**Topics**
+ [Create a new version of a parameter \(console\)](#sysman-paramstore-version-console)
+ [Reference a parameter version](#reference-parameter-version)

## Create a new version of a parameter \(console\)<a name="sysman-paramstore-version-console"></a>

You can use the AWS Systems Manager console to create a new version of a parameter and view the version history of a parameter\.

**To create a new version of a parameter**

1. Open the AWS Systems Manager console at [https://console\.aws\.amazon\.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/)\.

1. In the navigation pane, choose **Parameter Store**\.

   \-or\-

   If the AWS Systems Manager home page opens first, choose the menu icon \(![\[Image NOT FOUND\]](http://docs.aws.amazon.com/systems-manager/latest/userguide/images/menu-icon-small.png)\) to open the navigation pane, and then choose **Parameter Store**\.

1. Choose the name of a parameter that you created earlier\. For information about creating a new parameter, see [Creating Systems Manager parameters](sysman-paramstore-su-create.md)\. 

1. Choose **Edit**\.

1. In the **Value** box, type a new value, and then choose **Save changes**\.

1. Choose the name of the parameter you just updated\. On the **Overview** tab, verify that the version number incremented by 1, and verify the new value\.

1. To view the history of all versions of a parameter, choose the **History** tab\. 

## Reference a parameter version<a name="reference-parameter-version"></a>

You can reference specific parameter versions in commands, API calls, and SSM documents by using the following format: ssm: `parameter-name:version-number`\. 

 In the following example, version 3 of the parameter `default-instance-type` is used in the EC2 `run-instances command`\. 

```
aws ec2 run-instances \
    --image-id resolve:ssm:ps-resolve-test:3 \
    --count 1 \
    --instance-type c3.large \
    --key-name marbak-us-east-2 \
    --security-groups default
```

**Note**  
Currently, using `resolve` and a parameter value works only with the `--instance-id` option and a parameter that contains an Amazon Machine Image \(AMI\) as its value\. For more information, see [Native parameter support for Amazon Machine Image IDs](parameter-store-ec2-aliases.md)\.

Here is an example for specifying version 2 of a parameter named `MyRunCommandParameter` in an SSM document\.

```
{
    "schemaVersion": "2.2",
    "description": "Run a shell script or specify the commands to run.",
    "parameters": {
        "commands": {
            "type": "String",
            "description": "(Required) Specify a shell script or a command to run.",
            "displayType": "textarea",
            "default": "{{ssm:MyRunCommandParameter:2}}"
        }
    },
    "mainSteps": [
        {
            "action": "aws:runShellScript",
            "name": "RunScript",
            "inputs": {
                "runCommand": [
                    "{{commands}}"
                ]
            }
        }
    ]
}
```