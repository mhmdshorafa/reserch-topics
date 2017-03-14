# reserch-topics

# Authentication

Authentication within hapi is based on the concept of schemes and strategies. Think of a scheme as a general type of auth, like "basic" or "digest". A strategy on the other hand, is a pre-configured and named instance of a scheme.

## What's the difference between Basic and Digest ?

Digest Authentication communicates credentials in an encrypted form by applying a hash function to the the username, the password, a server supplied nonce value, the HTTP method, and the requested URI.

Whereas Basic Authentication uses unencrypted base64 encoding.

Therefore Basic Authentication should generally only be used where transport layer security is provided such as https.

### Let's look at an example of how to use hapi-auth-basic.

```javascript
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

# Validation

Data validation is the process of ensuring that a program operates on clean, correct and useful data. It uses routines, often called "validation rules" "validation constraints" or "check routines", that check for correctness, meaningfulness, and security of data that are input to the system. A Validation rule is a criterion or constraint used in the process of data validation, carried out after the data has been encoded onto an input medium and involves a data vet or validation program.

## List of criteria :

Size. The number of characters.

Format checks Data must conform to a specified format.

Consistency. Codes in the data items which are related in some way can thus be checked for the consistency of their relationship.

## Validation methods

Data type checks

Logic check Checks that an input does not yield a logical error, e.g., an input value should not be 0 when it will divide some other number somewhere in a program

File existence check Checks that a file with a specified name exists. This check is essential for programs that use file handling.

Format or picture check Checks that the data is in a specified format (template)

# cookies

```javascript
server.state('data', {
    ttl: null,
    isSecure: true,
    isHttpOnly: true,
    encoding: 'base64json'
});
reply('Hello').state('data', 'mhmdshorafa');
```

# caching

```javascript
server.route({
path: '/hapi',
method: 'GET',
handler: function (request, reply) {

    const response = reply({ be: 'hapi' });

},
config: {
    cache: {
        expiresIn: 2,
        privacy: 'private'
    }
}
});
```
# Templating with helper functions 

first we have to configure at least one templating engine on the server "server.views"
we load the "vision" module as a plugin. It adds template rendering support to hapi.
we register the handlebars module as the engine responsible for rendering templates with an extension of .html.

There are many options available to the views engine in hapi.
Note that all options may be set either globally, which configures them for all registered engines, or local to one specific engine, for example:
```js
server.views({
    engines: {
        'html': {
            module: require('handlebars'),
            compileMode: 'sync' // engine specific
        }
    },
    compileMode: 'async' // global setting
});
```
By default, hapi assumes that template engines are synchronous (i.e. compileMode defaults to sync), to use an asynchronous engine you must set compileMode to async.

# View helpers

helpersPath: the directory that contains your template helpers.
JavaScript modules located in the defined helpersPath are available in templates.
