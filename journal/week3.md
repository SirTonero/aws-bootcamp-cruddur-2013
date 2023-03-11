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

let us pass users to the following components:

```javascript
<DesktopNavigation user={user} active={'home'} setPopped={setPopped} />
<DesktopSidebar user={user} />
```

we will be rewritting the `DesktopNavigation.js` to use conditions to show links in the left column on whether a user is logged in or not.

observe we are passing the user to ProfileInfo


```javascript
import './DesktopNavigation.css';
import {ReactComponent as Logo} from './svg/logo.svg';
import DesktopNavigationLink from '../components/DesktopNavigationLink';
import CrudButton from '../components/CrudButton';
import ProfileInfo from '../components/ProfileInfo';

export default function DesktopNavigation(props) {

  let button;
  let profile;
  let notificationsLink;
  let messagesLink;
  let profileLink;
  if (props.user) {
    button = <CrudButton setPopped={props.setPopped} />;
    profile = <ProfileInfo user={props.user} />;
    notificationsLink = <DesktopNavigationLink 
      url="/notifications" 
      name="Notifications" 
      handle="notifications" 
      active={props.active} />;
    messagesLink = <DesktopNavigationLink 
      url="/messages"
      name="Messages"
      handle="messages" 
      active={props.active} />
    profileLink = <DesktopNavigationLink 
      url="/@andrewbrown" 
      name="Profile"
      handle="profile"
      active={props.active} />
  }

  return (
    <nav>
      <Logo className='logo' />
      <DesktopNavigationLink url="/" 
        name="Home"
        handle="home"
        active={props.active} />
      {notificationsLink}
      {messagesLink}
      {profileLink}
      <DesktopNavigationLink url="/#" 
        name="More" 
        handle="more"
        active={props.active} />
      {button}
      {profile}
    </nav>
  );
}
```
We'll update `ProfileInfo.js`

```js
import { Auth } from 'aws-amplify';

const signOut = async () => {
  try {
      await Auth.signOut({ global: true });
      window.location.href = "/"
  } catch (error) {
      console.log('error signing out: ', error);
  }
}
```

We'll rewrite `DesktopSidebar.js` so that it conditionally shows components in case you are logged in or not.

```js
import './DesktopSidebar.css';
import Search from '../components/Search';
import TrendingSection from '../components/TrendingsSection'
import SuggestedUsersSection from '../components/SuggestedUsersSection'
import JoinSection from '../components/JoinSection'

export default function DesktopSidebar(props) {
  const trendings = [
    {"hashtag": "100DaysOfCloud", "count": 2053 },
    {"hashtag": "CloudProject", "count": 8253 },
    {"hashtag": "AWS", "count": 9053 },
    {"hashtag": "FreeWillyReboot", "count": 7753 }
  ]

  const users = [
    {"display_name": "Andrew Brown", "handle": "andrewbrown"}
  ]

  let trending;
  if (props.user) {
    trending = <TrendingSection trendings={trendings} />
  }

  let suggested;
  if (props.user) {
    suggested = <SuggestedUsersSection users={users} />
  }
  let join;
  if (props.user) {
  } else {
    join = <JoinSection />
  }

  return (
    <section>
      <Search />
      {trending}
      {suggested}
      {join}
      <footer>
        <a href="#">About</a>
        <a href="#">Terms of Service</a>
        <a href="#">Privacy Policy</a>
      </footer>
    </section>
  );
}
```

## Signin Page

```js
import { Auth } from 'aws-amplify';

const [cognitoErrors, setCognitoErrors] = React.useState('');

const onsubmit = async (event) => {
  setCognitoErrors('')
  event.preventDefault();
  try {
    Auth.signIn(username, password)
      .then(user => {
        localStorage.setItem("access_token", user.signInUserSession.accessToken.jwtToken)
        window.location.href = "/"
      })
      .catch(err => { console.log('Error!', err) });
  } catch (error) {
    if (error.code == 'UserNotConfirmedException') {
      window.location.href = "/confirm"
    }
    setCognitoErrors(error.message)
  }
  return false
}

let errors;
if (cognitoErrors){
  errors = <div className='errors'>{cognitoErrors}</div>;
}

// just before submit component
{errors}
```

## Signup Page

```js
import { Auth } from 'aws-amplify';

const [cognitoErrors, setCognitoErrors] = React.useState('');

const onsubmit = async (event) => {
  event.preventDefault();
  setCognitoErrors('')
  try {
      const { user } = await Auth.signUp({
        username: email,
        password: password,
        attributes: {
            name: name,
            email: email,
            preferred_username: username,
        },
        autoSignIn: { // optional - enables auto sign in after user is confirmed
            enabled: true,
        }
      });
      console.log(user);
      window.location.href = `/confirm?email=${email}`
  } catch (error) {
      console.log(error);
      setCognitoErrors(error.message)
  }
  return false
}

let errors;
if (cognitoErrors){
  errors = <div className='errors'>{cognitoErrors}</div>;
}

//before submit component
{errors}
```

## Confirmation Page

```js
const resend_code = async (event) => {
  setCognitoErrors('')
  try {
    await Auth.resendSignUp(email);
    console.log('code resent successfully');
    setCodeSent(true)
  } catch (err) {
    // does not return a code
    // does cognito always return english
    // for this to be an okay match?
    console.log(err)
    if (err.message == 'Username cannot be empty'){
      setCognitoErrors("You need to provide an email in order to send Resend Activiation Code")   
    } else if (err.message == "Username/client id combination not found."){
      setCognitoErrors("Email is invalid or cannot be found.")   
    }
  }
}

const onsubmit = async (event) => {
  event.preventDefault();
  setCognitoErrors('')
  try {
    await Auth.confirmSignUp(email, code);
    window.location.href = "/"
  } catch (error) {
    setCognitoErrors(error.message)
  }
  return false
}
```

## Recovery Page

```js
import { Auth } from 'aws-amplify';

const onsubmit_send_code = async (event) => {
  event.preventDefault();
  setCognitoErrors('')
  Auth.forgotPassword(username)
  .then((data) => setFormState('confirm_code') )
  .catch((err) => setCognitoErrors(err.message) );
  return false
}

const onsubmit_confirm_code = async (event) => {
  event.preventDefault();
  setCognitoErrors('')
  if (password == passwordAgain){
    Auth.forgotPasswordSubmit(username, code, password)
    .then((data) => setFormState('success'))
    .catch((err) => setCognitoErrors(err.message) );
  } else {
    setCognitoErrors('Passwords do not match')
  }
  return false
}

## Authenticating Server Side

Add in the `HomeFeedPage.js` a header eto pass along the access token

```js
  headers: {
    Authorization: `Bearer ${localStorage.getItem("access_token")}`
  }
```

In the `app.py`

```py
cors = CORS(
  app, 
  resources={r"/api/*": {"origins": origins}},
  headers=['Content-Type', 'Authorization'], 
  expose_headers='Authorization',
  methods="OPTIONS,GET,HEAD,POST"
)
```







