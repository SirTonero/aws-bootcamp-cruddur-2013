# Week 3 — Decentralized Authentication

This week we learnt how to implenmet decnetralised authentication using Amazon Cognito.

## Installing AWS Amplify

we have to have aws amplify installed using the following command

```sh
npm i aws-amplify --save
```

npm i aws-amplify `-save`

Adding `--save` will add the installation to the npm package.json folder

## Creating An AWS Userpool

To create a userpool in aws we will be using the AWS MAnagement Console 

- login to your AWS Management Console
- search for aws cognito 
- click `Create UserPool`
- select `cognito userpool` as a provider type.
- select email as a cognito user pool signin option or one that suit your usecase and press next.
- slect no mfa, enable self service account recovery
- select email only as a delivery method for account recovery to avoid spend then click next
- enable `self registration` to allow users create account from our app into cognito
- allow cognito to automatically send message to verify and comfirm user.
- Select `preferred username and name` as required attributes.
- on the `configure message delivery` select  `send email with cognito`
- select a `FROM email address` and click next
- set user pool name to `cruddur-user-pool` 
- select public client as app type
- set app client name to `Cruddur`.
- select `Dont generate secret` as we wont be using secrets.
- review all settings and select create userpool.

<img width="1409" alt="SCR-20230311-8vj" src="https://user-images.githubusercontent.com/112965272/224468550-26961cc6-6d99-4e9a-b3ce-48439b984b7e.png">

<img width="1431" alt="SCR-20230311-a6g" src="https://user-images.githubusercontent.com/112965272/224468703-34fd82a8-da56-4567-9bf4-2521b0551b95.png">



