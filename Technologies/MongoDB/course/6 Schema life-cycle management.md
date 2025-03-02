# 1. Schema validation
## Validation rules
- to make sure there are no unintended structures
- create validation rules
	- define when creating the collection
		- then enforce it on inserts and updates
	- add to an existing collection
		- all newly added documents are validated
		- previous documents are validated only when they are being updated

- $jsonSchema
- query operator
```json
db.createCollection("sales", {
    validator: {
      "$and": [ // queery operator
        {
          "$expr": {
            "$lt": ["$items.discountedPrice", "$items.price"]
          }
        },
        {
          "$jsonSchema": {  // json schema
            "properties": {
              "items": { "bsonType": "array" }
            }
           }
         }
       ]
     }
   }
 )
```
## Validation levels
- **strict**
	- applies rules to all inserted and updated documents
- **moderate**
	- applies rules to new and existing valid documents
## Validation actions
- what to do when validation fails
- 2 options
	- **Error** - default
		- reject update or insert operation
	- **Warn**
		- allow operation to proceed and record validation failure in log
## Example
- To find all the documents that do not match a validator expression
```json
db.collection.find( { $nor: [ validator ] } )
```
- This is the reviews validator used for our bookstore:
```js
const bookstore_reviews_default = {
    bsonType: "object",
    // mandatory fields
    required: [“_id”, "review_id", "user_id", "timestamp", "review", "rating"], 
    // allow more fields ? 
    additionalProperties: false,
    // of given fields
    properties: {
        _id: { bsonType: "objectId" },
        review_id: { bsonType: "string" },
        user_id: { bsonType: "string" },
        timestamp: { bsonType: "date" },
        review: { bsonType: "string" },
        rating: {
            bsonType: "int",
            minimum: 0,
            maximum: 5,
        },
        comments: {
            bsonType: "array",
            maxItems: 3,
            items: {
                bsonType: "object",
            },
        },
    },
};
```
- To enable or update schema validation for an existing collection use the `collMod` command providing the validator option. In our example, we used the `$jsonSchema` operator to build the validator. We also overrided the default validation level and validation action options to be `strict` and `error` respectively:
```js
const schema_validation = { $jsonSchema: bookstore_reviews_default };

db.runCommand({
    collMod: "reviews",
    validator: schema_validation,
    validationLevel: "strict",
    validationAction: "error",
});
```
# 2. Schema evolution
- can be
	- part of planned update
	- ad hoc during development

- monitoring schema validation
	- identifying schema design issues
	- improving data quality
	- optimizing performance
- use logs 
## Example - adding new locale
```js
const bookstore_reviews_international = {
    bsonType: "object",
    required: [“_id”, "review_id", "user_id", "timestamp", "review", "rating"],
    additionalProperties: false,
    properties: {
        _id: { bsonType: "objectId" },
        review_id: { bsonType: "string" },
        user_id: { bsonType: "string" },
        timestamp: { bsonType: "date" },
        review: { bsonType: "string" },
        rating: {
            bsonType: "int",
            minimum: 0,
            maximum: 5,
        },
        comments: {
            bsonType: "array",
            maxItems: 3,
            items: {
                bsonType: "object",
            },
        },
        locale: { 
            enum: [“en”, “fr”]},  // allowed values
        },
    },
};
```
- To enable or update schema validation for the existing collection, use the `collMod` command and provide the validator option. In our example, we used the `$jsonSchema` operator to build the validator and set the validation level and validation action options to `moderate` and `warn`:
```js
const schema_validation_international = 
{ $jsonSchema: bookstore_reviews_international };

db.runCommand({
    collMod: "reviews",
    validator: schema_validation_international,
    validationLevel: "moderate",
    validationAction: "warn",
});
```
# 3. Schema migration
- transition from one schema to next
- schema acts like a contract between users
- so requires coordination when migrating

- schema versioning pattern
	- but increases application complexity
	- so limit number of versions

- migration strategies
	- eager - all at once
	- lazy - as data is used
	- incremental - small iterative steps
	- predictive - update the schema based on predictions for future data usage
## Example
- default schema:
```js
const bookstore_reviews_default = {
    bsonType: "object",
    required: ["_id", "review_id", "user_id", "timestamp", "review", "rating"],
    additionalProperties: false,
    properties: {
        _id: { bsonType: "objectId" },
        review_id: { bsonType: "string" },
        user_id: { bsonType: "string" },
        timestamp: { bsonType: "date" },
        review: { bsonType: "string" },
        rating: {
            bsonType: "int",
            minimum: 0,
            maximum: 5,
        },
        comments: {
            bsonType: "array",
            maxItems: 3,
            items: {
                bsonType: "object",
            },
        },
    },
};
```
- new version of the schema includes the `locale` field: 
```js
const bookstore_reviews_international = {
    bsonType: "object",
    required: [
         "_id",
        "review_id",
        "user_id",
        "timestamp",
        "review",
        "rating",
        "locale",
        "schema_version"
    ],
    additionalProperties: false, // only this or that can be used
    properties: {        
        _id: { bsonType: "objectId" },
        review_id: { bsonType: "string" },
        user_id: { bsonType: "string" },
        timestamp: { bsonType: "date" },
        review: { bsonType: "string" },
        rating: {
            bsonType: "int",
         minimum: 0,
            maximum: 5,
        },
        comments: {
            bsonType: "array",
            maxItems: 3,
            items: {
                bsonType: "object",
            },
        },
        locale: {
          enum: ["en", "fr"],
        },
        schema_version: {
          enum: ["1.0.0"],  // limit number of verisons
        },
    },
};
```
- To enable schema validation for both schemas simultaneously, we used the `$jsonSchema` operator and the ==`oneOf`== keyword:
```js
const schema_migration_validation = { 
    $jsonSchema: { 
       oneOf: [
         bookstore_reviews_default,
         bookstore_reviews_international
       ]
    }
};
```
- To update the schema validation rules for the existing collection we used the `collMod` command
```json
db.runCommand({
    collMod: "reviews",
    validator: schema_migration_validation,
    validationLevel: "strict",
    validationAction: "error",
});
```
- strict and error for production environment