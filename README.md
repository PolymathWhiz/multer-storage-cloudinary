# Multer Storage Cloudinary
A multer storage engine for Cloudinary. Also consult the [Cloudinary API](https://github.com/cloudinary/cloudinary_npm).

## Installation

```sh
npm install --save multer-storage-cloudinary
```

## Usage

```javascript
var cloudinary = require('cloudinary');
var cloudinaryStorage = require('multer-storage-cloudinary');
var express = require('express');
var multer = require('multer');

var app = express();

var storage = cloudinaryStorage({
  cloudinary: cloudinary,
  folder: 'folder-name',
  allowedFormats: ['jpg', 'png'],
  filename: function (req, file, cb) {
    cb(undefined, 'my-file-name');
  }
});

var parser = multer({ storage: storage });

app.post('/upload', parser.array('images', 10), function (req, res) {
  console.log(req.files);
});
```

### File properties

File objects will expose (at least) the following properties provided by the [Cloudinary API](https://github.com/cloudinary/cloudinary_npm#upload):

Key | Description
--- | ---
`public_id` | Name of the file
`format` | Extension of the stored file
`url` | A URL for fetching the file
`secure_url` | A secure URL for fetching the file
`resource_type` | The type of the file
`bytes` | Size of the file
*NOTE: Many properties depend on the file type, to find out about the other exposed properties please check the Cloudinary API reference.*

### Options

Upload parameters can be configured using the options object passed to the `multer-storage-cloudinary` function call.

```javascript
var cloudinaryStorage = require('multer-storage-cloudinary');

var storage = cloudinaryStorage({
  // configuration options
});
```

All parameters are optional except the configured Cloudinary API object:

Parameter | Description | Type
--- | --- | ---
`options.cloudinary` | A Cloudinary API object <br>The API must be configured by the user | `object` <br>**required**
`options.filename` | The name of the file to be uploaded | `function`
`options.folder` | The name of the Cloudinary folder to upload into | `string` or `function`
`options.transformation` | Transformations to be applied - [Cloudinary API](https://github.com/cloudinary/cloudinary_npm#additional-resources) | `array` or `function`
`options.type` | Storage mode | `string` or `function`
`options.format` | The format to convert the file into | `string` or `function`
`options.allowedFormats` | A formats filter | `array` or `function`
`options.params` | An overriding object to directly supply all parameters | `object` or `function`
*NOTE: For further guidance on all parameters: consult the Cloudinary documentation.*

Functional parameters are called on every request and can be used in the following way:

```javascript
var cloudinary = require('cloudinary');
var cloudinaryStorage = require('multer-storage-cloudinary');

var storage = cloudinaryStorage({
  cloudinary: cloudinary,
  folder: function(request, file, callback) {
    callback(undefined, 'my-value');
  }
});
```
#### options.params
All Cloudinary upload parameters can be configured altogether using this. If a non-null value is provided(either directly or through the callback) all other options will be overriden.

Example:
```javascript
var cloudinary = require('cloudinary');
var cloudinaryStorage = require('multer-storage-cloudinary');

var storage = cloudinaryStorage({
  cloudinary: cloudinary,
  folder: 'folder', // will not be used
  format: 'jpg', // will not be used
  params: {
    folder: 'foo',
    format: 'png'
  }
});

var anotherStorage = cloudinaryStorage({
  cloudinary: cloudinary,
  folder: 'folder',
  format: 'jpg',
  params: function(req, file, cb) {
    var foo = undefined;
    cb(undefined, foo); // other options will not be overriden
                        // as long as foo is null
  }
});
```

## Testing

The Cloudinary API must be configured using the `CLOUDINARY_URL` environment variable in order to run the tests.
All test files are stored in a seperate Cloudinary folder, which is deleted after tests finish.

```sh
npm test
```
