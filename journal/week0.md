# Week 0 — Billing and Architecture

## Required Homework/Tasks

---

1. Create AWS Budget
1. Installation and Setup of AWS CLI
1. Recreate Logical Architectural Deisgn
1. Conceptual Achitectural Diagram or Napkin Design
1. Generating AWS Credentials

----

## Recreate Logical Architectural Deisgn

#### LUCID allowed me to recreate the amazing cruddur app architectural diagram. I would like to introduce you to Lucid before I show you my diagram.  <br> <br>Lucidchart is a web-based diagramming application that allows users to visually collaborate on drawing, revising and sharing charts and diagrams, and improve processes, systems, and organizational structures. It is produced by Lucid Software Inc.

### my Cruddur Achitectural Diagram Remake
![Screenshot 2023-02-16 at 00 54 12](https://user-images.githubusercontent.com/112965272/219223662-2ad73c33-712b-4d4b-82c0-3483dba21360.png "Cruddur Achitectural Diagram Remake")

### find the link to my diagram👇below.

[My Cruddur Achitectural Diagram Remake](https://lucid.app/lucidchart/635a50ae-73c1-4bc4-aab4-655e4b626d24/edit?viewport_loc=-733%2C-1376%2C2879%2C1284%2C0_0&invitationId=inv_6c508643-34c4-4ca2-b0ec-d1b6acb1d313)


---

## Installation and Setup of AWS CLI

#### The AWS Command Line Interface (AWS CLI) is a unified tool to manage your AWS services. With just one tool to download and configure, you can control multiple AWS services from the command line and automate them through scripts.

#### I was able to install the aws CLI and set it using my AWS Credentions on gitpod as well as well as my local terminal. <br> In order to prove that I am able to use the AWS CLI. I am providing the instructions I used for my configuration of my local machine on Mac and Gitpod.

#### I took the following steps to install the AWS CLI by following the [Aws CLI Install Documentation Page](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

1.Opened my terminal and type in the command `curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg" /`  <br> <br> The -o option specifies the file name that the downloaded package is written to. In this example, the file is written to AWSCLIV2.pkg in the current folder. <br>

2.install the download AWS cli pacgae by entering the command in my terminal `sudo installer -pkg AWSCLIV2.pkg -target`
<br>

<img width="986" alt="Screenshot 2023-02-16 at 01 43 37" src="https://user-images.githubusercontent.com/112965272/219228845-5b8fff3e-c7df-4da8-ad89-e4a7b5021184.png">

### Create a new User and Generate AWS Credentials

- Go to (IAM Users Console](https://us-east-1.console.aws.amazon.com/iamv2/home?region=us-east-1#/users) Anthony create a new user
- `Enable console access` for the user
- Create a new `Admin` Group and apply `AdministratorAccess`
- Create the user and go find and click into the user
- Click on `Security Credentials` and `Create Access Key`
- Choose AWS CLI Access
- Download the CSV with the credentials

### Set Env Vars

We will set these credentials for the current bash terminal
```
export AWS_ACCESS_KEY_ID=""
export AWS_SECRET_ACCESS_KEY=""
export AWS_DEFAULT_REGION=us-east-1
```

We'll tell Gitpod to remember these credentials if we relaunch our workspaces
```
gp env AWS_ACCESS_KEY_ID=""
gp env AWS_SECRET_ACCESS_KEY=""
gp env AWS_DEFAULT_REGION=us-east-1
```

### Check that the AWS CLI is working and you are the expected user

```sh
aws sts get-caller-identity
```

3. After installation is complete, debug logs are written to /var/log/install.log.

To verify that the shell can find and run the aws command in your $PATH, use the following commands. 

`$ which aws` <br>
`/usr/local/bin/aws `
<br>
``` aws --version

```
<br>
`aws-cli/2.10.0 Python/3.9.11 Darwin/22.3.0 exe/x86_64 prompt/off `

<img width="875" alt="Screenshot 2023-02-16 at 02 00 27" src="https://user-images.githubusercontent.com/112965272/219230995-371fe39a-c0b6-4313-85a2-9c67aa5a2d30.png">

### Gitpod Installation

#### On Gitpod, you can follow the above installation methon with the exception of adding your credential via an environmental variable which is loadded to the enviroment each time gitpod is opened. This was achieved by pasting the following YAML syntax code into the gitpod.yaml file


```yaml
tasks:
  - name: aws-cli
    env:
      AWS_CLI_AUTO_PROMPT: on-partial
    init: |
      cd /workspace
      curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
      unzip awscliv2.zip
      sudo ./aws/install
      cd $THEIA_WORKSPACE_ROOT
vscode:
  extensions:
    - 42Crunch.vscode-openapi
```

 #### I tested that my cli has been configured properly by typinbg in the following command
 
 ```bash
 aws sts get-caller-identity
 ```
  
![Screenshot 2023-02-16 at 03 00 14](https://user-images.githubusercontent.com/112965272/219249049-240d38f0-0b04-41d9-a3a9-1367cb3c508e.png)


-------

### Aws Budget <br>

#### The AWS Budget service is another one I learned about this week. For cloud users, it is a very relevant service.<br> Using AWS budgets, you can set custom cost and usage thresholds and receive alerts when your thresholds are exceeded (or forecast to exceed). 

#### The AWS Budget can be setup using the AWS management console, cloudshell and gitpod terminal

1. step 1 : Create a Json file with the json Script below

```json
{
    "BudgetLimit": {
        "Amount": "10",
        "Unit": "USD"
    },
    "BudgetName": "AWS_bootCamp",
    "BudgetType": "COST",
    "CostFilters": {
        "TagKeyValue": [
            "user:Key$value1",
            "user:Key$value2"
        ]
    },
    "CostTypes": {
        "IncludeCredit": true,
        "IncludeDiscount": true,
        "IncludeOtherSubscription": true,
        "IncludeRecurring": true,
        "IncludeRefund": true,
        "IncludeSubscription": true,
        "IncludeSupport": true,
        "IncludeTax": true,
        "IncludeUpfront": true,
        "UseBlended": false
    },
    "TimePeriod": {
        "Start": 1477958399,
        "End": 3706473600
    },
    "TimeUnit": "MONTHLY"
}
````

2. ## Creating a Billing Alarm

### Create SNS Topic

- We need an SNS topic before we create an alarm.
- The SNS topic is what will delivery us an alert when we get overbilled
- [aws sns create-topic](https://docs.aws.amazon.com/cli/latest/reference/sns/create-topic.html)

We'll create a SNS Topic
```sh
aws sns create-topic --name billing-alarm
```
which will return a TopicARN

We'll create a subscription supply the TopicARN and our Email
```sh
aws sns subscribe \
    --topic-arn TopicARN \
    --protocol email \
    --notification-endpoint your@email.com
```

Check your email and confirm the subscription

3. Create the alarm script for the notification alert.

```json
[
    {
        "Notification": {
            "ComparisonOperator": "GREATER_THAN",
            "NotificationType": "ACTUAL",
            "Threshold": 80,
            "ThresholdType": "PERCENTAGE"
        },
        "Subscribers": [
            {
                "Address": "tony.osunde02@gmail.com",
                "SubscriptionType": "EMAIL"
            }
        ]
    }
]
```

