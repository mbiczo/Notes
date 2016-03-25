===================
R&D NoSQL Databases
===================
Purpose:
  Research the benefit and differences between a NoSQL data model versus a traditional RDBMS.

MongoDB
=======
MongoDB is an open-source, document database designed for ease of development and scaling.  
A record in MongoDB is a document, which is a data structure composed of field and value pairs. 
MongoDB documents are similar to JSON objects. The values of fields may include other documents, arrays, and arrays of documents.

Working with Collections - JavaScript
-------------------------------------
*Finding Items*

  1. Find all items
    db.<collection>.find() - returns all fields - same as SELECT *
  2. Filter logic and columns - also works with nested arrays
    db.<collection>.find({"name":"James", "age": 25}, {"name":true, "age":false})
    db.<collection>.find({"balance" :{$gte: 0, $lte: 1000}})
    
    $type
      Searches for certain data types based on BSON int mapping here.
    $regex
      Searches string values
    $exists
      Checks to see if item is present or not
    $all : ["pretzels", "beer"]
      Used to query multiple values - ALL MUST MATCH (and)
    $in : ["Howard", "John"]
      Used to query multiple values - ANY VALUES MUST MATCH (or)
    $or
      ex: 
    $and
      ex:
      
  3. Cursors - manipulating server cursor on DB
    cur = db.people.find(); null;
    cur.hasNext()
    cur.next()
    cur.limit(5)
    cur.sort( {name: -1} )
    
  4. Counting
    count()

*Updating Items
  Performs a wholesale replacement - *BE CAREFUL DOING THIS
  1. db.<collection>.update( {name : "Smith" }, { name : "Thompson" , salary : 50000 })
  
  Just update individual field
  2. db.<collections>.update( {name : "Alice" }, { $set : {age : 30}})

  3. $unset : { field: 1}
    Note: the value is arbitrary and isnored 
  
  4. $upsert : true
    updates record if exists, otherwise inserts
    
  5. Update multi items - may process some docs then others as they unlock
    > must add: multi : true
    
  6. Increment field
    > $inc()
    
  7. Remove documents
   > db.<collection>.remove( { } )
    Will remove all docs
      
References
----------
BSON reference: http://bsonspec.org/

    
