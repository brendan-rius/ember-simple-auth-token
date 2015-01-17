# Ember Simple Auth Token [![Build Status](https://travis-ci.org/jpadilla/ember-cli-simple-auth-token.svg?branch=master)](https://travis-ci.org/jpadilla/ember-cli-simple-auth-token)

This is an extension to the Ember Simple Auth library that provides a default token authenticator, an enhanced authenticator with automatic refresh capability, and an authorizer that are compatible with APIs with token-based authentication.

Based on [ember-simple-auth-devise](https://github.com/simplabs/ember-simple-auth/tree/master/packages/ember-simple-auth-devise).

## Authors

- [José Padilla](https://github.com/jpadilla)
- [Giovanni Collazo](https://github.com/gcollazo)

## Installation

To install Ember Simple Auth Token in an Ember.js application that uses [Ember CLI](https://github.com/stefanpenner/ember-cli):

```
npm install --save-dev ember-cli-simple-auth-token
ember generate simple-auth-token
```

## The Authenticators

In order to use the Token authenticator or the JWT authenticator, the application needs to have a login route:

```js
// app/router.js
App.Router.map(function() {
  this.route('login');
});
```

This route displays the login form with fields for `identification`,
`password`:

```html
{{! app/templates/login.hbs }}
<form {{action 'authenticate' on='submit'}}>
  <label for="identification">Login</label>
  {{input id='identification' placeholder='Enter Login' value=identification}}
  <label for="password">Password</label>
  {{input id='password' placeholder='Enter Password' type='password' value=password}}
  <button type="submit">Login</button>
</form>
```

The `authenticate` action that is triggered by submitting the form is provided
by the `LoginControllerMixin` that the respective controller in the application
can include (the controller can also implement its own action and use the
session API directly; see the
[API docs for `Session`](http://ember-simple-auth.simplabs.com/ember-simple-auth-api-docs.html#SimpleAuth-Session)).
It then also needs to specify the Token authenticator to be used:

**Token Authenticator**

Default base implementation for token authentication.

```js
// app/controllers/login.js
import Ember from 'ember';
import LoginControllerMixin from 'simple-auth/mixins/login-controller-mixin';

export default Ember.Controller.extend(LoginControllerMixin, {
  authenticator: 'simple-auth-authenticator:token'
});
```

**JWT Authenticator**

Extends the Token Authenticator and adds automatic refresh functionality.

```js
// app/controllers/login.js
import Ember from 'ember';
import LoginControllerMixin from 'simple-auth/mixins/login-controller-mixin';

export default Ember.Controller.extend(LoginControllerMixin, {
  authenticator: 'simple-auth-authenticator:jwt'
});
```

## The Authorizer

The authorizer authorizes requests by adding `token` property from the session in the `Authorization` header:

```
Authorization: Bearer <token>
```

To use the authorizer, configure it in the global environment object:

```js
// config/environment.js
ENV['simple-auth'] = {
  authorizer: 'simple-auth-authorizer:token'
};
```

## Available Customization Options

```js
// config/environment.js
ENV['simple-auth-token'] = {
  serverTokenEndpoint: '/api-token-auth/',
  identificationField: 'username',
  tokenPropertyName: 'token',
  authorizationPrefix: 'Bearer ',
  authorizationHeaderName: 'Authorization',

  // Only used with JWT Authenticator.
  refreshAccessTokens: true,
  serverTokenRefreshEndpoint: '/api-token-refresh/',
  tokenExpireName: 'exp',
  timeFactor: 1  // example - set to "1000" to convert incoming seconds to milliseconds.
};
```
