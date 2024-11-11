# Mongodb-Aggregation-And-Indexing

## Module-1 : Introduction to powerful aggregation framework

#### What is Aggregation?
- Aggregation is a way of processing a large number of documents in a collection by means of passing them through different stages.
- The stages make up what is known as pipeline
- The stages in a pipeline can filer,sort, group, reshape and modify documents that pass through the pipeline
- Syntax
```javascript
db.collection.aggregate(
    [
        // stage-1
        {} --->pipeline
        // stage-2
        {} --->pipeline
        // stage-3
        {} --->pipeline
    ]
)
``` 
- Data flow : Collection -> Stage1 -> Stage-2 -> Stage-3 -> Final Output

- Rough Example
```javascript
db.cousins.aggregate([
    // filter out the cousins who have exam
    {$match : {hasExam : {$ne :true}}},

    // Filter out cousins who have a budget less than 5000
    {$match :{budget : {$gte :5000}}},

    // filter out cousins who is sick
    {$match : {isSick :true}},

    // sort by age
    {$sort :{age:-1}},

    // limit by 2 
    {$limit :2},

// calculate budget
{
    $group :
    {
        _id:"null",
        totalBudget : {$sum : "$budget"},
        cousins : {$push : "$name"}
    }
}
])
```

## Module-2 : $match , $project aggregation stage

#### $match
- It allow to choose just those documents from a collection that we ant to work with. It does this by filtering out those that do not follow our requirement

#### $project
- In MongoDB, the $project aggregation stage is used to shape the structure of the documents that result from a pipeline. 

#### Difference between regular mongodb to aggregation
```javascript
// Regular
db.test.find({})

// using aggregation
db.test.aggregate([])
```

```javascript
// Regular
db.test.find({
    gender:"Male"
})

// Using aggregation
db.test.aggregate([
    {$match : {gender : "Male"}}
])
```

```javascript
// Regular
db.test.find({
    gender:"Male" , age: {$lte :30}
}).project({name:1,age:1,gender:1})

// Using aggregation
db.test.aggregate([
    // stage-1
    {$match : {gender : "Male"}}

    // stage-2 ---> we have to use $project always at the end 
    {$project : {name:1, age:1, gender:1}}
])
```
## Module-3 : $addFields , $out , $merge aggregation stage