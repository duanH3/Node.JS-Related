# Mongoose DB Notes

MongoDB and MySQL Compared
https://www.mongodb.com/compare/mongodb-mysql

SQL to MongoDB Mapping Chart
https://docs.mongodb.com/manual/reference/sql-comparison/

| SQL SELECT Statements  | MongoDB find() Statements |
| ------------- | ------------- |
| SELECT * FROM people  | db.people.find() |
| SELECT id, user_id, status FROM people  | db.people.find({}, { user_id: 1, status: 1 })  |
|SELECT * FROM people WHERE status = "A" | db.people.find({ status: "A" }) |
|SELECT * FROM people WHERE status != "A" | db.people.find({ status: { $ne: "A" }}) |

# Update single entry

There are many instances where you only required to update a single entry. Let say you only wanted to update the article [status] of a particular [user id] from "_anything" to "approved"

```
...
  var query = { _id: userId};
  var newvalues = { $set: { status: "approved" } };
  dbo.collection("article").updateOne(query, newvalues, function(err, res) {
...
```

# Updatemany

one example

| SQL Update Statements  | MongoDB updateMany() Statements |
| ------------- | ------------- |
| UPDATE people SET status = "C" WHERE age > 25 | db.people.updateMany({ age: { $gt: 25 } }, { $set: {status: "C"}})|


# Use cases
Use Cases
https://docs.mongodb.com/ecosystem/use-cases/category-hierarchy/

    Storing Log Data
    Hierarchical Aggregation
    Product Catalog
    Inventory Management
    Category Hierarchy
    Metadata and Asset Management
    Storing Comments
    Client Side Field Level Encryption Guide




