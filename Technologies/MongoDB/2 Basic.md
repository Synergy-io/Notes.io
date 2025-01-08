### create collection
- You can create a collection using the `createCollection()` database method.
- You can also create a collection during the `insert` process.
```
db.posts.insertOne(object)
```
- We are here assuming `object` is a valid JavaScript object containing post data

==**Remember:** In MongoDB, a collection is not actually created until it gets content!==

### Insert Documents

#### ### `insertOne()`
- To insert a single document, use the `insertOne()` method.
 ```jsx
db.posts.insertOne({
  title: "Post Title 1",
  body: "Body of post.",
  category: "News",
  likes: 1,
  tags: ["news", "events"],
  date: Date()
})
```

==**Note:** When typing in the shell, after opening an object with curly braces "{" you can press enter to start a new line in the editor without executing the command. The command will execute when you press enter after closing the braces.==

==**Note:** If you try to insert documents into a collection that does not exist, MongoDB will create the collection automatically.==

#### ### `insertMany()`
```jsx
db.posts.insertMany([  
  {
    title: "Post Title 2",
    body: "Body of post.",
    category: "Event",
    likes: 2,
    tags: ["news", "events"],
    date: Date()
  },
  {
    title: "Post Title 3",
    body: "Body of post.",
    category: "Technology",
    likes: 3,
    tags: ["news", "events"],
    date: Date()
  },
  {
    title: "Post Title 4",
    body: "Body of post.",
    category: "Event",
    likes: 4,
    tags: ["news", "events"],
    date: Date()
  }
])
```

### `find()`
- To select data from a collection in MongoDB, we can use the `find()` method.
```jsx
db.posts.find()
```
### `findOne()`
- To select only one document, we can use the `findOne()` method.
```jsx
db.posts.findOne()
```
## Querying Data
- To query, or filter, data we can include a query in our `find()` or `findOne()` methods.
```jsx
db.posts.find( {category: "News"} )
```
## Projection
- standard projection
```jsx
db.posts.find({}, {title: 1, date: 1})
```
==Notice that the `_id` field is also included. This field is always included unless specifically excluded.==
```jsx
db.posts.find({}, {_id: 0, title: 1, date: 1})
```
==**Note:** You cannot use both 0 and 1 in the same object. The only exception is the `_id` field. You should either specify the fields you would like to include or the fields you would like to exclude.==

```jsx
db.posts.find({}, {category: 0})
```
- category field excluded
```jsx
db.posts.find({}, {title: 1, date: 0})
```
- this will cause an error

## Update Document

- To update an existing document we can use the `updateOne()` or `updateMany()` methods.
- The first parameter is a query object to define which document or documents should be updated.
- The second parameter is an object defining the updated data.
## `updateOne()`
- The `updateOne()` method will update the first document that is found matching the provided query.
```jsx
db.posts.updateOne( { title: "Post Title 1" }, { $set: { likes: 2 } } ) 
```
- if successful return id else null
```
{
  acknowledged: true,
  insertedId: ObjectId('676f99778fba659c82d66638'),
  matchedCount: 0,
  modifiedCount: 0,
  upsertedCount: 1
}
```
## Insert if not found
- If you would like to insert the document if it is not found, you can use the `upsert` option.
```jsx
db.posts.updateOne( 
  { title: "Post Title 5" }, 
  {
    $set: 
      {
        title: "Post Title 5",
        body: "Body of post.",
        category: "Event",
        likes: 5,
        tags: ["news", "events"],
        date: Date()
      }
  }, 
  { upsert: true }
)
```
## `updateMany()`
- Eg. Update `likes` on all documents by 1. For this we will use the `$inc` (increment) operator:
```jsx
db.posts.updateMany({}, { $inc: { likes: 1 } })
```

## Delete Documents

## `deleteOne()`
- The `deleteOne()` method will delete the first document that matches the query provided.
```jsx
db.posts.deleteOne({ title: "Post Title 5" })
```

## `deleteMany()`
The `deleteMany()` method will delete all documents that match the query provided.
```jsx
db.posts.deleteMany({ category: "Technology" })
```

## MongoDB Query Operators

### Comparison
- `$eq`: Values are equal
- `$ne`: Values are not equal
- `$gt`: Value is greater than another value
- `$gte`: Value is greater than or equal to another value
- `$lt`: Value is less than another value
- `$lte`: Value is less than or equal to another value
- `$in`: Value is matched within an array
### Logical
- `$and`: Returns documents where both queries match
- `$or`: Returns documents where either query matches
- `$nor`: Returns documents where both queries fail to match
- `$not`: Returns documents where the query does not match
### Evaluation
- `$regex`: Allows the use of regular expressions when evaluating field values
- `$text`: Performs a text search
- `$where`: Uses a JavaScript expression to match documents

---
## MongoDB Update Operators
### Fields
- `$currentDate`: Sets the field value to the current date
- `$inc`: Increments the field value
- `$rename`: Renames the field
- `$set`: Sets the value of a field
- `$unset`: Removes the field from the document
### Array
- `$addToSet`: Adds distinct elements to an array
- `$pop`: Removes the first or last element of an array
- `$pull`: Removes all elements from an array that match the query
- `$push`: Adds an element to an array
---
## Aggregation Pipelines

- Aggregation operations allow you to group, sort, perform calculations, analyze data, and much more.
- Aggregation pipelines can have one or more "stages". The order of these stages are important. Each stage acts upon the results of the previous stage.
```jsx
db.posts.aggregate([
  // Stage 1: Only find documents that have more than 1 like
  {
    $match: { likes: { $gt: 1 } }
  },
  // Stage 2: Group documents by category and sum each categories likes
  {
    $group: { _id: "$category", totalLikes: { $sum: "$likes" } }
  }
])
```
- ==Remember that the order of your stages matters. Each stage only acts upon the documents that previous stages provide.==
## Aggregation `$group`
- This aggregation stage groups documents by the unique `_id` expression provided.
- ==Don't confuse this `_id` expression with the `_id` ObjectId provided to each document.==
```jsx
db.listingsAndReviews.aggregate(
    [ { $group : { _id : "$property_type" } } ]
)
```
- This will return the distinct values from the `property_type` field.

## Aggregation `$limit`
- This aggregation stage limits the number of documents passed to the next stage.
```jsx
db.movies.aggregate([ { $limit: 1 } ])
```

## Aggregation `$project`
- This aggregation stage passes only the specified fields along to the next aggregation stage.
- ==This is the same projection that is used with the `find()` method.==
```jsx
db.restaurants.aggregate([
  {
    $project: {
      "name": 1,
      "cuisine": 1,
      "address": 1
    }
  },
  {
    $limit: 5
  }
])
```
- This will return the documents but only include the specified fields.
- Notice that the `_id` field is also included. This field is always included unless specifically excluded.
- We use a `1` to include a field and `0` to exclude a field.

## Aggregation `$sort`
- This aggregation stage groups sorts all documents in the specified sort order.
```jsx
db.listingsAndReviews.aggregate([ 
  { 
    $sort: { "accommodates": -1 } 
  },
  {
    $project: {
      "name": 1,
      "accommodates": 1
    }
  },
  {
    $limit: 5
  }
])
```
- This will return the documents sorted in descending order by the `accommodates` field.
- ==The sort order can be chosen by using `1` or `-1`. `1` is ascending and `-1` is descending.==

## Aggregation `$match`
- This aggregation stage behaves like a find. It will filter documents that match the query provided.
- Using `$match` early in the pipeline can improve performance since it limits the number of documents the next stages must process.
```jsx
db.listingsAndReviews.aggregate([ 
  { $match : { property_type : "House" } },
  { $limit: 2 },
  { $project: {
    "name": 1,
    "bedrooms": 1,
    "price": 1
  }}
])
```
- This will only return documents that have the `property_type` of "House".

## Aggregation `$addFields`
- This aggregation stage adds new fields to documents.
```jsx
db.restaurants.aggregate([
  {
    $addFields: {
      avgGrade: { $avg: "$grades.score" }
    }
  },
  {
    $project: {
      "name": 1,
      "avgGrade": 1
    }
  },
  {
    $limit: 5
  }
])
```
- This will return the documents along with a new field, `avgGrade`, which will contain the average of each restaurants `grades.score`.
- ==The original documents in the `restaurants` collection remain unchanged.==
- The aggregation pipeline processes the data **temporarily** and only outputs the specified result (5 documents in this case).

## Aggregation `$count`
- This aggregation stage counts the total amount of documents passed from the previous stage.
```jsx
db.restaurants.aggregate([
  {
    $match: { "cuisine": "Chinese" }
  },
  {
    $count: "totalChinese"
  }
])
```

## Aggregation `$lookup`
- This aggregation stage performs a left outer join to a collection in the same database.
- There are four required fields:
	- `from`: The collection to use for lookup in the same database
	- `localField`: The field in the primary collection that can be used as a unique identifier in the `from` collection.
	- `foreignField`: The field in the `from` collection that can be used as a unique identifier in the primary collection.
	- `as`: The name of the new field that will contain the matching documents from the `from` collection.
```jsx
db.comments.aggregate([
  {
    $lookup: {
      from: "movies",
      localField: "movie_id",
      foreignField: "_id",
      as: "movie_details",
    },
  },
  {
    $limit: 1
  }
])
```

## Aggregation `$out`
- This aggregation stage writes the returned documents from the aggregation pipeline to a collection.
- ==The `$out` stage must be the last stage of the aggregation pipeline.==
```jsx
db.listingsAndReviews.aggregate([
  {
    $group: {
      _id: "$property_type",
      properties: {
        $push: {
          name: "$name",
          accommodates: "$accommodates",
          price: "$price",
        },
      },
    },
  },
  { $out: "properties_by_type" },
])
```
- The first stage will group properties by the `property_type` and include the `name`, `accommodates`, and `price` fields for each. The `$out` stage will create a new collection called `properties_by_type` in the current database and write the resulting documents into that collection.

---

## Indexing & Search

- MongoDB Atlas comes with a full-text search engine that can be used to search for documents in a collection.
- [Atlas Search](https://www.mongodb.com/docs/atlas/atlas-search?utm_campaign=w3schools_mdb&utm_source=w3schools&utm_medium=referral) is powered by Apache Lucene.

## Creating an Index
- find

## Running a Query
- To use our search index, we will use the `$search` operator in our aggregation pipeline.
```jsx
db.movies.aggregate([
  {
    $search: {
      index: "default", // optional unless you named your index something other than "default"
      text: {
        query: "star wars",
        path: "title"
      },
    },
  },
  {
    $project: {
      title: 1,
      year: 1,
    }
  }
])
```
- The first stage of this aggregation pipeline will return all documents in the `movies` collection that contain the word "star" or "wars" in the `title` field.
- The second stage will project the `title` and `year` fields from each document.

---
## Schema Validation

- By default MongoDB has a flexible schema. This means that there is no strict schema validation set up initially.
- Schema validation rules can be created in order to ensure that all documents in a collection share a similar structure.

- MongoDB supports [JSON Schema](http://json-schema.org/) validation. The `$jsonSchema` operator allows us to define our document structure.
```jsx
db.createCollection("posts", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: [ "title", "body" ],
      properties: {
        title: {
          bsonType: "string",
          description: "Title of post - Required."
        },
        body: {
          bsonType: "string",
          description: "Body of post - Required."
        },
        category: {
          bsonType: "string",
          description: "Category of post - Optional."
        },
        likes: {
          bsonType: "int",
          description: "Post like count. Must be an integer - Optional."
        },
        tags: {
          bsonType: ["string"],
          description: "Must be an array of strings - Optional."
        },
        date: {
          bsonType: "date",
          description: "Must be a date - Optional."
        }
      }
    }
  }
})
```
- This will create the `posts` collection in the current database and specify the JSON Schema validation requirements for the collection.
