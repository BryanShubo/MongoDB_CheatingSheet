##Aggregation

####1. Aggregation pipeline
* 1.1 $project 
* 1.2 $match 
* 1.3 $group
* 1.4 $sort
* 1.5 $skip
* 1.6 $limit
* 1.7 $unwind
* 1.8 $out
* 1.9 $ redact
* 1.10 $geonear
* 1.11. Simple example

####2. $group--aggregation stage
* 2.1 $sum
* 2.2 $avg
* 2.3 $addToSet
* 2.4 $push
* 2.5 $max and $min
* 2.6 Compound grouping
* 2.7 Double grouping

####3. $project--aggregation stage 

####4. $match--aggregation stage 

####5. $sort--aggregation stage 
* 5.1 $first and $last 

####6. $skip and $limit--aggregation stage

####7. $unwind--aggregation stage 
* 7.1 Double $unwind 

####8. Mapping between sql and aggregation

####9. Limitation in aggregation framework



####1. Aggregation pipeline
```
collection->$project->$match->$group->$sort->result
```
```
1.1 $project -> reshape doc, 1:1
```
```
1.2 $match -> filter doc, n:1 (reduce the number of doc)
```
```
1.3 $group -> aggregation, n:1 (reduce the number of doc)
The following operations should be used within $group aggregation:
* $sum -> return a single result
* $avg -> return a single result
* $push -> return an array (with duplicates)
* $addToSet -> return an array (without duplicates)
* $max -> return a single result
* $min > return a single result
```
```
1.4 $sort -> sort doc, 1:1
The following operations should be used within $sort aggregation:
* $first
* $last
```
```
1.5 $skip -> skip doc, n:1 (reduce the num of doc)

1.6 $limit -> limit doc, n:1 (reduce the num of doc)

** Note: In aggregation, the order of them does matter. In find() operation, the order of them does not matter. MongoDB alwarys run sort, skip, and limit in sequence.**
```
```
1.7 $unwind -> normalize doc, 1:n
db.items.insert({_id:'nail', 'attributes':['hard', 'shiny', 'pointy', 'thin']});
db.items.aggregate([{$unwind:"$attributes"}]);

result:
'nail':'hard'
'nail':'shiny'
'nail':'pointy'
'nail':'thin'
```
```
1.8 $out -> output to another collection, 1:1 // Usually, the result will return as a cursor
```
```
1.9 $ redact -> Control certain use can see the fields
```
```
1.10 $geonear -> location based queries
```
```
1.11. Simple example
db.products.insert({'name':'iPad 16GB Wifi', 'manufacturer':"Apple", 
		    'category':'Tablets', 
		    'price':499.00})
db.products.insert({'name':'iPad 32GB Wifi', 'category':'Tablets', 
		    'manufacturer':"Apple", 
		    'price':599.00})
db.products.insert({'name':'iPad 64GB Wifi', 'category':'Tablets', 
		    'manufacturer':"Apple", 
		    'price':699.00})
db.products.insert({'name':'Galaxy S3', 'category':'Cell Phones', 
		    'manufacturer':'Samsung',
		    'price':563.99})
db.products.insert({'name':'Galaxy Tab 10', 'category':'Tablets', 
		    'manufacturer':'Samsung',
		    'price':450.99})
db.products.insert({'name':'Vaio', 'category':'Laptops', 
		    'manufacturer':"Sony", 
		    'price':499.00})
db.products.insert({'name':'Macbook Air 13inch', 'category':'Laptops', 
		    'manufacturer':"Apple", 
		    'price':499.00})
db.products.insert({'name':'Nexus 7', 'category':'Tablets', 
		    'manufacturer':"Google", 
		    'price':199.00})
db.products.insert({'name':'Kindle Paper White', 'category':'Tablets', 
		    'manufacturer':"Amazon", 
		    'price':129.00})
db.products.insert({'name':'Kindle Fire', 'category':'Tablets', 
		    'manufacturer':"Amazon", 
		    'price':199.00})
		    
```
```
 db.products.aggregate([
    {$group:
     {
	 _id:"$manufacturer", 
	 num_products:{$sum:1}
     }
    }
]) 

// _id is the $group factor
```
```
{
    "result" : [ 
        {
            "_id" : "Amazon",
            "num_products" : 2
        }, 
        {
            "_id" : "Sony",
            "num_products" : 1
        }, 
        {
            "_id" : "Samsung",
            "num_products" : 2
        }, 
        {
            "_id" : "Google",
            "num_products" : 1
        }, 
        {
            "_id" : "Apple",
            "num_products" : 4
        }
    ],
    "ok" : 1
}
```
```
products -> $group (Doing a upsert operation. If exist, update, otherwise, insert) -> result
```

####2. $group--aggregation stage

#####2.1 $sum
```
db.products.aggregate([
    {$group:
     {
	 _id: {
	     "maker":"$manufacturer"
	 },
	 sum_prices:{$sum:"$price"}
     }
    }
])

```

#####2.2 $avg
```
db.products.aggregate([
    {$group:
     {
	 _id: {
	     "category":"$category"
	 },
	 avg_price:{$avg:"$price"}
     }
    }
])
```

#####2.3 $addToSet
```
db.products.aggregate([
    {$group:
     {
	 _id: {
	     "maker":"$manufacturer"
	 },
	 categories:{$addToSet:"$category"}
     }
    }
])
// To see how many categories of each manufacture carries.
// Only add unique element to array.
```

#####2.4 $push
```
db.products.aggregate([
    {$group:
     {
	 _id: {
	     "maker":"$manufacturer"
	 },
	 categories:{$push:"$category"}
     }
    }
])
// Note: $push does not take care duplicates
```

#####2.5 $max and $min
```
db.products.aggregate([
    {$group:
     {
	 _id: {
	     "maker":"$manufacturer"
	 },
	 maxprice:{$max:"$price"}
     }
    }
])
```

####2.6 Compound grouping
```
db.products.aggregate([
    {$group:
     {
	 _id: {
	     "manufacturer":"$manufacturer", 
	     "category" : "$category"},
	 num_products:{$sum:1}
     }
    }
])

Note: _id can be compound key, but has to be unique.
db.foo.insert({_id:{name:"Bryan", hometown:"NY"}})
```

#####2.7 Double grouping
```
db.grades.aggregate([
    {'$group':{_id:{class_id:"$class_id", student_id:"$student_id"}, 'average':{"$avg":"$score"}}},
    {'$group':{_id:"$_id.class_id", 'average':{"$avg":"$average"}}}])
```
Quiz:
```
Given the following collection:
> db.fun.find()
{ "_id" : 0, "a" : 0, "b" : 0, "c" : 21 }
{ "_id" : 1, "a" : 0, "b" : 0, "c" : 54 }
{ "_id" : 2, "a" : 0, "b" : 1, "c" : 52 }
{ "_id" : 3, "a" : 0, "b" : 1, "c" : 17 }
{ "_id" : 4, "a" : 1, "b" : 0, "c" : 22 }
{ "_id" : 5, "a" : 1, "b" : 0, "c" : 5 }
{ "_id" : 6, "a" : 1, "b" : 1, "c" : 87 }
{ "_id" : 7, "a" : 1, "b" : 1, "c" : 97 }

And the following aggregation query
db.fun.aggregate([{$group:{_id:{a:"$a", b:"$b"}, c:{$max:"$c"}}}, {$group:{_id:"$_id.a", c:{$min:"$c"}}}])

52 and 22
```

####3. $project--aggregation stage
```
$project is used to:
* remove keys
* add new keys
* reshape keys
* use some simple functions:
  * $toUpper
  * $toLower
  * $add
  * $multiply
```

```
db.products.aggregate([
    {$project:
     {
	 _id:0,
	 'maker': {$toLower:"$manufacturer"},
	 'details': {'category': "$category",
		     'price' : {"$multiply":["$price",10]}
		    },
	 'item':'$name'
     }
    }
])
```


####4. $match--aggregation stage

* pre-aggregate filter
* filter the result
* One thing to note about $match (and $sort) is that they can use indexes, but only if done at the beginning of the aggregation pipeline.

```
use agg
db.zips.aggregate([
    {$match:
     {
	 state:"NY"
     }
    }
])
```
```
use agg
db.zips.aggregate([
    {$match:
     {
	 state:"NY"
     }
    },
    {$group:
     {
	 _id: "$city",
	 population: {$sum:"$pop"},
	 zip_codes: {$addToSet: "$_id"}
     }
    }
])
```
```
use agg
db.zips.aggregate([
    {$match:
     {
	 state:"NY"
     }
    },
    {$group:
     {
	 _id: "$city",
	 population: {$sum:"$pop"},
	 zip_codes: {$addToSet: "$_id"}
     }
    },
    {$project:
     {
	 _id: 0,
	 city: "$_id",
	 population: 1,
	 zip_codes:1
     }
    }
     
])
```


####5. $sort--aggregation stage
* disk / memory based sort (100 Mb limit)
* before or after grouping stage
* $first and $last have to work with $sort

```
db.zips.aggregate([
    {$match:
     {
	 state:"NY"
     }
    },
    {$group:
     {
	 _id: "$city",
	 population: {$sum:"$pop"},
     }
    },
    {$project:
     {
	 _id: 0,
	 city: "$_id",
	 population: 1,
     }
    },
    {$sort:
     {
	 population:-1
     }
    }
])
```



#####5.1. $first and $last

```
use agg
db.zips.aggregate([
    /* get the population of every city in every state */
    {$group:
     {
	 _id: {state:"$state", city:"$city"},
	 population: {$sum:"$pop"},
     }
    },
     /* sort by state, population */
    {$sort: 
     {"_id.state":1, "population":-1}
    },

    /* group by state, get the first item in each group */
    {$group: 
     {
	 _id:"$_id.state",
	 city: {$first: "$_id.city"},
	 population: {$first:"$population"}
     }
    },

    /* now sort by state again */
    {$sort:
     {"_id":1}
    }
])
```


```
use agg
db.zips.aggregate([
    /* get the population of every city in every state */
    {$group:
     {
	 _id: {state:"$state", city:"$city"},
	 population: {$sum:"$pop"},
     }
    }
])
```

```
use agg
db.zips.aggregate([
    /* get the population of every city in every state */
    {$group:
     {
	 _id: {state:"$state", city:"$city"},
	 population: {$sum:"$pop"},
     }
    },
     /* sort by state, population */
    {$sort: 
     {"_id.state":1, "population":-1}
    }
])
```


```
use agg
db.zips.aggregate([
    /* get the population of every city in every state */
    {$group:
     {
	 _id: {state:"$state", city:"$city"},
	 population: {$sum:"$pop"},
     }
    },
     /* sort by state, population */
    {$sort: 
     {"_id.state":1, "population":-1}
    },
    /* group by state, get the first item in each group */
    {$group: 
     {
	 _id:"$_id.state",
	 city: {$first: "$_id.city"},
	 population: {$first:"$population"}
     }
    }
])

```

####6. $skip and $limit
* In find() operation, skip first and then limit. But in aggregation, the orders does matter.

```
use agg
db.zips.aggregate([
    {$match:
     {
	 state:"NY"
     }
    },
    {$group:
     {
	 _id: "$city",
	 population: {$sum:"$pop"},
     }
    },
    {$project:
     {
	 _id: 0,
	 city: "$_id",
	 population: 1,
     }
    },
    {$sort:
     {
	 population:-1
     }
    },
    {$skip: 10},
    {$limit: 5}
])
```

####7. $unwind--aggregation stage

Quiz
```
use agg;
db.items.drop();
db.items.insert({_id:'nail', 'attributes':['hard', 'shiny', 'pointy', 'thin']});
db.items.insert({_id:'hammer', 'attributes':['heavy', 'black', 'blunt']});
db.items.insert({_id:'screwdriver', 'attributes':['long', 'black', 'flat']});
db.items.insert({_id:'rock', 'attributes':['heavy', 'rough', 'roundish']});
db.items.aggregate([{$unwind:"$attributes"}]);

```

```
use blog;
db.posts.aggregate([
    /* unwind by tags */
    {"$unwind":"$tags"},
    /* now group by tags, counting each tag */
    {"$group": 
     {"_id":"$tags",
      "count":{$sum:1}
     }
    },
    /* sort by popularity */
    {"$sort":{"count":-1}},
    /* show me the top 10 */
    {"$limit": 10},
    /* change the name of _id to be tag */
    {"$project":
     {_id:0,
      'tag':'$_id',
      'count' : 1
     }
    }
    ])
```

#####7.1. Double $unwind
```
use agg;
db.inventory.drop();
db.inventory.insert({'name':"Polo Shirt", 'sizes':["Small", "Medium", "Large"], 'colors':['navy', 'white', 'orange', 'red']})
db.inventory.insert({'name':"T-Shirt", 'sizes':["Small", "Medium", "Large", "X-Large"], 'colors':['navy', "black",  'orange', 'red']})
db.inventory.insert({'name':"Chino Pants", 'sizes':["32x32", "31x30", "36x32"], 'colors':['navy', 'white', 'orange', 'violet']})
db.inventory.aggregate([
    {$unwind: "$sizes"},
    {$unwind: "$colors"},
    {$group: 
     {
	'_id': {'size':'$sizes', 'color':'$colors'},
	'count' : {'$sum':1}
     }
    }
])

```

```

use agg;
db.inventory.drop();
db.inventory.insert({'name':"Polo Shirt", 'sizes':["Small", "Medium", "Large"], 'colors':['navy', 'white', 'orange', 'red']})
db.inventory.insert({'name':"T-Shirt", 'sizes':["Small", "Medium", "Large", "X-Large"], 'colors':['navy', "black",  'orange', 'red']})
db.inventory.insert({'name':"Chino Pants", 'sizes':["32x32", "31x30", "36x32"], 'colors':['navy', 'white', 'orange', 'violet']})
db.inventory.aggregate([
    {$unwind: "$sizes"},
    {$unwind: "$colors"},
    {$group: 
     {
	'_id': "$name",
	 'sizes': {$addToSet: "$sizes"},
	 'colors': {$addToSet: "$colors"},
     }
    }
])

```

```
use agg;
db.inventory.drop();
db.inventory.insert({'name':"Polo Shirt", 'sizes':["Small", "Medium", "Large"], 'colors':['navy', 'white', 'orange', 'red']})
db.inventory.insert({'name':"T-Shirt", 'sizes':["Small", "Medium", "Large", "X-Large"], 'colors':['navy', "black",  'orange', 'red']})
db.inventory.insert({'name':"Chino Pants", 'sizes':["32x32", "31x30", "36x32"], 'colors':['navy', 'white', 'orange', 'violet']})
db.inventory.aggregate([
    {$unwind: "$sizes"},
    {$unwind: "$colors"},
    /* create the color array */
    {$group: 
     {
	'_id': {name:"$name",size:"$sizes"},
	 'colors': {$push: "$colors"},
     }
    },
    /* create the size array */
    {$group: 
     {
	'_id': {'name':"$_id.name",
		'colors' : "$colors"},
	 'sizes': {$push: "$_id.size"}
     }
    },
    /* reshape for beauty */
    {$project: 
     {
	 _id:0,
	 "name":"$_id.name",
	 "sizes":1,
	 "colors": "$_id.colors"
     }
    }
])

```

####8. Mapping between sql and aggregation
```
where ----- $match
group by -----$group
having ----- $match
select -----$project
order by ----- $sort
limit -----$limit
sum ----- $sum
count(*)---- $sum
join ---- $unwind is a similar operation
```

####9. Limitation in aggregation framework
* 100 MB limit for pipeline stage in memory. Disk has no limitation.
* 16 MB limit for a single doc as return collection. Cursor has not limitation.
* When aggregating large size of files, all aggregation stages operate in primary shard. In this case, consider other tools, such as hadoop.


#### HW-1

Use the aggregation framework in the web shell to calculate the author with the greatest number of comments. 
```
db.posts.aggregate([{$project:{_id:0,authors:"$comments.author"}},{$unwind:"$authors"}, {$group:{_id:"$authors",num:{$sum:1}}},{$sort:{num:-1}}])
```

```
mongoimport -d blog -c posts < posts.json
```

$match sub-part
```
{
  aggregate: 'Posts',
  pipeline: [
    { $unwind: '$Comments'},
    { $match: {'Comments.Owner': 'Harry' }},
    { $group: {
      '_id': '$Comments._id'
    }}
  ]
}
```

####HW-3

Your task is to calculate the class with the best average student performance. This involves calculating an average for each student in each class of all non-quiz assessments and then averaging those numbers to get a class average. To be clear, each student's average includes only exams and homework grades. Don't include their quiz scores in the calculation. 

```
db.grades.aggregate([
    {$unwind: "$scores"},
    {$match:
        {$or:[{"scores.type":"exam"}, {"scores.type":"homework"}]}
    },
    {$group:
        {_id:"$class_id"
         avg:{$avg:"$scores.score"}
        }
    },
    {$sort:
        {"avg":-1}
    }
])
```


####HW-4

Using the aggregation framework, calculate the sum total of people who are living in a zip code where the city starts with a digit. Choose the answer below. 

```
db.zips.aggregate([
     {$project:{
               _id:0,
               first_char:{$substr:["city", 0,1]},
               pop:"$pop"
               }
     },
     {$match: {
              first_char:{$in:["0","1","2","3","4","5","6","7","8","9"]}
              }
     },
     {$group:{
              _id:0,
              total:{$sum:"$pop"}
             }
     }
])
```
