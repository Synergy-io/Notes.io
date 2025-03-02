- The `$unwind` operator deconstructs an array field from the input documents to output a document for each element. Each output document is the input document with the value of the array field replaced by the element. To use it, you must specify the field name and value using the following syntax:
```js
{ $unwind: <field path> }
```
- The `$replaceRoot` operator allows you to promote a subdocument to the top level of a document. To use it, you must specify the name of the field to promote using the following syntax:
```js
{
  $replaceRoot: {
    newRoot: <field>
  }
}
```
- The `$set` stage replaces the value of a field with the specified value. If the field does not exist, `$set` will add a new field with the specified value. To use it, you must specify the field name and value using the following syntax:
```js
{
  $set: {
    <field1>: <value1>,
    <field2>: <value2>,
    ...
  }
}
```
- The `$slice` operator returns a subset of an array. To use it, specify the array field name and the number of elements to return using the following syntax:
```js
{
  $slice: [ <arrayField>, <number> ]
}
```
- The `$size` operator returns the number of elements in an array. For example, to return the number of elements in the `views` array, you can use the following:
```js
{
  $size: "$views",
}
```
- Use the `$exists` operator, set to `true`, to identify documents with more than two comments. The syntax for the `$exists` operator is as follows:
```js
{ <field>: { $exists: <boolean> } }
```
