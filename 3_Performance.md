


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



####HW-1

```
db.products.getIndexes()
[
	{
		"v" : 1,
		"key" : {
			"_id" : 1
		},
		"ns" : "store.products",
		"name" : "_id_"
	},
	{
		"v" : 1,
		"key" : {
			"sku" : 1
		},
                "unique" : true,
		"ns" : "store.products",
		"name" : "sku_1"
	},
	{
		"v" : 1,
		"key" : {
			"price" : -1
		},
		"ns" : "store.products",
		"name" : "price_-1"
	},
	{
		"v" : 1,
		"key" : {
			"description" : 1
		},
		"ns" : "store.products",
		"name" : "description_1"
	},
	{
		"v" : 1,
		"key" : {
			"category" : 1,
			"brand" : 1
		},
		"ns" : "store.products",
		"name" : "category_1_brand_1"
	},
	{
		"v" : 1,
		"key" : {
			"reviews.author" : 1
		},
		"ns" : "store.products",
		"name" : "reviews.author_1"
	}
```

```
Which of the following queries can utilize an index. Check all that apply.

A. db.products.find({'brand':"GE"})
B. db.products.find({'brand':"GE"}).sort({price:1})
C. db.products.find({$and:[{price:{$gt:30}},{price:{$lt:50}}]}).sort({brand:1})
D. db.products.find({brand:'GE'}).sort({category:1, brand:-1}).explain()
```
A-'brand' is not an index. B-'price' is an index and it is used in sort function. C-'price' is an index and being used for '$gt' function etc. Although this way is inefficient, index is still used. D-'category' is an index, but brand' isn't. So this query is only using basicCursor.
