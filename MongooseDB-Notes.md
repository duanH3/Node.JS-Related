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

| SQL Update Statements  | MongoDB updateMany() Statements |
| ------------- | ------------- |
| UPDATE people SET status = "C" WHERE age > 25 | db.people.updateMany({ age: { $gt: 25 } }, { $set: {status: "C"}})|





