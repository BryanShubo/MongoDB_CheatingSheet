


###1. Indexes
```
Index of (name, dob, hair_color)
search from top to bottom
name
name_dob
name_hair_color
name_dob_hair_color
```

If you have index, then read data fast but write data slow.

###2. Create an index

```
db.student.ensureIndex({student:1, class:-1}) //1 means ascending, -1 means descending
```

###3. Discoving indexes

```
db.system.indexes.find() // find all indexes from a database
```

```
db.students.getIndexes() // find all indexes from a collection
```

```
db.students.dropIndex({"student":1}) // drop an index from a collection
```

###4. Multi-key index

Example 1: Can't create index parallel arrays[a][b]
```
db.test.insert({a:1,b:1})
db.test.ensureIndex({a:1, b:1})
db.test.insert({a:[1,2,3], b:2}) // valid
db.test.insert({a:[1,2,3], b:[4,5,6]}) // invalid
```

Example 2: For a subpart, you may able to create multi-key for two arrays
```
db.user.insert({
    name:bryan,
    tag: prime,
    contacts: {phones:[1, 2,3], emails:[4,5,6]}
})
db.user.ensureIndex({contacts.phones:1, contacts.emails:1})
```

###5. Create an unique index
```
db.students.ensureIndex({name:1}, {unique:true})
```

Remove duplicates:
```
db.students.ensureIndex({name:1}, {unique:true, dropDups:true})
```

If you use dropDups, indexes will be deleted ever and forever.




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
A-'brand' is not an index. 

B-'price' is an index and it is used in sort function. 

C-'price' is an index and being used for '$gt' function etc. Although this way is inefficient, index is still used. 

D-'category' is an index, but brand' isn't. So this query is only using basicCursor.


####HW-2

```
Suppose you have a collection called tweets whose documents contain information about the created_at time of the tweet and the user's followers_count at the time they issued the tweet. What can you infer from the following explain output?
db.tweets.find({"user.followers_count":{$gt:1000}}).sort({"created_at" : 1 }).limit(10).skip(5000).explain()
{
        "cursor" : "BtreeCursor created_at_-1 reverse",
        "isMultiKey" : false,
        "n" : 10,
        "nscannedObjects" : 46462,
        "nscanned" : 46462,
        "nscannedObjectsAllPlans" : 49763,
        "nscannedAllPlans" : 49763,
        "scanAndOrder" : false,
        "indexOnly" : false,
        "nYields" : 0,
        "nChunkSkips" : 0,
        "millis" : 205,
        "indexBounds" : {
                "created_at" : [
                        [
                                {
                                        "$minElement" : 1
                                },
                                {
                                        "$maxElement" : 1
                                }
                        ]
                ]
        },
        "server" : "localhost.localdomain:27017"
}
```

```
A. This query performs a collection scan.
B. The query uses an index to determine the order in which to return result documents.
C. The query uses an index to determine which documents match.
D. The query returns 46462 documents.
E. The query visits 46462 documents.
F. The query is a "covered index query".
```

The answer is ABE.

C-The index is only used in sorting.

D-The skip(5000) means this five thousand won't be scanned.

F-Have no idea.
