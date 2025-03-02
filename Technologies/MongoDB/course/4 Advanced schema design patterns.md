https://learn.mongodb.com/learn/course/advanced-schema-design-patterns
# 1. Single collection pattern
- to optimize performance
	- need to use few queries as possible 
	- avoid $lookup operations
- embedding can exceed 16 MB / unbound array 
- but saving in different collections can impact performance
	- can cause 
		- $lookups 
		- or multiple queries

- this pattern groups related documents of different type into a single collection
	- avoid multiple queries to read non embedded related documents 
	- avoid $lookup
- particularly useful in modelling ==many to many relationships==
- and one to many relationships
---
## Variant 1
- need to add
	- array of references
	- docType field
- ![](https://raw.githubusercontent.com/Synergy-io/Notes.io/main/assets/Pasted%20image%2020250301192115.png)
- see book references itself
- now ==have to index relatedTo array==
- ![](https://raw.githubusercontent.com/Synergy-io/Notes.io/main/assets/Pasted%20image%2020250301192258.png)
- ![](https://raw.githubusercontent.com/Synergy-io/Notes.io/main/assets/Pasted%20image%2020250301192315.png)
---
## Variant 2
- use overloaded field
	- use for other purpose other than it's original intent
- in this case
	- identify document
	- and to enable efficient queries
-  used to ==model one to many relationships==
- ![](https://raw.githubusercontent.com/Synergy-io/Notes.io/main/assets/Pasted%20image%2020250301192637.png)
- sc_id : single collection id
	- used to identify book
	- and review with book
- ==need to index with sc_id==
- user regex to query all related to sc_id
- to get books and reviews
	- ![](https://raw.githubusercontent.com/Synergy-io/Notes.io/main/assets/Pasted%20image%2020250301192918.png)
- to get only the reviews
	- ![](https://raw.githubusercontent.com/Synergy-io/Notes.io/main/assets/Pasted%20image%2020250301192934.png)
---
# 2. Subset pattern
- when document contains large number of embedded documents
- but only a small portion of them is frequently used

- MongoDB default storage engine
	- ![](https://raw.githubusercontent.com/Synergy-io/Notes.io/main/assets/Pasted%20image%2020250301204226.png)
- keeps data accessed frequently in memory
	- this set is called "wired tiger internal cache"
- ideally working set should fit in the cache
	- set of indexes and documents frequently used by application
	-  ![](https://raw.githubusercontent.com/Synergy-io/Notes.io/main/assets/Pasted%20image%2020250301204527.png)
	- if not, performance is impacted
	- ![](https://raw.githubusercontent.com/Synergy-io/Notes.io/main/assets/Pasted%20image%2020250301204614.png)
- use this pattern for cases like that

- reduces document size 
- using relocating data that is not frequently accessed
- so we can fit more documents in memory
- so performance is improved
- use case
	- when we have large number of embedded in document
	- and small subset of those documents is used by application

## Example
![](https://raw.githubusercontent.com/Synergy-io/Notes.io/main/assets/Pasted%20image%2020250301204959.png)
- duplicate data of 3 documents here
- but they are unlikely to change
- trade-off : duplication vs performance
- move reviews out of the book:
```json
[
  {
    $unwind: {
      path: "$reviews"
    }
  },
  {
    $set: {
      "reviews.product_id": "$product_id"
    }
  },
  {
    $replaceRoot: {
      newRoot: "$reviews"
    }
  },
  {
    $out: "reviews"
  }
]
```
- cleanup book documents
```json
   {
       reviews: {
           $slice: ["$reviews", 3]
       }
   }
];
```
- call method
```js
db.books.updateMany({}, update_reviews_array_pipeline);
```
---
# 3. Bucket pattern
- where embedding nor referencing is a good pattern

- groups pieces of information into buckets
- helps
	- keep document size predictable
	-  read only data we need
	- reduce number of documents in a collection
	- improve index performance
- need to know what queries will run so can make buckets in sensible way
- need a field to identify bucket according to granularity we need

- often use to handle sensor data
	- store each reading as a document and bucket them to easier organization and access
- common to use with computed pattern

## Example
- track user views of a book
- query for
	- monthly views for a book
	- ![](https://raw.githubusercontent.com/Synergy-io/Notes.io/main/assets/Pasted%20image%2020250301213122.png)
- ![](https://raw.githubusercontent.com/Synergy-io/Notes.io/main/assets/Pasted%20image%2020250301213227.png)
- avoid creating unbounded array in each bucket document
	- use schema validation tool to that
	- add application level logic
- views document
```json
{
  "id": {
    "book_id": 1,
    "month": {
      "$date": "2023-11-22T00:00:00.000Z"
    }
  },
  "views": [
    {
      "user_id": "user00",
      "timestamp": {
        "$date": "2023-11-22T00:00:00.000Z"
      }
    },
    {
      "user_id": "user10",
      "timestamp": {
        "$date": "2023-11-22T00:01:00.000Z"
      }
    },
  ]
}
```
- aggregation pipeline to set view count
```json
[
   {
       $group: {
           _id: {
               book_id: "$book_id",
               month: {
                   $dateFromParts: {
                       year: { $year: "$timestamp" },
                       month: { $month: "$timestamp" },
                       day: 1,
                   },
               },
           },
           views: {
               $push: {
                   user_id: "$user_id",
                   timestamp: "$timestamp",
               },
           },
       },
   },
   {
       $set: {
           views_count: { $size: "$views" },
       },
   }
]
```
---
# 4. Outlier pattern
- when app is in real world, unpredicted states can happen
- like when we plan limited number of elements will be in unbound array; but for some cases, it exceed document size.

- when some cases are different that need special handling
- but optimizing for edge cases might degrade performance

- like handling popular items on a e-commerce site
- or celebrities in social media sites

- need a threshold to identify outliers
- add 'outlier' field to document
- handle outliers using bucket pattern
## Example
- To apply the Outlier pattern to the review documents in our bookstore app, we added the `outlier` field to the documents in the reviews collection. This field is set to true when the comments array in a review document exceeds the three comments threshold:
```json
{
  "rating": 1,
  "userId": 318,
  "productId": 34538756,
  "review_text": "Lorem ipsum dolor sit amet...",
  "comments": [{...}, {...}, ... ],
  "outlier": true,
  ...
}
```
- To update the existing documents in the reviews collection we used the `updateMany` method with two arguments. First a filter to match all documents with a comments array larger than 3 elements. The second argument is an aggregation pipeline with $set operator to add the outlier field and set it to true.
```json
db.reviews.updateMany(
    {
        "comments.3": { $exists: true }
    },
    {
        $set: { outlier: true }
    }
);
```
---
# 5. Archive pattern
- when more and more data is accumulating, some data may not be retrieved or rarely accessed
- can affect performance
	- write operations need update all indexes
	- large indexes use memory
	- uses space of primary memory
		- which is expensive

- help move data that no longer accessed outside the main database
- copy source document to archive
	- but if document contains references, recreating whole hierarchy will be challenging
- use extended reference pattern

- then delete documents in main db
## Example
- archive reviews that are no longer accessed
- ![](https://raw.githubusercontent.com/Synergy-io/Notes.io/main/assets/Pasted%20image%2020250301222014.png)
- use extended reference pattern
- ![](https://raw.githubusercontent.com/Synergy-io/Notes.io/main/assets/Pasted%20image%2020250301222041.png)
- 