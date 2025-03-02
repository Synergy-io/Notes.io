# 1. Unbounded arrays
-  Risk of BSON document size limit of 16 MB
- decrease index performance

- ==only store data together if it required together==
- array should not grow without bound
- high cardinality array should not be embedded

- extended reference pattern 
	- embed relevant data from multiple documents and different collections into the main document
- subset pattern
	-  relocate data not frequently accessed

- depend on the use case
---
# 2. Bloated documents
- number of fields getting bigger and we keep adding fields instead of refactoring the schema
- this is called bloating
- here, data is related , but accessed separately

- this increase the size of working set
- that impact the performance
- ![[Pasted image 20250302072605.png]]
- WT internal cache size = 50%(RAM - 1 GB)  or 256 MB
	- whatever is larger of them
- logical data size = # of documents x average document size
	- db.collection.stats().count
	- db.collection.stats().avgObjSize

- so will have to
	- increase cluster memory 
	- or improve existing memory usage

- so we will have to separate the document so that separately accessed parts are stored separately

## Example
- ![[Pasted image 20250302073200.png]]
- ![[Pasted image 20250302073232.png]]
- ![[Pasted image 20250302073245.png]]
- Solution: 
- ![[Pasted image 20250302073327.png]]

---
# 3. Massive number collections
- in real world scenarios
	- when # of collections grow, performance tend to decrease
	- recommended # is 10,000
	- but it depend on workload and amount of 

- every collection have at least one index file
- in atlas degrade performance
	- M10 cluster - 5000 collections
	- M20/M30 - 10,000 collections
	- replica set - 10,000
	- 10,000 per shard
	- https://learn.mongodb.com/learn/course/schema-design-anti-patterns/lesson-3-massive-number-of-collections/first-lesson?client=customer

- regularly monitor database for collections that aren't being used
- re model data
- sharding 
---
# 4. Unnecessary indexes
- index is unnecessary when
	- it is not used
	- rarely used
	- covered by another compound index
- limit for # indexes of a collections is 64 - but this is way higher
- but use fewest number of indexes possible
	- need space 
	- need to be loaded to memory
	- grow when collection grow
	- can impact performance
- indicators of suboptimal indexing stategy
	- slow queries 
	- slow writes

- handle using data explorer

- we can hide index before dropping it

- To obtain index usage stats for a collection, use the `$indexStats` aggregation stage. The following aggregation pipeline lists the `name`, `ops` and `since` fields for the indexes in the collection and sorts them by number of times they have been used:
```js
var sortIndexesByAccessOpsPipeline =  [ 
{ $indexStats: {} },
{ $project: { 
_id: 0,
name: "$name",
ops: "$accesses.ops",
since: "$accesses.since" }
},
{ $sort: { ops: 1 } }
]
```
---
# 5. Data normalization
- normalizing data that accessed together will cause
	 - multiple queries for one operation 
	- $lookup
- can negatively impact performance

- subset pattern
- extended reference pattern
---
# 6.  Case-sensitivity
- search might not work when not in exact case order
- database queries does exact match on search
- MongoDB queries are case sensitive by default

- use collation
	- language specific rules
	- determine how characters are compared and sorted
	- specify locale for desired language
		- Eg: locale: 'en'
	- strength 1-5
		- default 3
		- case insensitive: 1,2
			- 1 - compare base chars ony
			- ![[Pasted image 20250302081530.png]]
			- 2 - include secondary differences and diacritics
			- ![[Pasted image 20250302081615.png]]
		
- can make index and query has same collation
	- and specify collation in collation
- can also assign default collation to collection when creating
	- default collation applies to all indexes
	- cannot be changed
	- queries use same collation
	- can override when using queries
- $regex with /i option
	- regex with indexes can be used when search for exact matches
	- not very efficient for case insensitive
		- do not use collation used index
	- so not recommended

- have to match the index case sensitivity strength with query strength, else index will not be used
	- will have to recreate index
## Example
- To create a case-insensitive index on the `author` field for an existing collection, we used the following:
```js
db.Books.createIndex(
   { author: 1 },
   {
       collation: {
           locale: 'en',
           strength: 2
       }
   }
)
```
- Now to run a case-insensitive query, we ran the the following query with the same collation:
```js
db.Books.find({ author: 'Paul Done' }).collation({locale: 'en', strength: 2})
```
- To check the existing indexes on a collection
	- .getIndexes() command