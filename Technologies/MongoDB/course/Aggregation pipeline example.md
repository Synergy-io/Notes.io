- This example updates all book documents with a new “product type” field to identify the shape of the ebook, printed book, and audiobook documents.
- The pipeline also adds a `product_id` field for all documents and makes shared attributes more consistent.
- It does this by changing `desc` and `details` fields to `description` and by converting the `author` field to an array. 
- Finally, it replaces the existing documents in the same collection.

```json
var apply_inheritance_pattern_to_books_pipeline = [
  {
    $project: {
      _id: "$_id",
      product_id: "$product_id",
      product_type: {
        $ifNull: ["$product_type", "Unspecified"],
      },
      description: {
        $ifNull: [
          "$desc",
          "$description",
          "$details",
          "Unspecified",
        ],
      },
      authors: {
        $ifNull: [
          "$authors",
          ["$author"],
          "Unspecified",
        ],
      },
      publisher: "$publisher",
      language: "$language",
      pages: "$pages",
      catalogues: "$catalogues",
      eformats: "$eformats",
      isbn10: "$isbn10",
      isbn13: "$isbn13",
      narrator: "$narrator",
      length_minutes: "$length_minutes",
    },
  },
  {
    $merge: {
      into: "books",
      on: "_id",
      whenMatched: "replace",
      whenNotMatched: "discard",
    },
  },
]

db.books.aggregate(apply_inheritance_pattern_to_books_pipeline)
```

- The next pipeline changes all documents with a product type of “unspecified” with a minute length greater than zero to “audiobook” and saves them to the collection.

```json
var cleanup_audiobook_entries_in_book_pipeline = [
  {
    $match: {
      $and: [{ product_type: "Unspecified" }, { length_minutes: { $gte: 0 } }],
    },
  },
  {
    $set: { product_type: "audiobook" },
  },
  {
    $merge: {
      into: "books",
      on: "_id",
      whenMatched: "replace",
      whenNotMatched: "discard",
    },
  },
];

db.books.aggregate(cleanup_audiobook_entries_in_book_pipeline)
```