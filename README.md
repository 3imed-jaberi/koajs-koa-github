# [**koa-github**](https://github.com/koajs/koa-github)

> Simple github auth middleware for koa.


[![Build Status](https://secure.travis-ci.org/koajs/koa-github.png)](http://travis-ci.org/koajs/koa-github)

[![NPM](https://nodei.co/npm/koa-github.png?downloads=true)](https://nodei.co/npm/koa-github/)


## Example

```js
//use http://localhost:7001 to test

const koa = require('koa');
const http = require('http');
const session = require('koa-generic-session');
const githubAuth = require('koa-github');

const app = new koa();

app.name = 'nae-web';
app.keys = ['key1', 'key2'];

app.use(session());
app.use(githubAuth({
  clientID: '5ec1d25d2a3baf99a03c',
  clientSecret: '513607494a244e2759738cae3d50a89494c1e7f0',
  callbackURL: 'http://localhost:7001',
  userKey: 'user',
  timeout: 10000
}));

app.use(function handler() {
  if (!this.session.githubToken) {
    this.body = '<a href="/github/auth">login with github</a>';
  } else {
    this.body = this.session.user;
  }
});

app.on('error', function (err) {
  if (!err.status || err.status >= 500) {
    logger.error(err);
  }
});

http.createServer(app.callback()).listen(7001);
```


## Options

```
  @param {Object} options
    - [String] clientID      github client ID     // regist in https://github.com/settings/applications
    - [String] clientSecret  github client secret
    - [String] callbackURL   github redirect url
    - [String] signinPath    sign in with github's triggle path, default is `/github/auth`
    - [String] tokenKey      session key, default is githubToken
    - [String] userKey       user key, if set user key, will request github once to get the user info
    - [Array]  scope         A comma separated list of scopes
    - [Number] timeout       request github api timeout
    - [String] redirect      redirect key when call signinPath, so we can redirect after auth, default is `redirect_uri`
```

* clientID, clentSecret and callbackURL are registered in https://github.com/settings/applications.
* if you set userKey field, `koa-github` will request to get the user info and set this object to `this.session[options.userKey]`, otherwise `koa-github` will not do this request.
* if you triggle by `/github/auth?redirect_uri=/callback`, `koa-github` will redirect to `/callback` after auth, and the redirect_uri only accept start with `/`.


## Licence

[MIT](/LICENSE)