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
- the more we use stages it will take more time.
- Our Target should be like we will ue less stages
```javascript
db.Test.aggregate([
    // Stage-1: Filter for males
    { $match: { gender: "Male" } },
    
    // Stage-2: Filter for age <= 30
    { $match: { age: { $lte: 30 } } },
    
    // Stage-3: Project only selected fields
    { $project: { gender: 1, age: 1, name: 1 } },
    
    // Stage-4: Sort by age in ascending order (change 1 to -1 for descending order)
    { $sort: { age: 1 } }
])
```
#### $addFields
- If we want to add new filed with the existing field we have to use $addField. It will not modify the original document, it will just add a field in the pipeline.
```javascript
db.Test.aggregate([
    { $match: { gender: "Male" } },
    { $addFields: { course: "Level-2", eduTech: "Programming Hero" } },
    { $project: { course: 1, eduTech: 1, gender: 1 } }
])
```
#### $out
- If we want to add new fields and create a new collection with the added fields we have to use $out
```javascript
db.Test.aggregate([
    { $match: { gender: "Male" } },
    { $addFields: { course: "Level-2", eduTech: "Programming Hero" } },
    // { $project: { course: 1, eduTech: 1, gender: 1 } } // we have to take care of project because it will only add the mentioned fields
    {$out : "Course-Students"}
])
```
#### $merge
- If we want to add new fields and merge with the existing collection we have to use $merge
```javascript
db.Test.aggregate([
    { $addFields: { course: "Level-2", eduTech: "Programming Hero" } },
    {$merge : "Test"}
])
```

## Module-4 : $group , $sum , $push aggregation stage

#### $group and $sum
- with the $group stage we can perform all the aggregation or summarize queries that we need. such as finding counts, total, average or maximum.
- It is responsible for grouping and summarizing documents. It takes multiple documents and arranges them into several separate batches based on grouping.

- This will group the ages based on the duplicate number and show count. Its like if we have three person aged 75, grouping will make one group taking the three person and shw the count 
```javascript
db.Test.aggregate([
    { $group: { _id: "$age", count : {$sum:1} } }
])
```
- Accessing from nested document field
```javascript
db.Test.aggregate([
    { $group: { _id: "$address.country", count : {$sum:1} } }
])
```

#### $push
- Adds extra values into the array of the resulting document.

-This will additionally add the names who are with the country groups and count the persons
```javascript
db.Test.aggregate([
{$group : {_id : "$address.country", count : {$sum:1}, newAdditionalValue : {$push :"$name"}}}
])
```

- If we want to keep all the documents and after the project afterwards to send some fields in frontend

```javascript
db.Test.aggregate([
    { $group: { _id: "$address.country", count: { $sum: 1 }, newValue: { $push: "$$ROOT" } } },
    { $project: { "newValue.name": 1, "newValue.email": 1, "newValue.age": 1, } }
])
``` 

## Module-5 : Explore more about $group & $project