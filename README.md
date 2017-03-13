# reserch-topics

# Authentication

Authentication within hapi is based on the concept of schemes and strategies. Think of a scheme as a general type of auth, like "basic" or "digest". A strategy on the other hand, is a pre-configured and named instance of a scheme.

## What's the difference between Basic and Digest ?

Digest Authentication communicates credentials in an encrypted form by applying a hash function to the the username, the password, a server supplied nonce value, the HTTP method, and the requested URI.

Whereas Basic Authentication uses unencrypted base64 encoding.

Therefore Basic Authentication should generally only be used where transport layer security is provided such as https.

### Let's look at an example of how to use hapi-auth-basic.

```
'use strict';

const Bcrypt = require('bcrypt');
const Hapi = require('hapi');
const Basic = require('hapi-auth-basic');

const server = new Hapi.Server();
server.connection({
  host: 'localhost',
  port: 3000
});

const users = {
  john: {
    username: 'john',
    password: '$2a$10$iqJSHD.BGr0E2IxQwYgJmeP3NvhPrXAeLSaGCj6IR/XU5QtjVu5Tm', // 'secret'
    name: 'Ali',
    id: '2133d33a'
  }
};

const validate = function(request, username, password, callback) {
  const user = users[username];
  if (!user) {
    return callback(null, false);
  }

  Bcrypt.compare(password, user.password, (err, isValid) => {
    callback(err, isValid, {
      id: user.id,
      name: user.name
    });
  });
};

server.register(Basic, (err) => {
  if (err) {
    throw err;
  }

  server.auth.strategy('simple', 'basic', {
    validateFunc: validate
  });
  server.route({
    method: 'GET',
    path: '/',
    config: {
      auth: 'simple',
      handler: function(request, reply) {
        reply('hello, ' + request.auth.credentials.name);
      }
    }
  });

  server.start((err) => {
    if (err) {
      throw err;
    }
    console.log('server running at: ' + server.info.uri);
  });
});
```
