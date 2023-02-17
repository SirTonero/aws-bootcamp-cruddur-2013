# Week 0 — Billing and Architecture

## Required Homework/Tasks

---

1. Create AWS Budget
1. Installation and Setup of AWS CLI
1. Recreate Logical Architectural Deisgn
1. Conceptual Achitectural Diagram or Napkin Design
1. Generating AWS Credentials
1. create a Billing Alarm

----

## Recreate Logical Architectural Deisgn

#### LUCID allowed me to recreate the amazing cruddur app architectural diagram. I would like to introduce you to Lucid before I show you my diagram.  <br> <br>Lucidchart is a web-based diagramming application that allows users to visually collaborate on drawing, revising and sharing charts and diagrams, and improve processes, systems, and organizational structures. It is produced by Lucid Software Inc.

### my Cruddur Achitectural Diagram Remake
![Screenshot_6](https://user-images.githubusercontent.com/112965272/219649122-81c52230-109e-4aec-99bb-67429b5d52a8.png)


### find the link to my diagram👇below.

[My Cruddur Achitectural Diagram Remake](https://lucid.app/lucidchart/635a50ae-73c1-4bc4-aab4-655e4b626d24/edit?viewport_loc=-734%2C-1448%2C2879%2C1325%2C0_0&invitationId=inv_6c508643-34c4-4ca2-b0ec-d1b6acb1d313)



---
### My conceptual Diagram

![Screenshot 2023-02-16 at 21 31 40](https://user-images.githubusercontent.com/112965272/219481491-a6090ef9-aa3e-434c-9f44-fe7eccec567b.png)

### find the link to my conceptual diagram👇below

[my Conceptual Diagram](https://lucid.app/lucidchart/64efb01c-d098-4665-8130-6710e7f27213/edit?viewport_loc=-354%2C106%2C3758%2C1676%2C0_0&invitationId=inv_5ce91669-2322-4bb7-919c-bbe4da4ec156)
------
### Napkin Design

![20230217_120844](https://user-images.githubusercontent.com/112965272/219650013-7d4b50ba-681a-4f76-a37e-fd26531d25fc.jpg)


----------
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

<img width="915" alt="Screenshot 2023-02-17 at 22 11 10" src="https://user-images.githubusercontent.com/112965272/219794312-5577389d-6ba0-45ed-bcc5-a04c16059a43.png">


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

1. step 1 : Create a Json file with the json Script below called budgets.json

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
-------
I created my own budget for $10 usd because that is the state amount budgetted for this bootcamp, should in case i use a service that is not covered in the free tier.

I did not create a second budget, because i was concerned of udget spending going over the two budge free tier limit.

<img width="1215" alt="Screenshot 2023-02-17 at 23 17 26" src="https://user-images.githubusercontent.com/112965272/219804736-71de3dcc-3c22-4bee-90b2-6db45b4cd141.png">

---------
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

3. Create the alarm script for the notification alert. called budget-notifications-with-subscribers.json

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
4. in the cli type the command below and pass in those json script inside like the snippet below to create the aws budget

```cli
aws budgets create-budget \
    --account-id $AWS_ACCOUNT_ID \
    --budget file://aws/json/budget.json \
    --notifications-with-subscribers file://aws/json/budget-notifications-with-subscribers.json
```
5. type the following command to activate the alert of the sns service.
```cli
aws sns subscribe \
    --topic-arn="arn:aws:sns:us-east-1:370485215308:billing_alarm" \
    --protocol=email \
    --notification-endpoint=tony.osunde02@gmail.com
```

-----------

 ### Creating A billing Alarm.

#### A billing alarm is a service that will notify you by email when your aws account charges for the month have exceeded a monetary amount you have set. This is a tool to stay on-budget

- Go to (IAM Users Console](https://us-east-1.console.aws.amazon.com/console/home?region=us-east-1) TOnero-admin 
- click on the your username at the top of the management console and click on billing dashboard.
- click on billing preference
- click on manage billing alarm 
<img width="1249" alt="SCR-20230217-9j" src="https://user-images.githubusercontent.com/112965272/219508923-ab753cf5-091a-4481-9e93-cd6e9962b993.png">
<img width="1438" alt="SCR-20230217-bx" src="https://user-images.githubusercontent.com/112965272/219509400-4a841927-a25f-483b-a21f-04b96e41334f.png">
- click on metric and click on billing 
- click on total estimated charge under billing.
<img width="1241" alt="Screenshot 2023-02-17 at 00 22 18" src="https://user-images.githubusercontent.com/112965272/219510432-36ac64ee-1b00-4bb9-ac77-98a6ea8a6a9f.png">

<img width="849" alt="Screenshot 2023-02-17 at 00 22 57" src="https://user-images.githubusercontent.com/112965272/219510578-29864674-40f0-405f-9d7a-a93b7d6f801d.png">
- click next and assign an sns topic if you have any, or create one.
- click on create alarm next.
<img width="1438" alt="Screenshot 2023-02-17 at 00 26 13" src="https://user-images.githubusercontent.com/112965272/219510796-7d72f4dd-232c-4ab2-97b3-32bf77c955b1.png">

---------

## Bonus extra work

####
Since cruddur app is the next big thing to take over the micro blogging space, i decided to do a little mobile UI for it. please find a video below . Hope you like it.

https://user-images.githubusercontent.com/112965272/219794989-4f3ed589-92a4-4c89-b21a-fbb15d300a12.mp4



