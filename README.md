# MongoDB Cheat Sheet with Examples

This README file provides a comprehensive guide to MongoDB commands and operations. MongoDB is a popular NoSQL database that stores data in a JSON-like format, making it easy to work with and flexible.

<img src="https://miro.medium.com/v2/resize:fit:512/1*doAg1_fMQKWFoub-6gwUiQ.png" alt="MongoDB Image" width="350px" height="350px"/>

---

## Table of Contents

1. [Terminal Connection (mongosh)](#terminal-connection-mongosh)
2. [Basic Commands](#basic-commands)
3. [Database Creation Commands](#database-creation-commands)
4. [Collection Creation Commands](#collection-creation-commands)
5. [Insertion Commands](#insertion-commands)
6. [Query Commands](#query-commands)
7. [Projection](#projection)
8. [Query Operators](#query-operators)
9. [Array Operators](#array-operators)
10. [Update Commands](#update-commands)
11. [Deletion Commands](#deletion-commands)
12. [Extras (skip, sort, limit, count)](#extras)
13. [Index Commands](#index-commands)
14. [Aggregation](#aggregation)
15. [Aggregation Expressions](#aggregation-expressions)
16. [Aggregation Pipeline Stages](#aggregation-pipeline-stages)
17. [Transactions (ACID)](#transactions-acid)
18. [BulkWrite](#bulkwrite)
19. [Database & Collection Utilities](#database--collection-utilities)
20. [User Management](#user-management)
21. [Regular Expressions](#regular-expressions)

---

## Terminal Connection (mongosh)

> `mongosh` is the modern MongoDB Shell (replaces old `mongo`). Install from [mongodb.com/try/download/shell](https://www.mongodb.com/try/download/shell)

### Connect to localhost (default port 27017)
```bash
mongosh
# or explicitly
mongosh --host localhost --port 27017
```

### Connect to a specific database directly
```bash
mongosh --host localhost --port 27017 myDatabaseName
```

### Connect with authentication
```bash
mongosh --host localhost --port 27017 -u myUser -p myPassword --authenticationDatabase admin
```

### Connect using a connection string URI
```bash
# Local
mongosh "mongodb://localhost:27017/myDatabase"

# With credentials in URI
mongosh "mongodb://username:password@localhost:27017/myDatabase"

# MongoDB Atlas (cloud) - SRV URI
mongosh "mongodb+srv://username:password@cluster0.xxxxx.mongodb.net/myDatabase"

# Atlas - password prompt (more secure)
mongosh "mongodb+srv://username@cluster0.xxxxx.mongodb.net/myDatabase"
# You'll be prompted for the password
```

### Connect with TLS/SSL
```bash
mongosh "mongodb+srv://username@cluster.mongodb.net/db" --tls
```

### Connect with replica set
```bash
mongosh "mongodb://host1:27017,host2:27017/myDB?replicaSet=myReplicaSet"
```

### Useful shell options
```bash
mongosh --quiet          # suppress startup banners
mongosh --eval "db.stats()"   # run a single command and exit
mongosh --version        # check mongosh version
```

### Exit the shell
```bash
exit
# or
quit()
# or
.exit
```

---

## Basic Commands

### Show Databases
```javascript
show dbs
```

### Use / Switch Database
```javascript
use myDatabase
```

### Show current database
```javascript
db
```

### Show Collections
```javascript
show collections
```

### Show current user
```javascript
db.getUser()
```

### Show all users in current DB
```javascript
show users
```

### Show all roles
```javascript
show roles
```

### Drop a database
```javascript
db.dropDatabase()
```

---

## Database Creation Commands

MongoDB creates databases automatically when data is inserted. Just `use <name>` and insert a document.

---

## Collection Creation Commands

### Auto-created on insert
Collections are created automatically when you insert data.

### Explicit creation
```javascript
db.createCollection("products")

// With options (capped collection - fixed size)
db.createCollection("logs", {
  capped: true,
  size: 10485760,  // 10MB max size
  max: 5000        // max 5000 documents
})
```

### Drop a collection
```javascript
db.products.drop()
```

### Rename a collection
```javascript
db.products.renameCollection("items")
```

---

## Insertion Commands

### insertOne()
```javascript
db.products.insertOne({ name: "Laptop", price: 999.99, stock: 10 })
```

### insertMany()
```javascript
db.products.insertMany([
  { name: "Keyboard", price: 49.99, stock: 50 },
  { name: "Mouse", price: 19.99, stock: 100 },
  { name: "Monitor", price: 299.99, stock: 20 }
])
```

---

## Query Commands

### find() — Get all or filtered documents
```javascript
db.products.find()
db.products.find().pretty()  // pretty print
db.products.find({ price: { $lt: 100 } })
```

### findOne() — Get first matching document
```javascript
db.products.findOne({ name: "Laptop" })
```

### count() / countDocuments()
```javascript
db.products.countDocuments()
db.products.countDocuments({ category: "Electronics" })
// Deprecated but still seen:
db.products.count({ category: "Electronics" })
```

### distinct() — Unique values of a field
```javascript
db.products.distinct("category")
```

---

## Projection

Control which fields are returned (like `SELECT col1, col2` in SQL).

```javascript
// Syntax: find(filter, projection)
// 1 = include, 0 = exclude

// Include only name and price (always returns _id unless excluded)
db.products.find({}, { name: 1, price: 1 })

// Exclude _id
db.products.find({}, { name: 1, price: 1, _id: 0 })

// Exclude a specific field
db.products.find({}, { stock: 0 })
```

> **Note:** You can't mix include and exclude in the same projection (except `_id`).

---

## Query Operators

### Comparison Operators

| Operator | Meaning       |
|----------|--------------|
| `$eq`    | Equal        |
| `$ne`    | Not equal    |
| `$lt`    | Less than    |
| `$lte`   | Less than or equal |
| `$gt`    | Greater than |
| `$gte`   | Greater than or equal |
| `$in`    | In array     |
| `$nin`   | Not in array |

```javascript
db.products.find({ price: { $lt: 1000 } })
db.products.find({ price: { $gte: 500 } })
db.products.find({ category: { $ne: "Clothing" } })
db.products.find({ color: { $in: ["Black", "White"] } })
db.products.find({ color: { $nin: ["Red", "Blue"] } })
```

### Logical Operators

```javascript
// AND
db.products.find({ $and: [{ category: "Electronics" }, { price: { $lt: 1000 } }] })

// OR
db.products.find({ $or: [{ category: "Electronics" }, { category: "Clothing" }] })

// NOR
db.products.find({ $nor: [{ category: "Electronics" }, { category: "Clothing" }] })

// NOT
db.products.find({ price: { $not: { $lt: 500 } } })

// AND + OR combined
db.products.find({
  $and: [
    { category: "Electronics" },
    { $or: [{ price: { $lt: 500 } }, { stock: { $gte: 50 } }] }
  ]
})
```

### Element Operators

```javascript
// Check if field exists
db.products.find({ discount: { $exists: true } })
db.products.find({ discount: { $exists: false } })

// Check field type
db.products.find({ price: { $type: "double" } })
db.products.find({ name: { $type: "string" } })
```

---

## Array Operators

### Query inside arrays

```javascript
// Match documents where tags array contains "electronics"
db.products.find({ tags: "electronics" })

// $all — must contain ALL values
db.products.find({ tags: { $all: ["electronics", "wireless"] } })

// $size — array length equals N
db.products.find({ tags: { $size: 3 } })

// $elemMatch — at least one element matches all conditions
db.products.find({ ratings: { $elemMatch: { $gte: 4, $lt: 5 } } })
```

### Update arrays

```javascript
// $push — add element to array
db.products.updateOne({ _id: 1 }, { $push: { colors: "Silver" } })

// $push with $each — add multiple elements
db.products.updateOne({ _id: 1 }, { $push: { colors: { $each: ["Gold", "Rose Gold"] } } })

// $addToSet — add only if not already present (no duplicates)
db.products.updateOne({ _id: 1 }, { $addToSet: { colors: "Silver" } })

// $pull — remove specific element(s)
db.products.updateOne({ _id: 1 }, { $pull: { tags: "outdated" } })

// $pop — remove first (-1) or last (1) element
db.products.updateOne({ _id: 1 }, { $pop: { tags: 1 } })   // remove last
db.products.updateOne({ _id: 1 }, { $pop: { tags: -1 } })  // remove first
```

---

## Update Commands

### updateOne()
```javascript
db.products.updateOne({ name: "Monitor" }, { $set: { stock: 15 } })
```

### updateMany()
```javascript
db.products.updateMany({ category: "Electronics" }, { $set: { onSale: true } })

// Multiply a field ($mul)
db.employees.updateMany({ department: "IT" }, { $mul: { salary: 1.1 } })

// Increment a field ($inc)
db.products.updateMany({ category: "Electronics" }, { $inc: { stock: -1 } })

// Remove a field ($unset)
db.products.updateMany({}, { $unset: { oldField: "" } })

// Rename a field ($rename)
db.products.updateMany({}, { $rename: { "oldName": "newName" } })
```

### replaceOne() — Replace entire document
```javascript
// WARNING: This replaces the ENTIRE document (except _id)
db.products.replaceOne(
  { name: "Keyboard" },
  { name: "Keyboard Pro", price: 59.99, stock: 40 }
)
```

### findOneAndUpdate() — Atomic find + update, returns document
```javascript
// Returns document BEFORE update (default)
db.products.findOneAndUpdate(
  { name: "Mouse" },
  { $set: { price: 24.99 } }
)

// Return document AFTER update
db.products.findOneAndUpdate(
  { name: "Mouse" },
  { $set: { price: 24.99 } },
  { returnDocument: "after" }
)
```

### findOneAndReplace()
```javascript
db.products.findOneAndReplace(
  { name: "Mouse" },
  { name: "Mouse Pro", price: 39.99 },
  { returnDocument: "after" }
)
```

### findOneAndDelete() — Atomic find + delete (useful for job queues)
```javascript
db.jobs.findOneAndDelete({ status: "pending" })
```

### Upsert — Insert if not found, update if found
```javascript
db.products.updateOne(
  { name: "Trackpad" },
  { $set: { price: 79.99, stock: 25 } },
  { upsert: true }
)
```

### $setOnInsert — Only set fields on new insert (not on update)
```javascript
db.products.updateOne(
  { name: "Headphones" },
  {
    $set: { price: 199 },
    $setOnInsert: { createdAt: new Date() }
  },
  { upsert: true }
)
```

---

## Deletion Commands

### deleteOne()
```javascript
db.products.deleteOne({ stock: { $lt: 10 } })
```

### deleteMany()
```javascript
db.products.deleteMany({ category: "Electronics" })

// Delete all documents in a collection (keeps the collection)
db.products.deleteMany({})
```

---

## Extras

### skip()
```javascript
db.products.find().skip(5)
```

### limit()
```javascript
db.products.find().limit(10)
```

### sort()
```javascript
db.products.find().sort({ price: -1 })  // descending
db.products.find().sort({ price: 1 })   // ascending
db.products.find().sort({ category: 1, price: -1 })  // multi-field sort
```

### Pagination pattern (skip + limit)
```javascript
// Page 1
db.products.find().sort({ _id: 1 }).skip(0).limit(10)
// Page 2
db.products.find().sort({ _id: 1 }).skip(10).limit(10)

// ⚠️ Avoid skip() on large collections — use cursor-based pagination instead
// Cursor-based (faster for large datasets)
db.products.find({ _id: { $gt: lastSeenId } }).sort({ _id: 1 }).limit(10)
```

---

## Index Commands

### createIndex()
```javascript
// Single field index
db.products.createIndex({ name: 1 })   // ascending
db.products.createIndex({ price: -1 }) // descending

// Compound index
db.products.createIndex({ category: 1, price: -1 })

// Unique index
db.products.createIndex({ email: 1 }, { unique: true })

// Sparse index (only indexes docs where field exists)
db.products.createIndex({ discount: 1 }, { sparse: true })

// TTL index (auto-delete after N seconds)
db.sessions.createIndex({ createdAt: 1 }, { expireAfterSeconds: 3600 })

// Text index (full-text search)
db.articles.createIndex({ content: "text" })
db.articles.find({ $text: { $search: "mongodb tutorial" } })
```

### listIndexes()
```javascript
db.products.getIndexes()
```

### dropIndex()
```javascript
db.products.dropIndex({ name: 1 })
db.products.dropIndex("name_1")  // by index name
```

### dropIndexes()
```javascript
db.products.dropIndexes()  // drops all indexes except _id
```

---

## Aggregation

The aggregation pipeline processes documents through a sequence of stages.

```javascript
db.products.aggregate([
  { $match: { category: "Electronics" } },
  { $group: { _id: "$brand", total: { $sum: "$price" } } },
  { $sort: { total: -1 } }
])
```

---

## Aggregation Expressions

| Expression  | Description                        |
|-------------|------------------------------------|
| `$sum`      | Sum of values                      |
| `$avg`      | Average of values                  |
| `$min`      | Minimum value                      |
| `$max`      | Maximum value                      |
| `$count`    | Count documents                    |
| `$push`     | Add values to array                |
| `$addToSet` | Add unique values to array         |
| `$first`    | First value in group               |
| `$last`     | Last value in group                |

```javascript
// Sum
db.products.aggregate([{ $group: { _id: null, total: { $sum: "$price" } } }])

// Avg
db.products.aggregate([{ $group: { _id: null, average: { $avg: "$price" } } }])

// Min / Max
db.products.aggregate([{ $group: { _id: null, minPrice: { $min: "$price" }, maxPrice: { $max: "$price" } } }])

// push — collect all values into array
db.products.aggregate([{ $group: { _id: "$category", allPrices: { $push: "$price" } } }])

// addToSet — unique values only
db.products.aggregate([{ $group: { _id: "$category", brands: { $addToSet: "$brand" } } }])

// first / last
db.products.aggregate([
  { $sort: { price: 1 } },
  { $group: { _id: "$category", cheapest: { $first: "$name" } } }
])
```

---

## Aggregation Pipeline Stages

### $match — Filter documents (like find)
```javascript
{ $match: { status: "active", price: { $gt: 100 } } }
```

### $project — Reshape documents (include/exclude/compute fields)
```javascript
{
  $project: {
    name: 1,
    price: 1,
    _id: 0,
    discountedPrice: { $multiply: ["$price", 0.9] }
  }
}
```

### $group — Group by field and aggregate
```javascript
{
  $group: {
    _id: "$category",
    total: { $sum: "$price" },
    count: { $sum: 1 }
  }
}
```

### $sort — Sort results
```javascript
{ $sort: { total: -1 } }
```

### $limit / $skip — Paginate results
```javascript
{ $limit: 10 }
{ $skip: 20 }
```

### $lookup — LEFT OUTER JOIN with another collection
```javascript
{
  $lookup: {
    from: "orders",          // collection to join
    localField: "_id",       // field from current collection
    foreignField: "userId",  // field from joined collection
    as: "userOrders"         // output array field name
  }
}
```

**Full example:**
```javascript
db.users.aggregate([
  {
    $lookup: {
      from: "orders",
      localField: "_id",
      foreignField: "userId",
      as: "orders"
    }
  },
  { $match: { "orders.0": { $exists: true } } }  // users who have at least 1 order
])
```

### $unwind — Deconstruct an array field (one doc per array element)
```javascript
{ $unwind: "$tags" }

// Preserve docs with empty/missing arrays
{ $unwind: { path: "$tags", preserveNullAndEmptyArrays: true } }
```

### $addFields — Add computed fields without removing existing ones
```javascript
{
  $addFields: {
    totalValue: { $multiply: ["$price", "$stock"] },
    updatedAt: new Date()
  }
}
```

### $count — Count documents passing through
```javascript
{ $count: "totalProducts" }
```

### $out — Write pipeline result to a new collection
```javascript
{ $out: "product_summary" }
```

### $facet — Run multiple sub-pipelines in parallel
```javascript
{
  $facet: {
    byCategory: [{ $group: { _id: "$category", count: { $sum: 1 } } }],
    priceStats: [{ $group: { _id: null, avg: { $avg: "$price" } } }]
  }
}
```

---

## Transactions (ACID)

Multi-document ACID transactions — available from MongoDB 4.0+. Required for operations spanning multiple collections or documents that must be atomic.

```javascript
// Using mongosh
const session = db.getMongo().startSession()
session.startTransaction()

try {
  const accounts = session.getDatabase("bank").accounts

  accounts.updateOne(
    { name: "Alice" },
    { $inc: { balance: -500 } }
  )

  accounts.updateOne(
    { name: "Bob" },
    { $inc: { balance: 500 } }
  )

  session.commitTransaction()
  print("Transaction committed!")
} catch (err) {
  session.abortTransaction()
  print("Transaction aborted:", err)
} finally {
  session.endSession()
}
```

> **Note:** Transactions require a replica set or sharded cluster. They don't work on standalone mongod instances.

---

## BulkWrite

Perform multiple write operations in a single network round-trip.

```javascript
db.products.bulkWrite([
  {
    insertOne: {
      document: { name: "USB Hub", price: 29.99, stock: 75 }
    }
  },
  {
    updateOne: {
      filter: { name: "Keyboard" },
      update: { $set: { price: 44.99 } }
    }
  },
  {
    updateMany: {
      filter: { category: "Electronics" },
      update: { $inc: { stock: -1 } }
    }
  },
  {
    replaceOne: {
      filter: { name: "Mouse" },
      replacement: { name: "Mouse Pro", price: 34.99, stock: 60 }
    }
  },
  {
    deleteOne: {
      filter: { stock: 0 }
    }
  },
  {
    deleteMany: {
      filter: { discontinued: true }
    }
  }
])
```

> By default, `bulkWrite()` is **ordered** (stops on first error). Pass `{ ordered: false }` to continue on errors.

```javascript
db.products.bulkWrite([...operations], { ordered: false })
```

---

## Database & Collection Utilities

### Database stats
```javascript
db.stats()
db.serverStatus()
```

### Collection stats
```javascript
db.products.stats()
db.products.totalSize()
db.products.totalIndexSize()
db.products.dataSize()
```

### Validate a collection
```javascript
db.products.validate()
```

### List all collections with details
```javascript
db.getCollectionInfos()
```

### Copy a database (mongosh)
```javascript
// Use mongodump/mongorestore for production — in-shell copy is removed in newer versions
```

---

## User Management

### Create a user
```javascript
db.createUser({
  user: "appUser",
  pwd: "securePassword123",
  roles: [
    { role: "readWrite", db: "myDatabase" },
    { role: "read", db: "reporting" }
  ]
})
```

### Show users
```javascript
show users
db.getUsers()
```

### Update user password
```javascript
db.changeUserPassword("appUser", "newPassword456")
```

### Grant additional roles
```javascript
db.grantRolesToUser("appUser", [{ role: "dbAdmin", db: "myDatabase" }])
```

### Revoke roles
```javascript
db.revokeRolesFromUser("appUser", [{ role: "dbAdmin", db: "myDatabase" }])
```

### Drop a user
```javascript
db.dropUser("appUser")
```

### Built-in roles reference

| Role          | Access                              |
|---------------|-------------------------------------|
| `read`        | Read-only                           |
| `readWrite`   | Read + write                        |
| `dbAdmin`     | Schema/index admin, no data access  |
| `userAdmin`   | Manage users/roles                  |
| `dbOwner`     | All of the above combined           |
| `readAnyDatabase` | Read all databases (admin only) |
| `root`        | Full superuser access               |

---

## Regular Expressions

```javascript
// Basic regex
db.products.find({ name: { $regex: /^Laptop/ } })

// Case-insensitive
db.products.find({ name: { $regex: /laptop/i } })

// Contains substring
db.products.find({ name: { $regex: /pro/i } })

// String form
db.products.find({ name: { $regex: "^Laptop", $options: "i" } })
```

---

## Quick Reference: Update Operators

| Operator      | Description                              |
|---------------|------------------------------------------|
| `$set`        | Set a field value                        |
| `$unset`      | Remove a field                           |
| `$inc`        | Increment a numeric field                |
| `$mul`        | Multiply a numeric field                 |
| `$rename`     | Rename a field                           |
| `$min`        | Update if new value is less than current |
| `$max`        | Update if new value is greater           |
| `$currentDate`| Set field to current date                |
| `$push`       | Add element to array                     |
| `$pull`       | Remove elements from array               |
| `$addToSet`   | Add to array (unique only)               |
| `$pop`        | Remove first/last array element          |
| `$setOnInsert`| Set only on upsert insert                |

---

## License

This project is licensed under the [MIT License](LICENSE).
