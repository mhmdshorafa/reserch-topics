# reserch-topics

# Authentication

Authentication within hapi is based on the concept of schemes and strategies.
Think of a scheme as a general type of auth, like "basic" or "digest". A strategy on the other hand, is a pre-configured and named instance of a scheme.

### What’s the difference between Basic and Digest ?

Digest Authentication communicates credentials in an encrypted form by applying a hash function to the the username, the password, a server supplied nonce value, the HTTP method, and the requested URI.

Whereas Basic Authentication uses unencrypted base64 encoding.

Therefore Basic Authentication should generally only be used where transport layer security is provided such as https.

###### Let's look at an example of how to use hapi-auth-basic.
