


###11. Hinting an Index

```
.hint({a:1, b:1, c:1})
```
Tell mongodb to use an index

```
hint({$nature:1})
```
Don't use an index.

Example: Which queries below will return all three documents for people collection? Check all that apply.

```
db.people.find()
{ "_id" : ObjectId("50a464fb0a9dfcc4f19d6271"), "name" : "Andrew", "title" : "Jester" }
{ "_id" : ObjectId("50a4650c0a9dfcc4f19d6272"), "name" : "Dwight", "title" : "CEO" }
{ "_id" : ObjectId("50a465280a9dfcc4f19d6273"), "name" : "John" }
```

```
db.people.find().sort({'title':1}).hint({$natural:1})
db.people.find().sort({'title':1})
db.people.find({name:{$ne:"Kevin"}}).sort({'title':1})
db.people.find({'title':{$ne:null}}).hint({'title':1})
```

The first three queries will return all 3 docs. Sort operation will return all docs even it uses an index to sort. Hint index will only operate docs with index.
