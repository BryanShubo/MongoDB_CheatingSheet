####1. Introduction

####2. Database--create and delete

####3. Collection--create and delete

```
mongorestore --collection messages --db enron messages.bson
```

####1. Introduction

1) MongoDB stores data in the form of documents, which are JSON-like field and value pairs. 
Documents are analogous to structures in programming languages that associate keys with values 
(e.g. dictionaries, hashes, maps, and associative arrays). Formally, MongoDB documents are BSON documents. BSON is a binary representation of JSON with additional type information. In the documents, the value of a field can be any of the BSON data types, including other documents, arrays, and arrays of documents. 

2) In mongoDB, 
database is database
table is collection
row is document

3) MongoDB does not support:
* Join
* Transaction

####2. Database

1) Display databases: show dbs
2) Create a database: use <db_name>
3) Delete / drop a database: 
```
>use mydb
switched to db mydb
>db.dropDatabase()
```

3. On Mac os, to run mongod and mongo: ./mongod or ./mongo








