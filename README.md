[![Bower version](https://badge.fury.io/bo/swissAngularSettings.svg)](http://badge.fury.io/bo/swissAngularSettings)
[![Build Status](https://travis-ci.org/ezraroi/swissAngularSettings.svg?branch=master)](https://travis-ci.org/ezraroi/swissAngularSettings)
[![Dependency Status](https://gemnasium.com/ezraroi/swissAngularSettings.svg)](https://gemnasium.com/ezraroi/swissAngularSettings)
[![Coverage Status](https://coveralls.io/repos/ezraroi/swissAngularSettings/badge.svg?branch=master)](https://coveralls.io/r/ezraroi/swissAngularSettings?branch=master)
[![Code Climate](https://codeclimate.com/github/ezraroi/swissAngularSettings/badges/gpa.svg)](https://codeclimate.com/github/ezraroi/swissAngularSettings)
[![Built with Grunt](https://cdn.gruntjs.com/builtwith.png)](http://gruntjs.com/)

# Swiss Angular Settings

Angular provider which auto generates service for saving and getting values from the **local storage** based on a defined schema.

## Installation
You can install the swissAngularSettings with bower:
```bat
bower install swissAngularSettings --save
```

or you can add the swissAngularSettings.min.js file to your HTML page:
```html
<script src="swissAngularSettings.min.js"/>
```

Add the `swissAngularSettings` to your module dependencies

## Dependencies
The swissAngularSettings depends on the following libraries:
* angular
* [angular-local-storage](https://github.com/grevory/angular-local-storage)

## Usage

Use the `swissSettingsServiceProvider` to define the schema of your desired settings service.
The schema of the settings service is a list of typed fields which can have a default value.
Field can be from one the following types:
* String
* Boolean
* Number
* Object
* Array
* Enum (Defined list of values)

****The service will use the type of the field for input validation and parsing****

****All values are stored in the local storage of the browser****

### Defening your service schema
You can use the following methods of the `swissSettingsServiceProvider` to define your service schema:

* `registerBooleanField(name, [defaultValue], [storageDuration])` - Adding a boolean field to your schema with the name `name` and the default value `defaultValue` (optional).
* `registerStringField(name, [defaultValue], [storageDuration])` - Adding a string field to your schema with the name `name` and the default value `defaultValue` (optional).
* `registerNumberField(name, [defaultValue], [storageDuration])` - Adding a number field to your schema with the name `name` and the default value `defaultValue` (optional).
* `registerArrayField(name, [defaultValue], [storageDuration])` - Adding an array field to your schema with the name `name` and the default value `defaultValue` (optional).
* `registerEnumField(name, allowedValues, [defaultValue], [storageDuration])` - Adding an enum field to your schema with the name `name` and the default value `defaultValue` (optional). The field will allow to save only the values defined `allowedValues` **array**
* `registerObjectField(name, [defaultValue], [storageDuration])` - Adding an object field to your schema with the name `name` and the default value `defaultValue` (optional).

The `storageDuration` is a moment Duration object

Example:
```javascript
    angular.module('myApp')
        .config(configure);

    function configure(swissSettingsServiceProvider) {
        swissSettingsServiceProvider.registerBooleanField('myBoolField', true);
        swissSettingsServiceProvider.registerBooleanField('myBoolField2');
        swissSettingsServiceProvider.registerNumberField('myNumField');
        swissSettingsServiceProvider.registerEnumField('myEnumField', ['a','b'], 'b');
        swissSettingsServiceProvider.registerEnumField('myTimeBasedField', ['a','b'], 'b', moment.duration(5, 'seconds'));
    }
```

### Saving time based values
You can define in your field how much time after setting the value the value should be ignored and return to default value. You can do this by passing a `moment.duration` object to the field constractor  

### Using the auto generated service
For each field you define in your schema, the `swissSettingsService` will have 2 methods:

* `get<FieldName>()` - Gets the last saved value from the local storage or the default one if defined.
* `set<FieldName>(value)` - Saves the given value to the local storage.

** The `FieldName` is the name given in the field defenition with the first char as capital letter **

Example:
```javascript
    angular
        .module('myApp')
        .controller('Main', Main);

    function Main( swissSettingsService) {
        var vm = this;
        swissSettingsService.getMyBoolField(); // true - the default value
        swissSettingsService.getMyBoolField2(); // undefined - no default value defained
        swissSettingsService.getMyEnumField(); // 'b' - the default value
        swissSettingsService.setMyNumField('string'); // error - should be number
        swissSettingsService.setMyBoolField(false); 
        swissSettingsService.getMyBoolField(); // false
    }
```

### Upgrading application versions
Sometimes you mke some changes in your application that requieres that will not be compatible with the old way you was saving your application seeitngs. The `swissAngularSettings` provides a mechanism for such cases.

You can call the `setVersion` method of the `swissAngularSettingsProvider` with a key, if the key is different from the previous time the application started, the local storage will be cleaned
