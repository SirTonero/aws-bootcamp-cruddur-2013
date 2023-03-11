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

<img width="1387" alt="SCR-20230311-962" src="https://user-images.githubusercontent.com/112965272/224469043-1c9a8932-404e-4085-a5bd-514e9b8e3639.png">
<img width="1419" alt="SCR-20230311-9am" src="https://user-images.githubusercontent.com/112965272/224469065-f5dcceb1-cddd-433e-9014-7ce30faea6b8.png">

<img width="1411" alt="SCR-20230311-9q2" src="https://user-images.githubusercontent.com/112965272/224469125-c60f7dc0-a0e7-4644-b32f-0f359aa77fa5.png">

<img width="1420" alt="SCR-20230311-9wm" src="https://user-images.githubusercontent.com/112965272/224469173-cb51bcce-cb13-436b-acc4-7e2563fd7fd9.png">
<img width="1420" alt="SCR-20230311-and" src="https://user-images.githubusercontent.com/112965272/224469490-388babbd-ce1f-478d-b360-f9af8edf2f66.png">

### cruddur Userpool ID 
<img width="1437" alt="SCR-20230311-atk" src="https://user-images.githubusercontent.com/112965272/224469910-bc608af2-51ae-467d-b704-7690f341fb58.png">

### configuring AWS Amplify
we will be hooking up our cognito pool to our `App.js` 

to hook cognito pool we need to import aws amplify using the import statement below.

```javascript
import { Amplify } from 'aws-amplify';
```
to configure amplify, we need to set the following ENV in our code as well as our dockerfile

- AWS_PROJECT_REGION
- aws_cognito_identity_pool_id
- aws_cognito_region
- aws_user_pools_id
- aws_user_pools_web_client_id

The `App.js` would be like this 👇

```javascript
import { Amplify } from 'aws-amplify';

Amplify.configure({
  "AWS_PROJECT_REGION": process.env.REACT_AWS_PROJECT_REGION,
  "aws_cognito_identity_pool_id": process.env.REACT_APP_AWS_COGNITO_IDENTITY_POOL_ID,
  "aws_cognito_region": process.env.REACT_APP_AWS_COGNITO_REGION,
  "aws_user_pools_id": process.env.REACT_APP_AWS_USER_POOLS_ID,
  "aws_user_pools_web_client_id": process.env.REACT_APP_CLIENT_ID,
  "oauth": {},
  Auth: {
    // We are not using an Identity Pool
    // identityPoolId: process.env.REACT_APP_IDENTITY_POOL_ID, // REQUIRED - Amazon Cognito Identity Pool ID
    region: process.env.REACT_AWS_PROJECT_REGION,           // REQUIRED - Amazon Cognito Region
    userPoolId: process.env.REACT_APP_AWS_USER_POOLS_ID,         // OPTIONAL - Amazon Cognito User Pool ID
    userPoolWebClientId: process.env.REACT_APP_AWS_USER_POOLS_WEB_CLIENT_ID,   // OPTIONAL - Amazon Cognito Web Client ID (26-char alphanumeric string)
  }
});

```
### Adding the cognito ENV to our docker-compose yaml file.

![SCR-20230311-let](https://user-images.githubusercontent.com/112965272/224490154-7798780b-22d0-4369-886f-ffddf72f848d.png)


### Using Condition to show component based on loggedin or logged out 

we have to have the code below 👇 

inside our cruddur `HomeFeedPage.js `

```javascript
import { Auth } from 'aws-amplify';

// set a state
const [user, setUser] = React.useState(null);

// check if we are authenicated
const checkAuth = async () => {
  Auth.currentAuthenticatedUser({
    // Optional, By default is false. 
    // If set to true, this call will send a 
    // request to Cognito to get the latest user data
    bypassCache: false 
  })
  .then((user) => {
    console.log('user',user);
    return Auth.currentAuthenticatedUser()
  }).then((cognito_user) => {
      setUser({
        display_name: cognito_user.attributes.name,
        handle: cognito_user.attributes.preferred_username
      })
  })
  .catch((err) => console.log(err));
};

// check when the page loads if we are authenicated
React.useEffect(()=>{
  loadData();
  checkAuth();
}, [])
```







