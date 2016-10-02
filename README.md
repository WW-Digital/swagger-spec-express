# swagger-spec-express

> Programmatically generate your Swagger specification (JSON) file.

[![NPM][spe-npm-icon] ][spe-npm-url]

[![Build status][spe-ci-image] ][spe-ci-url]

A library that allows you to programmatically annotate your existing express api with [swagger](http://swagger.io/) info and then generate and validate your json spec file. All without completely starting over or changing the structure of your express routes. Please note that this document is still being written.

## Why

There are already a few libraries out there to add Swagger documentation to your [express](https://expressjs.com/) api, like [swagger-node-express](https://www.npmjs.com/package/swagger-node-express) and [swagger-node](https://github.com/swagger-api/swagger-node) which work really well, however they require you to either start from scratch or change your routes to work with their format. This libary is different and can easily be added to an existing and established express api using the normal patterns you are used to.

\##Installation
Requires Express 4.x

Install the package:

> npm install swagger-spec-express --save-exact

Basic code example:

```javascript
var express = require('express');
var swagger = require('swagger-spec-express');
var packageJson = require('./package.json');

var app = express();
var options = {
    title: packageJson.title,
    version: packageJson.version
};
swagger.initialise(app, options);

app.get('/swagger.json', function (err, res) {
    res.status(200).json(swagger.json());
}).describe({
    responses: {
        200: {
            description: "Returns the swagger.json document"
        }
    }
});

swagger.compile();
var port = 3000;
app.listen(port, appListening);
function appListening() {
    console.info(packageJson.name + ' is listening on port ' + port);
}
```

This will create a very basic express app that serves up the swagger.json document when you navigate to **<http://localhost:3000/swagger.json>**

The [JSON Schema](http://json-schema.org/) file that will be used to validate the supplied
metadata on the route can be found [here](https://raw.githubusercontent.com/eXigentCoder/swagger-spec-express/master/lib/schemas/meta-data.json) in addition the fields are detailed below. Wherever possible the library tries to adhear to the official [Swagger Specification](http://swagger.io/specification/) so that is a good place to look for extra information about what you can specify.

## Describing Your Routes

As seen above, once you have called initialise on your app, the describe method is automatically added and can be added to routes you declare directly on your app object.

But what if you want to use the router object? In the above example code above add the following to the top of the page:

```javascript
var exampleRouter = require('./example-route');
```

And then after calling **swagger.initialise(app, options);** add the following:

```javascript
app.use(exampleRouter);
```

Code for the example router:

```javascript
'use strict';
var express = require('express');
var swagger = require('swagger-spec-express');
var router = new express.Router();
swagger.swaggerize(router);
module.exports = router;

router.get('/one', function (req, res) {
    res.status(200).json({example: 1})
}).describe({
    responses: {
        200: {
            description: "Returns example 1"
        }
    }
});
```

## Adding the UI

_todo_

## API

<!-- Generated by documentation.js. Update this documentation by updating the source code. -->

### compile

[lib/index.js:41-48](https://github.com/eXigentCoder/swagger-spec-express/blob/abe8bd9796e02313af2b4d7cdb8cda3fd3d5b831/lib/index.js#L41-L48 "Source code on GitHub")

Will gather together all your described app routes and compile them into a single document to be served up by your api when you call `json`.
Can only be called once `initialise` has been called. Should only call this once you have completely finished describing your routes.

-   Throws **[Error](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error)** Will throw an error if `initialise` wasn't called or if you don't yet have any routes defined or if there are certain errors in your metadata

Returns **void** 

### validate

[lib/index.js:56-58](https://github.com/eXigentCoder/swagger-spec-express/blob/abe8bd9796e02313af2b4d7cdb8cda3fd3d5b831/lib/index.js#L56-L58 "Source code on GitHub")

Will validate the internal json document created by calling `compile`.
This is done using the [ajv](https://www.npmjs.com/package/ajv) validator against the [official JSON schema](https://www.npmjs.com/package/swagger-schema-official). \* @throws {Error} Throws an exception if called before `compile` or `initialise`.

Returns **{valid: [boolean](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean), errors: [Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)>, message: [string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)}** The result of the validation

### json

[lib/index.js:67-71](https://github.com/eXigentCoder/swagger-spec-express/blob/abe8bd9796e02313af2b4d7cdb8cda3fd3d5b831/lib/index.js#L67-L71 "Source code on GitHub")

Returns the swagger specification as a json object.

-   Throws **[Error](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error)** Throws an exception if called before `compile` or `initialise`. You do not need to call `validate` first.

Returns **[Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)** The Swagger JSON object describing your api. See <http://swagger.io/specification/>.

### swaggerise

[lib/swaggerise.js:9-45](https://github.com/eXigentCoder/swagger-spec-express/blob/abe8bd9796e02313af2b4d7cdb8cda3fd3d5b831/lib/swaggerise.js#L9-L45 "Source code on GitHub")

Adds the .describe function onto the provided object. The object should either be an express app or express router.

**Parameters**

-   `item` **[object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)** the item to apply

Returns **void** 

### describe

[lib/swaggerise.js:39-44](https://github.com/eXigentCoder/swagger-spec-express/blob/abe8bd9796e02313af2b4d7cdb8cda3fd3d5b831/lib/swaggerise.js#L39-L44 "Source code on GitHub")

Allows you describe an app our router route.

**Parameters**

-   `metaData` **[object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)** Metadata about a route (Generated)
    -   `metaData.summary` **\[[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)]** A brief summary of the operation. (Generated)
    -   `metaData.description` **\[[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)]** A longer description of the operation, GitHub Flavored Markdown is allowed. (Generated)
    -   `metaData.externalDocs` **\[[object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)]** information about external documentation (Generated)
        -   `metaData.externalDocs.description` **\[[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)]** A short description of the target documentation. GFM syntax can be used for rich text representation. (Generated)
        -   `metaData.externalDocs.url` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** Required. The URL for the target documentation. Value MUST be in the format of a URL. (Generated)
    -   `metaData.operationId` **\[[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)]** A unique identifier of the operation. (Generated)
    -   `metaData.produces` **\[[Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)>]** A list of MIME types the API can produce. (Generated)
    -   `metaData.consumes` **\[[Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)>]** A list of MIME types the API can consume. (Generated)
    -   `metaData.parameters` **\[([object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object) \| [string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String))]** An object to hold parameters that can be used across operations. This property does not define global parameters for all operations. See [Parameter Definitions Object](http://swagger.io/specification/#parametersDefinitionsObject). (Generated)
    -   `metaData.tags` **\[[Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)>]** A list of tags used by the specification with additional metadata. The order of the tags can be used to reflect on their order by the parsing tools. Not all tags that are used by the [Operation Object](swagger.io/specification/#operationObject) must be declared. The tags that are not declared may be organized randomly or based on the tools' logic. Each tag name in the list MUST be unique. See [Tag Object](http://swagger.io/specification/#tagObject). (Generated)
    -   `metaData.schemes` **\[[Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)>]** The transfer protocol of the API. (Generated)
    -   `metaData.deprecated` **\[[boolean](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean)]** Declares this operation to be deprecated. Usage of the declared operation should be refrained. Default value is false. (Generated)
    -   `metaData.security` **\[([array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array) \| [boolean](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean) \| [string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String))]** A declaration of which security schemes are applied for the API as a whole. The list of values describes alternative security schemes that can be used (that is, there is a logical OR between the security requirements). Individual operations can override this definition. See [Security Requirement Object](http://swagger.io/specification/#securityRequirementObject). (Generated)
    -   `metaData.common` **\[[object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)]** A collection of common data to include in this route. (Generated)
        -   `metaData.common.responses` **\[[Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)>]** Common responses as added by calling common.addResponse (Generated)
        -   `metaData.common.parameters` **\[[object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)]** A collection of common parameters to use for this route. (Generated)
            -   `metaData.common.parameters.header` **\[[Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)>]** A common header parameter as added by calling common.parameters.addHeader (Generated)
            -   `metaData.common.parameters.body` **\[[Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)>]** A common body parameter as added by calling common.parameters.addBody (Generated)
            -   `metaData.common.parameters.query` **\[[Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)>]** A common query string parameter as added by calling common.parameters.addQuery (Generated)
            -   `metaData.common.parameters.formData` **\[[Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)>]** A common form data parameter as added by calling common.parameters.addFormData (Generated)
    -   `metaData.responses` **[object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)** Response objects names can either be any valid HTTP status code or 'default'. (Generated)
-   `metaData.common.parameters.path` **\[[Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)>]** A common path parameter as added by calling common.parameters.addPath (Generated)

Returns **void** 

### initialise

[lib/initialise.js:120-136](https://github.com/eXigentCoder/swagger-spec-express/blob/abe8bd9796e02313af2b4d7cdb8cda3fd3d5b831/lib/initialise.js#L120-L136 "Source code on GitHub")

Will initialise your app with the required swaggers-spec information.
In addition you can pass in some options which will be used when generating the swagger JSON document later on.
Both British and American spelling supported.

**Parameters**

-   `app` **[object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)** The express app class that you want to describe using swagger
-   `options.externalDocs.url` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** Required. The URL for the target documentation. Value MUST be in the format of a URL.
-   `options.document` **\[[object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)]** An existing or manually created swagger document to use as a base document and expanded upon. Note that the other options will override the base items in this supplied document. See <http://swagger.io/specification/> for info on how to manually construct a document.
-   `options.title` **\[[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)]** The title of the application.
-   `options.description` **\[[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)]** A short description of the application. [GFM syntax](<A short description of the application. GFM syntax can be used for rich text representation.>) can be used for rich text representation.
-   `options.termsOfService` **\[[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)]** The Terms of Service for the API.
-   `options.contact` **\[[object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)]** The contact information for the exposed API. See [Contact Object](http://swagger.io/specification/#contactObject).
    -   `options.contact.name` **\[[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)]** The identifying name of the contact person/organization.
    -   `options.contact.url` **\[[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)]** The URL pointing to the contact information. MUST be in the format of a URL.
    -   `options.contact.email` **\[[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)]** The email address of the contact person/organization. MUST be in the format of an email address.
-   `options.license` **\[[object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)]** The license information for the exposed API. See [License Object](http://swagger.io/specification/#licenseObject).
    -   `options.license.name` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** Required. The license name used for the API.
    -   `options.license.url` **\[[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)]** A URL to the license used for the API. MUST be in the format of a URL.
-   `options.version` **\[[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)]** Provides the version of the application API.
-   `options.host` **\[[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)]** The host (name or ip) serving the API. This MUST be the host only and does not include the scheme nor sub-paths. It MAY include a port. If the host is not included, the host serving the documentation is to be used (including the port). The host does not support [Path Templating](http://swagger.io/specification/#pathTemplating).
-   `options.basePath` **\[[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)]** The base path on which the API is served, which is relative to the host. If it is not included, the API is served directly under the host. The value MUST start with a leading slash (/). The basePath does not support [Path Templating](http://swagger.io/specification/#pathTemplating). (optional, default `/`)
-   `options.schemes` **\[[Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)>]** The transfer protocol of the API. Values MUST be from the list: "http", "https", "ws", "wss". If the schemes is not included, the default scheme to be used is the one used to access the Swagger definition itself.
-   `options` **[object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)** The options object, used to control how the swagger document will be generated
    -   `options.produces` **\[[Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)>]** A list of MIME types the APIs can produce. This is global to all APIs but can be overridden on specific API calls. Value MUST be as described under [Mime Types](http://swagger.io/specification/#mimeTypes).
    -   `options.paths` **\[[object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)]** The available paths and operations for the API. See [Paths Object](http://swagger.io/specification/#pathsObject).
    -   `options.definitions` **\[[object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)]** An object to hold data types produced and consumed by operations. See [Definitions Object](http://swagger.io/specification/#definitionsObject).
    -   `options.parameters` **\[[object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)]** An object to hold parameters that can be used across operations. This property does not define global parameters for all operations. See [Parameter Definitions Object](http://swagger.io/specification/#parametersDefinitionsObject).
    -   `options.responses` **\[[object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)]** An object to hold responses that can be used across operations. This property does not define global responses for all operations. See [Response Definitions Object](http://swagger.io/specification/#responsesDefinitionsObject).
    -   `options.securityDefinitions` **\[[object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)]** Security scheme definitions that can be used across the specification. See [Security Definitions Object](http://swagger.io/specification/#securityDefinitionsObject).
    -   `options.security` **\[[object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)]** A declaration of which security schemes are applied for the API as a whole. The list of values describes alternative security schemes that can be used (that is, there is a logical OR between the security requirements). Individual operations can override this definition. See [Security Requirement Object](http://swagger.io/specification/#securityRequirementObject).
    -   `options.defaultSecurity` **\[([string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String) \| [Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)>)]** The default security schema to use on a route when the security parameter is set to **true**. Must be a single [Security Requirement Object](http://swagger.io/specification/#securityRequirementObject).
    -   `options.tags` **\[[object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)]** A list of tags used by the specification with additional metadata. The order of the tags can be used to reflect on their order by the parsing tools. Not all tags that are used by the [Operation Object](swagger.io/specification/#operationObject) must be declared. The tags that are not declared may be organized randomly or based on the tools' logic. Each tag name in the list MUST be unique. See [Tag Object](http://swagger.io/specification/#tagObject).
        -   `options.tags.name` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** Required. The name of the tag.
        -   `options.tags.description` **\[[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)]** A short description for the tag. GFM syntax can be used for rich text representation.
        -   `options.tags.externalDocs` **\[[object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)]** Additional external documentation for this tag.
            -   `options.tags.externalDocs.description` **\[[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)]** A short description of the target documentation. GFM syntax can be used for rich text representation.
            -   `options.tags.externalDocs.url` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** Required. The URL for the target documentation. Value MUST be in the format of a URL.
    -   `options.externalDocs` **\[[object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)]** Additional external documentation. See [External Documentation Object](http://swagger.io/specification/#externalDocumentationObject).
        -   `options.externalDocs.description` **\[[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)]** A short description of the target documentation. GFM syntax can be used for rich text representation.
    -   `options.consumes` **\[[Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)>]** A list of MIME types the APIs can consume. This is global to all APIs but can be overridden on specific API calls. Value MUST be as described under [Mime Types](http://swagger.io/specification/#mimeTypes).

**Examples**

_Minimal_

```javascript
var express = require('express');
var swagger = require('swagger-spec-express');
var packageJson = require('./package.json');
var app = express();

swagger.initialise(app, options);
```

_Extensive_

```javascript
var express = require('express');
var swagger = require('swagger-spec-express');
var packageJson = require('./package.json');
var app = express();
var options = {
   document: {
       // An existing swagger json spec file that you may want to build on
   },
   title: packageJson.title,
   description: packageJson.description,
   termsOfService: 'You may only use this api for reasons!',
   contact: {
       name: '',
       url: '',
       email: ''
   },
   license: {
       name: '',
       url: ''
   },
   version: packageJson.version,
   host: 'localhost',
   basePath: '/',
   schemes: ['http', 'https'],
   consumes: ['application/json'],
   produces: ['application/json'],
   paths: {
       //manual paths here if desired, not required.
   },
   definitions: {
       //manual definitions here if desired, not required.
   },
   parameters: {
       //manual definitions here if desired, not required.
   },
   responses: {
       //manual responses here if desired, not required.
   },
   securityDefinitions: {
       basicAuth: {
           type: "basic",
           description: "HTTP Basic Authentication. Works over HTTPS"
       }
   },
   security: [{basicAuth: []}],
   defaultSecurity: "basicAuth",
   tags: [
       {
           name: 'My Manual Tag',
           description: 'Manually added to swagger',
           externalDocs: {
               description: 'This doc describes how to make tags',
               url: 'https://github.com/eXigentCoder/swagger-spec-express'
           }
       }
   ],
   externalDocs: {
       description: 'This doc describes how to use swagger spec express',
       url: 'https://github.com/eXigentCoder/swagger-spec-express'
   }
};
swagger.initialise(app, options);
```

-   Throws **[Error](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error)** if already initialised, should call reset first if reinitialisation required
-   Throws **[Error](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error)** if no app object provided
-   Throws **[Error](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error)** if no options object provided

Returns **void** 

## Reporting Bugs & Issues

_todo_

## Integrating to your tests

_todo_

## Awesome code reuse

_todo_

## Roadmap

### Low

-   Currently populating rootDocument.responses but then injecting the responses directly into the operation. Should use the $ref
-   Currently populating rootDocument.parameters but then injecting the responses directly into the operation. Should use the $ref
    ### Medium
-   Ability to describe the app like a router (alternate to passing all the data into the init method).
-   Ability to manually specify routes.
-   More tests
    ### High
-   addResponse should look for model & arrayOfModel
-   Better error messages in general so you don't need to debug
-   Ability to inject certain things into parameters. E.g. many GET routes will have GET /thing/:identifier would be nice to inject the name in there
-   Add examples to this document
-   when using arrayOfModel the schema doesn't have an id, so makes it harder to use ajv. Currently we just wrapping in {items:}. Still an issue?
-   custom fields not on all schemas?
-   proper error classes for specific errors to allow filtering and handling

[spe-npm-icon]: https://nodei.co/npm/swagger-spec-express.svg

[spe-npm-url]: https://npmjs.org/package/swagger-spec-express

[spe-ci-image]: https://travis-ci.org/eXigentCoder/swagger-spec-express.svg?branch=master

[spe-ci-url]: https://travis-ci.org/eXigentCoder/swagger-spec-express
