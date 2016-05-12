# node-http-signature (JWT extension)

A fork of Joyent's [HTTP Signature Scheme](http_signing.md) reference implementation with [JWT](http://jwt.io/) as an extension.

See [Joyent's repository](https://github.com/joyent/node-http-signature) for basic usage and up-to-date information about HTTP Signature.

For reference, you may also peek at [HTTP Signature's Internet-Draft status](https://tools.ietf.org/html/draft-cavage-http-signatures-04).

## Why

HTTP Signature enables the server receiving an HTTP request to trust the identity of the sender as well as the integrity of the message itself *_without the need for multiple round-trips_*.

In plain language, this means that the server can trust that the caller is who they say they are (authentic), and that what the server received has not been tampered with in transit (message integrity).

HTTP Signature is:

* synergistic with the REST architectural style
* [pure HTTP](https://tools.ietf.org/html/rfc2617)
* efficient compared to schemes requiring multiple round-trips (e.g. [CHAP](http://en.wikipedia.org/wiki/Challenge-Handshake_Authentication_Protocol))

### Adding JWT

From the server's point of view, HTTP Signature verifies the identity of a particular network endpoint (the caller) at a particular point in time (at message generation). The confidence this affords the server satisfies many security architectures; however, if the caller makes requests to the server on another security principal's behalf (e.g. an application level user), it is desirable to have the other principal's security related context conveyed with the request. This is where [JSON Web Token (JWT)](http://jwt.io/) comes in to play.

JSON Web Token encodes a [principal's identity claims](http://en.wikipedia.org/wiki/Claims-based_identity) into a security token that may be trusted across collaborating systems (a federation).

This fork leverages [HTTP Signature's built-in extension method](https://tools.ietf.org/html/draft-cavage-http-signatures-04#appendix-B) to piggy-back a JWT with an HTTP Signature, enabling API endpoints to trust not only the caller (which may be another API endpoint), but also trust an additional security context (the end user).

## How

We endeavor to stay entirely drop-in compatible with [the forked repository](https://github.com/joyent/node-http-signature) so that libraries that rely on Joyent's module ([`request`](https://github.com/request/request), [`restify`](https://github.com/mcavage/node-restify), etc.) work correctly when this module is overlaid.

As such, this module doesn't create JSON Web Tokens, it simply includes them in the signature if you provide one.

On the server side, Joyent's reference implementation's will place the provided `jwt` property on the parsed options: `parsed.options.jwt`.

### Client

```javascript
var httpSignature = require('http-signature');

function signWithOptionalJwt(req, jwt, keyId, key) {
  var options = {
    key: key,
    keyId: keyId
  };
  if (jwt) options.jwt = jwt;

  httpSignature.sign(req, options);
  return req;
}
```

## Installation

Many node modules already rely on HTTP Signature, so this module is installed by overlaying/patching it in your module's `package.json` file.

```json
{
  "name": UR-module,
  ...
  "dependencies": {
    ...
    "http-signature": "LeisureLink/node-http-signature",
    ...
  }
}
```

This module is also published to npm as @leisurelink/http-signature.

## License

MIT.

## Bugs

See <https://github.com/LeisureLink/node-http-signature/issues>.
