


####5. Double grouping

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

####7. $match

One thing to note about $match (and $sort) is that they can use indexes, but only if done at the beginning of the aggregation pipeline.


use agg
db.zips.aggregate([
    {$match:
     {
	 state:"NY"
     }
    }
])



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



####8. $sort

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
    }
      
    
     
])


```
