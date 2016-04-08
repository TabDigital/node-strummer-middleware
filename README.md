# strummer-middleware

[![NPM](http://img.shields.io/npm/v/strummer.svg?style=flat-square)](https://npmjs.org/package/strummer-middleware)
[![License](http://img.shields.io/npm/l/node-strummer-middleware.svg?style=flat-square)](https://github.com/TabDigital/node-strummer-middleware)

[![Build Status](https://travis-ci.com/TabDigital/node-strummer-middleware.svg?token=RfpP7WAYQqnR4gnFRm4r&branch=master)](https://travis-ci.com/TabDigital/node-strummer-middleware)

## Description

Wraps your [strummer](https://github.com/TabDigital/strummer) validation logic into middleware ready for a HTTP request!

## Usage

```bash
npm install strummer-middleware --save
```

```js
var s = require('strummer');
var sware = require('strummer-middleware');

var validate = sware({
  body: s({ id: 'uuid', name: 'string', age: 'number' })
});

server.post('/users', validate, controller);
```

`strummer-middleware` can validate 3 areas of the request:

```js
sware({
  body:     /* match req.body     */
  query:    /* match req.query    */
  headers:  /* match req.headers  */
})
```

These fields were chosen because they are a well established standard. `strummer-middleware` is not responsible for creating `req.body` or `req.query`, you must follow the documentation of your web framework.

## Error handling

`strummer-middleware` will call `next(err)` in case of validation errors.
In [Express](http://expressjs.com/) this means you will also need to set up an error handler.

```js
function errorHandler(err, req, res, next) {
  // is this a strummer validation error?
  if (err.status === 'InvalidSyntax') {
    res.statusCode(400).send('Bad request')
  }
}

// global handler
server.use(errorHandler)

// or local handler
server.post('/users', validation, errorHandler, controller)
```

The `err` object also contains more information:

```js
console.log(err.message)
console.log(err.fields)
```

## Strummer integration

Note that [strummer](https://github.com/TabDigital/strummer) is not included in `package.json` as a dependency, peerDependency or devDependency. This is to ensure we stay compatible with most versions of Node.

It also makes it very obvious which version of `strummer` you are using. For example, if `strummer-middleware` bundled its own version the following code would be quite confusing:

```js
var s = require('strummer')

sware({
  body: {
    name: 'string',    // would use the built-in version
    age: s.custom()    // would use the provided version
  }
})
```

Instead of `strummer`, you can also choose to use a custom validation library,
as long as matchers have a `.match(path, value)` function that returns an array.
