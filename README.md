# MongoDB Cheat Sheet with Examples

This README file provides a comprehensive guide to MongoDB commands and operations. MongoDB is a popular NoSQL database that stores data in a JSON-like format, making it easy to work with and flexible. This cheat sheet covers basic commands, database and collection creation, data insertion, querying, updates, deletions, and some advanced operations. Let's get started!
![MongoDB Logo](https://miro.medium.com/v2/resize:fit:512/1*doAg1_fMQKWFoub-6gwUiQ.png)

## Table of Contents
1. [Basic Commands](#basic-commands)
   - Show Databases
   - Use Database
   - Show Collections

2. [Database Creation Commands](#database-creation-commands)

3. [Collection Creation Commands](#collection-creation-commands)

4. [Insertion Commands](#insertion-commands)
   - insertOne()
   - insertMany()

5. [Query Commands](#query-commands)
   - find()
   - findOne()

6. [Query Operators](#query-operators)
   - Equality
   - Less Than
   - Less Than or Equal
   - Greater Than or Equal
   - Not Equal
   - Values in an Array
   - Values not in an Array
   - AND
   - OR
   - AND and OR Together
   - NOR
   - NOT

7. [Update Commands](#update-commands)
   - update()
   - save()
   - findOne and Update()
   - updateOne()
   - updateMany()

8. [Deletion Commands](#deletion-commands)
   - deleteOne()
   - deleteMany()

9. [Extras](#extras)
   - skip()
   - sort()
   - createIndex()
   - dropIndex()
   - dropIndexes()

10. [Aggregation](#aggregation)
    - aggregation()

11. [Aggregation Expressions](#aggregation-expressions)
    - sum
    - avg
    - min
    - max
    - push
    - addToSet
    - first

12. [Regular Expressions](#regular-expressions)

## Basic Commands

### Show Databases
To display a list of all available databases:
```javascript
show dbs
```
**Example:**
```javascript
show dbs
```
**Output:**
```plaintext
admin        0.000GB
my_database  0.001GB
```

### Use Database
To switch to a specific database or create it if it doesn't exist:
```javascript
use <database_name>
```
**Example:**
```javascript
use my_database
```

### Show Collections
To view all collections within the current database:
```javascript
show collections
```
**Example:**
```javascript
show collections
```
**Output:**
```plaintext
users
products
```

## Database Creation Commands

MongoDB creates databases and collections automatically when data is inserted. To create a new database, simply start inserting data into it using the `insertOne()` or `insertMany()` commands.

## Collection Creation Commands

Collections are created automatically when data is inserted. You can also create a collection explicitly using the `createCollection()` method:
```javascript
db.createCollection("<collection_name>")
```
**Example:**
```javascript
db.createCollection("products")
```

## Insertion Commands

### insertOne()
To insert a single document into a collection:
```javascript
db.<collection_name>.insertOne({ key1: value1, key2: value2, ... })
```
**Example:**
```javascript
db.products.insertOne({ name: "Laptop", price: 999.99, stock: 10 })
```

### insertMany()
To insert multiple documents into a collection in a single operation:
```javascript
db.<collection_name>.insertMany([
  { key1: value1, key2: value2, ... },
  { key1: value3, key2: value4, ... },
  ...
])
```
**Example:**
```javascript
db.products.insertMany([
  { name: "Keyboard", price: 49.99, stock: 50 },
  { name: "Mouse", price: 19.99, stock: 100 },
  { name: "Monitor", price: 299.99, stock: 20 }
])
```

## Query Commands

### find()
To retrieve documents from a collection that match a specific query criteria:
```javascript
db.<collection_name>.find({ key: value })
```
**Example:**
```javascript
db.products.find({ price: { $lt: 100 } })
```
To show all the documents in collection at once with pretty() method which is used to prettify documents
```javascript
db.products.find().pretty()
```
### findOne()
To retrieve a single document that matches a specific query criteria:
```javascript
db.<collection_name>.findOne({ key: value })
```
**Example:**
```javascript
db.products.findOne({ name: "Laptop" })
```

## Query Operators

### Equality
```javascript
db.<collection_name>.find({ key: value })
```
**Example:**
```javascript
db.products.find({ category: "Electronics" })
```

### Less Than
```javascript
db.<collection_name>.find({ key: { $lt: value } })
```
**Example:**
```javascript
db.products.find({ price: { $lt: 1000 } })
```

### Less Than or Equal
```javascript
db.<collection_name>.find({ key: { $lte: value } })
```
**Example:**
```javascript
db.products.find({ price: { $lte: 1000 } })
```

### Greater Than or Equal
```javascript
db.<collection_name>.find({ key: { $gte: value } })
```
**Example:**
```javascript
db.products.find({ price: { $gte: 500 } })
```

### Not Equal
```javascript
db.<collection_name>.find({ key: { $ne: value } })
```
**Example:**
```javascript
db.products.find({ category: { $ne: "Clothing" } })
```

### Values in an Array
```javascript
db.<collection_name>.find({ key: { $in: [value1, value2, ...] } })
```
**Example:**
```javascript
db.products.find({ color: { $in: ["Black", "White"] } })
```

### Values not in an Array
```javascript
db.<collection_name>.find({ key: { $nin: [value1, value2, ...] } })
```
**Example:**
```javascript
db.products.find({ color: { $nin: ["Red", "Blue"] } })
```

### AND
```javascript
db.<collection_name>.find({ $and: [ { key1: value1 }, { key2: value2 }, ... ] })
```
**Example:**
```javascript
db.products.find({ $and: [ { category: "Electronics" }, { price: { $lt: 1000 } } ] })


```

### OR
```javascript
db.<collection_name>.find({ $or: [ { key1: value1 }, { key2: value2 }, ... ] })
```
**Example:**
```javascript
db.products.find({ $or: [ { category: "Electronics" }, { category: "Clothing" } ] })
```

### AND and OR Together
```javascript
db.<collection_name>.find({ $and: [ { key1: value1 }, { $or: [ { key2: value2 }, { key3: value3 } ] } ] })
```
**Example:**
```javascript
db.products.find({ $and: [ { category: "Electronics" }, { $or: [ { price: { $lt: 500 } }, { stock: { $gte: 50 } } ] } ] })
```

### NOR
```javascript
db.<collection_name>.find({ $nor: [ { key1: value1 }, { key2: value2 }, ... ] })
```
**Example:**
```javascript
db.products.find({ $nor: [ { category: "Electronics" }, { category: "Clothing" } ] })
```

### NOT
```javascript
db.<collection_name>.find({ key: { $not: { <operator_expression> } } })
```
**Example:**
```javascript
db.products.find({ price: { $not: { $lt: 500 } } })
```

## Update Commands

### update()
To update documents that match a specific query with new values:
```javascript
db.<collection_name>.update({ key: value }, { $set: { new_key: new_value } })
```
**Example:**
```javascript
db.products.update({ name: "Laptop" }, { $set: { price: 899.99 } })
```

### save()
To update an existing document or insert a new one if it does not exist:
```javascript
db.<collection_name>.save({ _id: existing_id, key: new_value, ... })
```
**Example:**
```javascript
db.products.save({ _id: 1, name: "Keyboard", price: 39.99, stock: 50 })
```

### findOne and Update()
To update the first document that matches the query:
```javascript
db.<collection_name>.findOneAndUpdate({ key: value }, { $set: { new_key: new_value } })
```
**Example:**
```javascript
db.products.findOneAndUpdate({ name: "Mouse" }, { $set: { price: 24.99 } })
```

### updateOne()
To update the first document that matches the query with new values:
```javascript
db.<collection_name>.updateOne({ key: value }, { $set: { new_key: new_value } })
```
**Example:**
```javascript
db.products.updateOne({ name: "Monitor" }, { $set: { stock: 15 } })
```

### updateMany()
`updateMany()` is a MongoDB method used to update multiple documents that match a specified filter in a collection. It allows you to make changes to multiple records in a single database operation, which can be more efficient than updating each document individually.
The syntax for `updateMany()` in MongoDB is as follows:
```javascript
db.<collection_name>.updateMany({ key: value }, { $set: { new_key: new_value } })
```
**Advance Syntax:**
```javascript
db.collection.updateMany(
   <filter>,
   <update>,
   {
      upsert: <boolean>,
      collation: <document>,
      arrayFilters: [ <filterdocument1>, ... ],
      hint: <document|string>
   }
)
```
**Parameters:**
- `collection`: The name of the collection where the documents will be updated.
- `<filter>`: A document that specifies the selection criteria to identify the documents to be updated.
- `<update>`: A document that contains the modifications to be applied to the matching documents.
- `upsert`: (Optional) If set to `true`, creates a new document if no documents match the filter. Defaults to `false`.
- `collation`: (Optional) Specifies the collation rules for string comparisons during the update.
- `arrayFilters`: (Optional) Allows you to specify filters to identify which elements in an array to update.
- `hint`: (Optional) A document or a string specifying the index to use for the update.
**Example:**
```javascript
db.products.updateOne({ name: "Monitor" }, { $set: { stock: 15 } })
```
**Example:**
Suppose we have a MongoDB collection named "employees" with the following documents:
```json
{ "_id": 1, "name": "Alice", "age": 30, "department": "HR", "salary": 50000 }
{ "_id": 2, "name": "Bob", "age": 35, "department": "IT", "salary": 60000 }
{ "_id": 3, "name": "Charlie", "age": 28, "department": "Marketing", "salary": 45000 }
```
Now, let's use `updateMany()` to increase the salary of all employees in the "IT" department by 10%:
```javascript
// Filter to identify documents in the "IT" department
const filter = { department: "IT" };
// Update to increase the salary by 10%
const update = { $mul: { salary: 1.1 } };
// Perform the update operation on the "employees" collection
const result = db.employees.updateMany(filter, update);
// Output: The "salary" field of all employees in the "IT" department is increased by 10%.
```

## Deletion Commands

### deleteOne()
To delete a single document that matches a specific query:
```javascript
db.<collection_name>.deleteOne({ key: value })
```
**Example:**
```javascript
db.products.deleteOne({ stock: { $lt: 10 } })
```

### deleteMany()
To delete all documents that match a specific query:
```javascript
db.<collection_name>.deleteMany({ key: value })
```
**Example:**
```javascript
db.products.deleteMany({ category: "Electronics" })
```

## Extras

### skip()
To skip a specified number of documents in a collection and return the rest:
```javascript
db.<collection_name>.find().skip(number_of_documents_to_skip)
```
**Example:**
```javascript
db.products.find().skip(5)
```

### sort()
To sort the documents in a collection based on a specific field:
```javascript
db.<collection_name>.find().sort({ field: 1 })  // 1 for ascending, -1 for descending
```
**Example:**
```javascript
db.products.find().sort({ price: -1 })  // Sort by price in descending order
```

### createIndex()
To create an index on a specific field in a collection for faster querying:
```javascript
db.<collection_name>.createIndex({ field: 1 })  // 1 for ascending, -1 for descending
```
**Example:**
```javascript
db.products.createIndex({ name: 1 })  // Create an index on the "name" field
``

`

### dropIndex()
To remove a specific index from a collection:
```javascript
db.<collection_name>.dropIndex({ field: 1 })  // 1 for ascending, -1 for descending
```
**Example:**
```javascript
db.products.dropIndex({ name: 1 })  // Remove the index on the "name" field
```

### dropIndexes()
To remove all indexes from a collection:
```javascript
db.<collection_name>.dropIndexes()
```
**Example:**
```javascript
db.products.dropIndexes()
```

## Aggregation

### aggregation()
To perform aggregation operations on a collection:
```javascript
db.<collection_name>.aggregate([ { <aggregation_stage> }, { <aggregation_stage> }, ... ])
```
**Example:**
```javascript
db.products.aggregate([
  { $group: { _id: "$category", total: { $sum: "$price" } } },
  { $sort: { total: -1 } }
])
```

## Aggregation Expressions

### sum
To calculate the sum of a field in a collection:
```javascript
db.<collection_name>.aggregate([ { $group: { _id: null, total: { $sum: "$field" } } } ])
```
**Example:**
```javascript
db.products.aggregate([ { $group: { _id: null, total: { $sum: "$price" } } } ])
```

### avg
To calculate the average of a field in a collection:
```javascript
db.<collection_name>.aggregate([ { $group: { _id: null, average: { $avg: "$field" } } } ])
```
**Example:**
```javascript
db.products.aggregate([ { $group: { _id: null, average: { $avg: "$price" } } } ])
```

### min
To find the minimum value of a field in a collection:
```javascript
db.<collection_name>.aggregate([ { $group: { _id: null, min: { $min: "$field" } } } ])
```
**Example:**
```javascript
db.products.aggregate([ { $group: { _id: null, min: { $min: "$price" } } } ])
```

### max
To find the maximum value of a field in a collection:
```javascript
db.<collection_name>.aggregate([ { $group: { _id: null, max: { $max: "$field" } } } ])
```
**Example:**
```javascript
db.products.aggregate([ { $group: { _id: null, max: { $max: "$price" } } } ])
```

### push
To add elements to an array field in a collection:
```javascript
db.<collection_name>.update({ _id: document_id }, { $push: { field: new_element } })
```
**Example:**
```javascript
db.products.update({ _id: 1 }, { $push: { colors: "Silver" } })
```

### addToSet
To add elements to an array field only if they don't already exist in a collection:
```javascript
db.<collection_name>.update({ _id: document_id }, { $addToSet: { field: new_element } })
```
**Example:**
```javascript
db.products.update({ _id: 1 }, { $addToSet: { colors: "Silver" } })
```

### first
To get the first document from a collection:
```javascript
db.<collection_name>.find().limit(1)
```
**Example:**
```javascript
db.products.find().limit(1)
```

## Regular Expressions

MongoDB supports regular expressions for pattern matching in queries. You can use the `$regex` operator to perform regular expression queries.

```javascript
db.<collection_name>.find({ field: { $regex: /pattern/ } })
```
**Example:**
```javascript
db.products.find({ name: { $regex: /^Laptop/ } })
```

# Conclusion
In conclusion, this MongoDB cheat sheet provides a comprehensive guide to essential commands and operations for efficiently working with MongoDB databases. It covers basic commands for managing databases and collections, as well as querying techniques to retrieve specific data. Additionally, it introduces aggregation for complex data processing and analysis. You also learned about updating and deleting data, using query operators and regular expressions for precise filtering, and optimizing query performance with indexes.
While this cheat sheet covers a wide range of topics, there are still more advanced features to explore in MongoDB. By continuously practicing and referring to the official documentation, you can enhance your MongoDB skills and unlock exciting possibilities for building powerful applications. Happy coding with MongoDB!

## License

This project is licensed under the [MIT License](LICENSE).
