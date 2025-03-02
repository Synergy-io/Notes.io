#### 1. Inheritance pattern
- different forms of similar entities can be exist in same collection - Polymorphism
- shared fields across child entities
- child entities can have unique fields

- use this pattern
	- when documents have more similarities than differences
	- and want to keep in same collection so we can read them together
- can keep doc type attribute so the application can keep track of
	- document shape 
	- what fields to expect

- ==aggregation framework== comes in handy when we want to transform existing data model to inheritance pattern
	- [[Aggregation framework]]
	- [[Aggregation pipeline example]]
#### 2. Computed pattern
- frequent computations can decrease performance

- this pattern allows to run expensive operations when the data changes
- and store the results to quick access
- common computations
	- #### mathematical
		- pre-calculate results on write
	- #### roll up
		- merging data together
		- view data as a whole
		- categorize
		- Eg:
			- total number of books from the product type
			- average number of authors from product type
		- idea is instead of calculating them at every read, keep separate document for that and update it in special intervals
			- Eg: updating daily - depends on business requirements
		- have to tolerate some stale data
- [[Computed pattern exampleb| Example]]
#### 3. Approximation pattern
- generate statistically valid number that is not exactly correct

- use when
	- data is difficult or expensive to calculate
	- getting the exact number is not critical
	- working with big data

- many methods
- generate random number every time new document is added, or data changed, and do calculation only when that number is higher than some value
- [[Approximation example |Example]]
#### 4. Extended reference pattern
- joins / $lookup is resource intensive
- use this pattern to reduce or eliminate joins

- Embed relevant data from multiple collections into the main document relevant

- avoids round trips to the database
- avoid touching too many pieces of data

- gives faster reads

- duplication is an issue

- select fields that don't change
- bring only fields you need

- [[Extended reference example|Example]]

- for manage dependencies
	- first identify what is the list of dependent extended references
	- should they be updated immediately or they can be updated at a later time
#### 5. Schema versioning pattern
- deal with changes of schema
- add field for schema version![](https://raw.githubusercontent.com/Synergy-io/Notes.io/main/assets/Pasted%20image%2020250301113847.png)
- how to update to new shape
	- by application
	- by background task
- before update schema, need to update application so it can read every schema.
	- no downtime
- ![](https://raw.githubusercontent.com/Synergy-io/Notes.io/main/assets/Pasted%20image%2020250301114221.png)
