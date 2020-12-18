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

### Extending our options with Passport
[Passport](https://www.npmjs.com/package/passport) is a flexible library that enables us to offer users the ability to log in with a variety of services eg. Google, Facebook, Twitter, as well our our own local solution. 

***

## Authorisation options to consider
With the code above, we can now store user's passwords securely - very important! The next step is to be able to persist a login across multiple requests. The options discussed above of JWT tokens and sessions are both worth looking into. For JWT, look at the [`jsonwebtoken`](https://www.npmjs.com/package/jsonwebtoken) npm library and for sessions in express, [`express-session`](https://www.npmjs.com/package/express-session) is a perfect option. As always, there are many alternatives available so have a look around and see what solution works for you and your application! They can be a little complex but take your time and utilise the many great resources available to both learn and implement.

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
