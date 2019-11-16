# Mongoose DB Notes

MongoDB and MySQL Compared
https://www.mongodb.com/compare/mongodb-mysql

SQL to MongoDB Mapping Chart
https://docs.mongodb.com/manual/reference/sql-comparison/

| SQL SELECT Statements  | MongoDB find() Statements |
| ------------- | ------------- |
| SELECT * FROM people  | db.people.find() |
| SELECT id,
       user_id,
       status
FROM people  | db.people.find(
    { },
    { user_id: 1, status: 1 }
)  |




