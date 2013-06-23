# The Validation Tool

This tools is a basic validation tool i made for another node.js project. It's main purpose is to make a general purpose request input validator for the express.js applications.

It uses a schema - inspired by the MongoDB schemas - to create templates for how incoming data should be structured. It then validates and throws an exception if validation did not succeed.

And example of how to use the validator goes like this:

    var validator = require('validator');
    
    var newUserValidationSchema = {
        "user": { type: String, required: true },
        "password": { type: String, required: true }
    }
    
    app.post("/users", validator.validateBody(newUserValidationSchema), function(req, res) {
        
        // req.body is now validated and no further validation needs to take place.
        
        // If body could not be validated, and exception has been thrown.
        
    });

The above example is just a simple example, and the validator supports many different parameters depending on the type specified.

## Type shortcuts

Some types can be made with shortcuts. Instead of specifying the type, you just use the type. This currently works with objects and arrays.

Notice that we in the above example used the object shortcut for the root schema. If we hadn't it would have looked like this:

    var newUserValidationSchema = {
        type: Object,
        schema: {
            "user": { type: String, required: true },
            "password": { type: String, required: true}
        }
    }

### Objects

Objects shortcuts are used like this:

    {
        "user": { type: String }
    }

and is the same as:

    {
        type: Object,
        schema: {
        	"user": { type: String }
        }
    }

Which means that data should be an object with a `user` field of type `String`.

### Arrays

The same goes for arrays:

    [
        { type: String }
    ]

is essentially the same as:

    {
        type: Array,
        schema: { type: String }
    }

Which means data must be an array of strings.

## Supported types

Currently these types are supported by the validator:

 * Object
 * Array
 * String
 * Number
 * Boolean

There are some validators that are common to all types, and then some types have specific validators, which can be used to further specify data requirements.

### Common validators

These validators are supported by all types.

#### Default validator
Type: `Boolean`

This validator specifies a default value for fields.

Example:

    "email": { type: String, default: "email@not.set" }

This tells the validator, that an `email` field is expected, and if it is not found, it should just assign it with whatever is specified using default.

This also works with - as an example - booleans:

    "receive-newsletter": { type: Boolean, default: true }

Now if the `receive-newsletter` field has not been specified, the validator will default it to `true`.

#### Required validator
Type: `Boolean`

Required works a bit like default. But instead of replacing the value if the field is absent, we instead throw an exception.

Example is like the first example giving:

    "user": { required: true }

If both the `required` and `default` is set on a validation schema then `required` is ignored and the default value will just be inserted.

### Type specific validators

#### Array validators

Arrays has two validator besides the common validators.

##### Len validator
Type: `Number` or `String`

This ensures that an array has a specific number of items. This can be either a number or a range. The validator throws an exception if the array length is outside the specified range.

Example 1 shows an array that should have exactly 2 elements:

    {
        type: Array,
        len: 2,
        schema: { … }
    }

Exampe 2 shows an array that should have at least 2 elements:

    {
        type: Array,
        len: '2-',
        schema: { … }
    }

Example 3 shows an array that should have a maximum of 2 elements:

    {
        type: Array,
        len: '-2',
        schema: { … }
    }

Example 4 shows an array that should have at least 2 elements and a maximmum of 5:

    {
        type: Array,
        len: '2-5',
        schema: { … }
    }

Finally example 5 shows an advanced usage. In this example the array must have at two or less items or exactly 5 items or 8 or more items:

    {
        type: Array,
        len: '-2,5,8-',
        schema: { … }
    }

##### Unique validator
Type: `Boolean`

This ensures that all elements in the array are unique. If two or more elements are the same, the validator will throw an exception.

Example:

    {
        type: Array,
        unique: true,
        schema: { … }
    }

#### String validators

Strings has one validator besides the common validators.

##### Match validator
type: `RegExp`

This ensures that a string can be matched against a regular expressions pattern. The validator throws an exception if the string does not match the pattern.

Example shows a string that must contain at least one latin letter or numbers:

    { type: String, match /^[a-zA-Z0-9]+$/ }

## A note on automatic type conversion

### Numbers

If schema has type `Number` and the input holds a `String` containing numbers (or/and a point), the validator will automaticly convert that into a number.

### Booleans

Likewise will the schemas of type `Boolean` but with a type of `String` containing either `true` or `false` (only lowercase), the validator will automaticly convert that into the `Boolean` value.


## Express.js Middlewares

Express.js middleware can be created on the fly. Currently `validateBody` and `validateQuery` is supported.

Example of `validateBody`:

    var validator = require('validator');
    
    app.post('/user', validator.validateBody(schema), function(req, res) {
    	
    	// Body has been validated or an exception was thrown.
    	
    });

Example of `validateQuery`:

    var validator = require('validator');
    
    app.post('/user', validator.validateQuery(schema), function(req, res) {
    	
    	// Query has been validated or an exception was thrown.
    	
    });
