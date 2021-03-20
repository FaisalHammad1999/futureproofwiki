## Authentication vs Authorisation
These two words can get thrown around a lot and whilst they are both important and usually used together, it is important to note the difference:

**Authentication**: checking if someone is who they say they are. \
**Authorisation**: checking if someone has permission to access something.

## Popular approaches
### Session-based
Credentials are gathered from the user and compared to what is stored in the database. If a match is found, a 'session' is initialised and stored server-side. A 'session id' is sent to the client to store in a cookie. This session id is sent with all subsequent requests to the server where it is compared with the stored session data. If it matches, the requested data is sent. Sessions may end when a user explicitly logs out or after a set amount of time.

### Token-based
Credentials are gathered from the user and compared to what is stored in the database. If a match is found, A Web Token is generated, signed and sent over to be stored on the client side. This token will be included in the headers of any subsequent requests to the server. Upon receiving a request, the server will verify the token and if it is valid, will return the requested data. 

A JWT (JSON Web Token) consists of three parts:
**Header**: metadata including the type of algorithm used \
**Payload**: the data you are transporting eg. user data, expiry time \
**Signature**: this hashed combination of the header and payload is what enables the token to be verified \

To see these in all their decoded glory, check out the [JWT Debugger](https://jwt.io/).

There are various libraries available to assist in the process of signing and verifying JWT tokes, including [`jsonwebtoken`](https://www.npmjs.com/package/jsonwebtoken)

### Passwordless
Imagine a world where we didn't have to remember passwords... and not just a solution of 'store them in a credentials manager' but completely do away with them. WebAuthn is one of the tools addressing this and at time of writing, whilst most major browsers have added support for WebAuthn in their most recent releases, you probably don't want to rely solely on it at the moment. Learn more about it [here](https://webauthn.guide/) and keep an eye out for updates on libraries such as [npm's webauthn](https://www.npmjs.com/package/webauthn) which is ready to experiment with but not quite ready for production use at time of writing.

## Which to choose?
Between session and token based authorisation, token based is the current most commonly used method for authentication on the web, not least because it scales effortlessly. Since session-based auth relies on storing some additional data on the server, this can cause issues when scaling. The two (and other auth methods) can also be combined to create a hybrid solution customised for a specific use case. At this point, if you're not sure which one to use, we recommend starting with JWT tokens.

----

## A basic authentication flow for an Express app
We will first look at using `bcrypt` to encrypt passwords on registration before storing in the database and on login to be able to compare. bcrypt does most of the hard work for us here of generating a **salt**, **hashing** the password and adding it all together. Your job is just to make sure that the hashed password is stored in the database, not the plain text version! bcrypt will also help us when comparing a plain text password to a a stored hashed password.

```js
const bcrypt = require('bcrypt');

router.post('/register', async (req, res) => { 
// assuming a body of eg. { username: 'Gingertonic', email: 'email@address.com, password: 'weak-password!' }
    try {
        const salt = await bcrypt.genSalt(); // generate salt
        const hashed = await bcrypt.hash(req.body.password, salt); // hash password and add salt
        await User.create({...req.body, password: hashed}); // insert new user into db
        res.status(201).json({msg: 'User created'});
    } catch (err) {
        res.status(500).json({err});
    }
})

router.post('/login', async (req, res) => {
// assuming a body of eg. { email: 'email@address.com, password: 'weak-password!' }
    try {
        const user = await User.findByEmail(req.body.email) // find user record
        const authed = bcrypt.compare(req.body.password, user.passwordDigest) // compare given password to stored hashed password
        if (authed){
            res.status(200).json({ user })
        } else {
            throw new Error('User could not be authenticated')  
        }
    } catch (err) {
        res.status(403).json({ err });
    }
})
```

***

## Authorisation options to consider
With the code above, we can now store user's passwords securely - very important! The next step is to be able to persist a login across multiple requests. The options discussed above of JWT tokens and sessions are both worth looking into. For JWT, look at the [`jsonwebtoken`](https://www.npmjs.com/package/jsonwebtoken) npm library and for sessions in express, [`express-session`](https://www.npmjs.com/package/express-session) is a perfect option. As always, there are many alternatives available so have a look around and see what solution works for you and your application! They can be a little complex but take your time and utilise the many great resources available to both learn and implement.

In this example, see how we've extended our `login` route from above to include the signing a token which is sent back to the client:
```js
// Server: login route
router.post('/login', async (req, res) => {
    try {
        const user = await User.findByEmail(req.body.email)
        if(!user){ throw new Error('No user with this email') }
        const authed = bcrypt.compare(req.body.password, user.passwordDigest)
        if (!!authed){
            // new things start here!
            const payload = { username: user.username, email: user.email }
            const sendToken = (err, token) => {
                if(err){ throw new Error('Error in token generation') }
                res.status(200).json({
                    success: true,
                    token: "Bearer " + token,
                });
            }
            // creating & signing a token that will expire in 1hour (3600 seconds)
            // the callback of `sendToken` is called only once the token is successfully signed
            // the second argument, your secret, would be better stored elsewhere, perhaps as an environment variable
            jwt.sign(payload, 'supersecret-secret', { expiresIn: 3600 }, sendToken);
        } else {
            throw new Error('User could not be authenticated')  
        }
    } catch (err) {
        res.status(401).json({ err });
    }
})
```

Once we receive the token on the client, we can decode it with a library such as [`jwt-decode`](https://www.npmjs.com/package/jwt-decode) (also available via [CDN](https://cdn.jsdelivr.net/npm/jwt-decode@1.5.1/build/jwt-decode.min.js)). Where you store it a contentious point. `localStorage` is one such hotly debated option! Cookies are another. Each come with their own pros and cons so read up to see what the trade offs are.

Wherever you store it, you can now use it in an authorization header in requests to your server and any routes you wish to be made available only to authorized users, you can guard by ensuring the token provided can be verified. This will check to see if the token is valid and also that it has not expired.
```js
// Client: request to a route which requires authorization by token
async function getAllPosts(){
    try {
        const options = { headers: new Headers({'Authorization': localStorage.getItem('token')}) }
        const response = await fetch('http://localhost:3000/posts', options);
        const data = await response.json();
        if(data.err){
            console.warn(data.err);
            logout();
        }
        return data;
    } catch (err) {
        console.warn(err);
    }
}
```

```js
// Server: /posts router
const jwt = require('jsonwebtoken');

// I wish to protect the /posts route, note the passing of `verifyToken` as the only slightly 'new' item here
router.get('/', verifyToken, async (req, res) => {
    try {
        const posts = await Post.all
        res.json(posts)
    } catch (err) {
        res.status(500).send({ err })
    }
})

function verifyToken(req, res, next){
    const header = req.headers['authorization'];
    if (header) {
        const token = header.split(' ')[1]; // We just need the bit after 'Bearer '
        // using the `jsonwebtoken` library to verify the received token
        jwt.verify(token, 'supersecret-secret', async (err, data) => {
            if(err){
                // if it cannot be verified, access is forbidden
                res.status(403).json({ err: 'Invalid token' })
            } else {
                // if all went well, continue to the route handler (the next argument to the `router.get` above)
                next();
            }
        })
    } else {  // if no token is not present, access is forbidden
        res.status(403).json({ err: 'Missing token' })
    }
}
```


If you are a current or past student of futureproof, check out the [demo repo `jwt` branch](https://github.com/getfutureproof/fp_study_notes_authentication/tree/jwt) to see an implementation of the `jsonwebtoken` and `jwt-decode` libraries at work.

***

### Extending our options with OAuth
[OAuth](https://oauth.net/) is an open standard protocol for authorisation between services. It is very common now to give one service you are logged in to, limited access to another service. Perhaps you have given Instagram access to your Twitter account for automatic cross posting or allowed Netlify access to certain repositories on GitHub.

**Taking the second example, our character list is:** \
Protected Resource: repositories on GitHub \
Resource Owner: User _(with access to both services, thus in a position to grant access)_ \
Resource Server: GitHub servers _(where the repositories are stores)_ \
Client: Netlify - this use of the word 'client' can get a bit confusing if we are not clear that we are talking about OAuth at the time! \
Authorisation Server: A service to handle authorisation checks

**A full OAuth flow 'conversation' goes something like:** \
Resource Owner: 'Hey client, get my protected resource!' \
Client: 'Hey authorisation server, I need these protected resources please' \
Auth Server: 'Hey resource owner, client says they want access to this protected resource, is that okay?' \
Resource Owner: 'Yes, no problem, please give client access to the protected resource' \
Authorisation Server: 'Hey client, here's a an authorisation token, please send it back to confirm receipt...' \
Client: 'Thanks for the auth token, auth server. Here it is, safe and sound. Now please can I have access to those resources we were talking about..?' \
Authorisation Server: 'Thanks client, I trust you now. Here's a token you can use in the future to get those resources you asked for' \
Client: 'Hey resource server, I have an access token for these protected resources, please can I have them?' \
Resource Server: 'Hey auth server, can you just take a look at this access token and make sure it's legit?' \
Auth Server: 'Yep that looks good' \
Resource Server: 'Okay client, everything checks out, here are those protected resources you wanted' 

Simplified flows can be utilised and depending on the level of security required, may be suitable in some cases. If your access tokens are going to expire quite soon then you are more likely to use a simplified flow.

OAuth actually uses JWT so the flows might feel quite familiar once you get started.

How many services have you 'logged in' to with your Google or Facebook account? The OAuth protocol can also be integrated into an authentication flow. There are many third party library implementations to check out for this including [OAuth.io](https://docs.oauth.io/), [Passport](https://www.npmjs.com/package/passport), [Okta](https://developer.okta.com/code/nodejs/) and many more! 

*** 

## Protecting Routes within React
We have the option of putting up the walls to certain views within our React app. To achieve this, we will hijack the `react-router-dom` `Route` component and add some extra logic!
```js
import React from 'react';
import { Route, Redirect } from 'react-router-dom';

const PrivateRoute = ({ component: Component, isLoggedIn, ...rest }) => (
    <Route render={() => (
        !!isLoggedIn
            ? <Component {...rest} />
            : <Redirect to='/login' />
    )} />
)


export default PrivateRoute;
```

Now we have a resuable PrivateRoute component that essentially just extends the functionality of Route, we can use it in the same way:
```js
<Switch>
    <PrivateRoute path='/feed' isLoggedIn={this.state.isLoggedIn} component={Feed} />
    <PrivateRoute path='/profile' isLoggedIn={this.state.isLoggedIn} user={this.state.currentUser} component={Profile} />
</Switch>
```
