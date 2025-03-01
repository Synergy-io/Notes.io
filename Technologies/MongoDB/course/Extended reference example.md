 - have to lookup for product type for search review by book type query![[Pasted image 20250301111825.png]]
 - so embed required parts![[Pasted image 20250301111832.png]]
- improves read performance
---
- ![[Pasted image 20250301112022.png]]
- ![[Pasted image 20250301112033.png]]

---
#### Pipeline for this
- The following pipeline creates an extended reference by updating all documents in the `reviews` collection to include relevant product information from the `books` collection using the `$lookup` stage.
```json
var reshape_review_docs_pipeline = [
  {
    $lookup: {
      from: "books",
      localField: "product_id",
      foreignField: "product_id",
      as: "product_info",
    },
  },
  {
    $unwind: {
      path: "$product_info",
      includeArrayIndex: "string",
      preserveNullAndEmptyArrays: false,
    },
  },
  {
    $project: {
      _id: "$_id",
      "product.product_id": "$product_id",
      "product.product_type":
        "$product_info.product_type",
      "product.title": "$product_info.title",
      "review.user_id": "$user_id",
      "review.reviewTitle": "$reviewTitle",
      "review.reviewBody": "$reviewBody",
      "review.date": "$date",
      "review.stars": "$stars",
    },
  },
  {
    $merge: {
      into: "reviews",
      on: "_id",
      whenMatched: "replace",
      whenNotMatched: "discard",
    },
  },
]

db.reviews.aggregate(reshape_review_docs_pipeline)
```
- Finally, we run the aggregation pipeline by invoking the aggregate method on the reviews collection, passing in the pipeline variable.
```json
db.reviews.aggregate(reshape_review_docs_pipeline)
```

---
