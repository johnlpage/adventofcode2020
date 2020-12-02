Download puzzle input to input.txt, it's unique for everyone but I put mine here as an example.

1. Start MongoDB or use a free Atlas Cluster from (cloud.mongodb.com)

2. Install the mongo SHell (https://docs.mongodb.com/manual/mongo/)

3. Install the MongoDB database tools including MongoImport (https://docs.mongodb.com/database-tools/)




Load Data

    mongoimport -d advent -c day01 --type csv --fields=expense  "mongodb+srv://cluster0.myfbu.mongodb.net" --username jlp --drop  input.txt 

Open the mongo shell


    mongo "mongodb+srv://cluster0.myfbu.mongodb.net/advent" --username jlp

Calculate
```
> show collections
day01
> db.day01.count()
200
> db.day01.findOne()
{ "_id" : ObjectId("5fc7528d1f58d5dd3f3dc676"), "expense" : 1935 }
> db.day01.createIndex({expense:1})
{
	"createdCollectionAutomatically" : false,
	"numIndexesBefore" : 1,
	"numIndexesAfter" : 2,
	"ok" : 1,
	"$clusterTime" : {
		"clusterTime" : Timestamp(1606898370, 2),
		"signature" : {
			"hash" : BinData(0,"BmB3g+JRvnFaASJ/MI/ZA8PDP2k="),
			"keyId" : NumberLong("6865651496357199874")
		}
	},
	"operationTime" : Timestamp(1606898370, 2)
}


var add_target = { $addFields : { target : { $subtract : [ 2020, "$expense"]} }}
var lookup = { $lookup : { from : "day01",
              localField: "target",
              foreignField: "expense",
              as: "matches_found" }}
var find_match = { $match : { matches_found : { $size : 1}}}
var first = { $limit:1} // Stop when we get the first value

var multiply_for_answer = { $project : { result : { $multiply : [ "$expense","$target"  ]}}}
db.day01.aggregate([add_target,lookup,find_match,first,multiply_for_answer])

{ "_id" : ObjectId("5fc7528d1f58d5dd3f3dc690"), "result" : 145875 }

```


Overall complexity? Well O(n log n) to build an index and O(n log n) to get the answer

