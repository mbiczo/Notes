MongoDB Quick Ref Guide
=======================

**Purpose**

Research the benefit and differences between a NoSQL data model such as MongoDB versus a traditional RDBMS.

Document DB
-----------
MongoDB is an open-source, document database designed for ease of development and scaling.  
A record in MongoDB is a document, which is a data structure composed of field and value pairs. 
MongoDB documents are similar to ``JSON`` objects. The values of fields may include other documents, arrays, and arrays of documents.

Visit MongoDB `Reference Guide`_ for additional help

.. _Reference Guide: https://docs.mongodb.org/manual/reference/


PyMongo - Using Python 2.7
--------------------------

PyMongo is a distribution containing tools for working with MongoDB and is recommended way to work with MongoDB from Python.  It must be imported to each Python script with ``import pymongo``


Quick Import from JSON
'''''''''''''''''''''''

``mongoimport -d database_name -c collection_name < file.json``

There has been a known issue in some import jobs where you just need to append ``--batchSize 1`` to the end of the previous command.


Finding Items
'''''''''''''

**Find one item**

Find one item using ``collection.find_one()`` and ``operators`` such as ``$lt`` and ``$gt``, or greater than.

.. code:: python

  def find_one():
    query = {'field1':'value', 'field2': {'$gt': 50, '$lt':70}}  #greater than 50 and less than 70
    try:
      doc = collection.find_one(query)
    except Exception as e:
      print "Unexpected error", type(e), e
      
    print doc


Visit Mongo's page on `Operators`_ for additional features

.. _Operators: https://docs.mongodb.org/manual/reference/operator/


**Sorting**

Sort returned results.  Use a `list` of `tuples` for compound sorting.

.. code:: python

  doc = collection.find_one(query).sort('grade', pymongo.ASCENDING)
  
  # multi-sort
  doc = collection.find_one(query).sort([('grade', pymongo.ASCENDING), ('date', pymongo.DESCENDING)])


**Find many itmes**

Find all documents using ``collection.find()`` and a ``for`` loop only returning *projected* fields

.. code:: python

  def find():
    query = {'field1':'value'}
    
    # Shows field1, hides _id field (default = yes)
    projection = {'field1: 1, '_id': 0}
    
    try:
      cursor = collections.find(query, projection)  #add projection to find()
    except Exception as e:
      print "Unexpected error:", type(e), e
      
    sanity = 0
    for doc in cursor:
      print doc
      sanity += 1
      if (sanity > 10):
        break


**Using regex**

.. code:: python

  query = {'title': {'$regex': 'apple|google', '$options': 'i'}}  #case [i]nsensitive

Inserting
'''''''''

**Insert one record**

Insert one record at a time using ``insert_one()``

.. code:: python

  james = {'name': 'James Westfield', 'company': 'Waste Management',
          'interests': ['eating', 'sleeping', 'more sleeping']}  #no _id provided
  susan = {'_id': 42, 'name': 'Susan B', 'company': 'Google',
          'interests': ['data science', 'statistics', 'eating']}  #_id provided
          
  try:
    people.insert_one(james)
    people.insert_one(susan)
    
  except Exception as e:
    print "Unexpected error:", type(e), e
          
          
**Note:** If a document **has** an ``_id``, Mongo will insert the doc without appending anything.  On the second insert, an exception will be thrown.

**Note:** If a document **does not** have an ``_id``, Mongo will add one, then insert the doc.  On subsequent inserts, the doc **WILL** be inserted with a new ``_id`` as a new object.

**Insert Many**

Insert multiple documents using ``insert_many()`` and a python ``list``

.. code:: python

  # Pass a list to be inserted
  people_to_insert = [james, susan]
  
  try:
    #script will insert until/when an error is encounted, then exception out
    people.insert_many(people_to_insert, ordered=True)

Updating
''''''''

**Update One using** ``$set``

**VERIFY** Using ``$set`` only modifies *part* of the document in place rather than a wholesale replacement of the document such as using ``replace_one()``

.. code:: python

  try:
    #  Pass the pk in as the first arg to get one
    result = scores.update_one({'_id': primary_key}, {'$set': {'review_date': datetime.datetime.utcnow()}})

**Update Many using** ``$set``

.. code:: python

  try:
    #  Pass an empty dict to select all
    result = scores.update_many({}, {'$set': {'review_date': datetime.datetime.utcnow()}})
    

**Update One using** ``replace_one(<doc_filter>, <update operation>)``

This operation uses ``_update`` in that it performs a wholesale replacement of the document.  In other words, it will send the whole document back to the server to overwrite the *existing* or old document.  

**CAUTION: This transaction is not atomic - and has a window of vurnerability that may expose your document.**

.. code:: python

  # Get the doc you want to update
  doc = collection.find_one(filter)
  
  # Modify doc as needed such as appending a new field
  doc['new_field'] = 'something new'
  
  # Replace existing doc with modified doc
  collection.replace_one({'_id': primary_key}, doc)
  

**The Upsert**

By setting ``upsert=True`` within ``update_one`` or ``update_many``, Mongo will attempt to find a match to the document using the document filter provided.  If the document exists, an ``upsert`` with ``$set`` is performed as expected, otherwise, if the document is not found, it will be inserted, then the subsequent ``upset`` is performed on the new doc.

With ``replace_one``, if no document matches the provided filter, that doc **is not** inserted.  Only the ``replacing doc`` will be inserted.

.. code:: python
  
  # start fresh
  things.drop()
  
  # using update
  things.update_one({'thing':'apple'}, {'$set':{'color':'red'}}, upsert=True)
  collection.update_many({'thing':'banana'}, {'$set':{'color':'yellow'}}, upsert=True)
  
  # only the replacing doc will be inserted if no match is found
  things.replace_one({'thing':'pear'}, {'color':'green'}, upsert=True)
  
  > db.things.find()
  { "_id" : ObjectId("56f71cbe3b6d1d66ca9717c7"), "thing" : "apple", "color" : "red" }
  { "_id" : ObjectId("56f71cbe3b6d1d66ca9717c8"), "thing" : "banana", "color" : "yellow" }
  { "_id" : ObjectId("56f71cbe3b6d1d66ca9717c9"), "color" : "green" }
  > 


Deleting
''''''''

**Delete one**

Use ``collection.delete_one(doc_criteria)`` to delete one document.  If multiple documents match your criteria, only the first one is removed.  

**Delete many**

Use ``collection.delete_many()`` to delete many documents.


**find_and_modify**

**RESEARCH** use this to prevent the window of attack when grabbing a document and updating a value.


Aggregation
-----------

Aggregation in MongoDB is similar to SQL's `GROUP BY` clause where you can perform sums, averages, min, max and much more.  The framework contains a series of events or *stages* such as ``$group``, ``$match``, ``$limit``, ``$sort`` & ``$project`` to name a few.  Be sure to familiarize yourself with the various `stages of the aggregation framework`_.

.. _stages of the aggregation framework: https://docs.mongodb.org/manual/meta/aggregation-quick-reference/

**Note:** To use ``$text`` in the ``$match`` stage, the ``$match`` stage has to be the first stage of the pipeline.

**Note:** All stages are limited by **100MBs** of RAM by default.  This can be changed using *Aggregation Options*

Aggregation Options
'''''''''''''''''''

Use various *options* to enhance the aggregation framework:  Options can include:

* ``{explain: true}``  *javascript*
* ``{allowDiskUse: true}``  *javascript*
* ``{cursor= {}}``  *python*

Aggregation Limitations
'''''''''''''''''''''''

* 100MB limit for pipline stages
* 16MB limit for documents
* Sharding issues with 



Storage Engines
---------------
MongoDB storage engines sit between the Mongo server and physical disk.  It determines 2 primary things: the **data file format** and the **format of indexes**.  In Mongo, there are 2 built-in engines you can use: **MMAPv1** and **WiredTiger**.

MMAPv1
''''''

The basic default engine used by MongoDB as well as other Virtual Memory management systems.  View basic help in Terminal by typing ``man mmap``

Some basics - research in-depth:

* MMAPv1 is built on top of mmap
* MMAPv1 automatically allocates *power-of-two-sized documents* when new docs are inserted.
* Offers **Collection Level** locking.

WiredTiger
''''''''''

WiredTiger was aquired by MongoDB in 2014.  It is **not** turned on by default, and in many cases can handle work-loads a bit more efficiently.

Some basics - research

* Document Level Concurrency -  uses *optimistic locking* assumes 2 writes won't be to the same document
* Compression of docs and indexes
* No Inplace updates - appends at the end, then frees memory over time.

To use:

``killall mongod``

``mkdir WT``

``mongod -dbpath WT -storageEngine wiredTiger``

**NOTE: WiredTiger cannot read MMAPv1 documents**


Indexes
-------

Indexes are one of the single most important things you can do to optimize read queries.  However, maintaining indexes does slow writes, technically.  Be sure to review and understand examples of creating `indexes to support your queries`_ and `using indexes to sort your queries`_.  Here are basics:

.. _indexes to support your queries: https://docs.mongodb.org/manual/tutorial/create-indexes-to-support-queries/

.. _using indexes to sort your queries: https://docs.mongodb.org/manual/tutorial/sort-results-with-indexes/

To **create** an index on a collection named *students*, havingthe index key be *class, student_name*:

``db.students.createIndex({"class": 1, "student_id": 1})``

To **get** all current indexes on a collection:

``db.collection.getIndexes()``

To **drop** or remove an Index on a collection:

``db.collection.dropIndex({ 'key': <value_direction>})``

Multikey Indexes
''''''''''''''''

MongoDB will automatically set multiIndex = True when an indexed field is an array type.  MongoDB **cannot** insert a document where 2 or more indexed field are array types.

You can also set indexes on nested items using the dot notaion.

``db.collection.setIndex({"scores.score": 1})``

Be careful when querying sub document logic and possibly use the ``$elemMatch`` operator instead of the ``$and`` operator.  For example, if you wanted to find all individuals from a student collection with an *exam* score of greater than 99.8 try:

``db.students.find({"scores": {"$elemMatch": {"type": "exam", "score": {"$gt": 99.8}}}})``

Unique Indexes
''''''''''''''

Similar syntax as before, but places the **unique** constraint on the index so only one document can be inserted with that id.

``db.collection.createIndex({<field>, <direction>}, {unique: true})``

Sparse Indexes
''''''''''''''

Sparse indexes occur when not all keys being indexed are present in the data.  To account for this while creating indexes, use the ``sparse`` option:

``db.collection.createIndex({<field>, <direction>}, {sparse: true})``


Full Text Search Index
''''''''''''''''''''''

Allows for easier text search and parsing in documents that have small or large strings.  To create a full text search index, use the following command with the **text** argument:

``db.collection.ensureIndex({'<field>':'text'})``

To use the newly created index:

``db.collection.find({$text:{$search: '<string>'}})``

For multi-word search:

``db.collection.find({$text:{$search: '<string> <string> <string>'}})``

To have Mongo attempt to sort the documnets in order of search match *importance*, use the ``$meta: 'textScore'`` argument in conjunction with the ``sort()`` method like this:

``db.collection.find({$text:{$search: 'dog tree obsidian'}}, {score: {$meta: 'textScore'}}).sort({score: {$meta: 'textScore'}})``

When to create Indexes?
'''''''''''''''''''''''
There are 2 options when to create your indexes in MongoDB.  **Foreground** is the default, otherwise you can choose **Background**

* **Foreground**

  * Relatively Fast
  * Blocks all writers and readers in the database (probably don't do in production)

* **Background**

  * A bit slower
  * Doesn't block database reads or writes
  * As of Mongo 2.4, you can only build more than 1 index at a time

To create an index in the background, set the *background* option to true:

``db.collection.createIndex({<field>: <direction>}, {background: true})``


Slow Queries
''''''''''''

MongoDB by default logs all *slow* queries, or queries that take longer than **100ms** in the text logs.

Also, you can use the **Profiler** to log various levels of debugging.  Start up mongod like the following:

``mongod -dbpath \path\to\db --profile <level> --slowms <int>``

The levels are as follows:

* 0 - None
* 1 - Slow Queries, specify number of ms with ``--slowms``
* 2 - All queries


Using Explain
'''''''''''''
Use ``.explain()`` to find out vital information regarding database statistics and query execution plans.  Returns an *explainable object*

**queryPlanner**

Returned by default.  Shows query plan information including the *winning plan* that was executed and indexes used, if any:

``db.collection.explain().find(<somequery>)``

**executionStats**

Returns statistics on number of documents examined, keys examined, documents returned, execution time, ect:

``db.collection.explain('executionStats').find(<somequery>).sort(<sortlogic>)``


mongotop & mongostat
''''''''''''''''''''

**mongotop**

Taken from the Unix ``top`` command that shows the CPUs most expensive processes, Mongo has a similar command called ``mongotop <int>`` that shows what MongoDB is spending most of its time on.  To see Mongo's top processes for 10 seconds, call the following from the command line:

``$ mongotop 10``

**mongostat**

Shows the db statistics **in  1 second interval** for *inserts*, *updates*, *deletes*, etc...   It can be called from the command line as such:

``$ mongostat``


Sharding
--------

Split data among different Mongo servers to distribute the workload using **mongos**.  A *shard key* should be provided for higher efficiency, and so that extra broadcasting is not occuring.






References
----------
`BSON reference`_ 

.. _BSON reference: http://bsonspec.org/

    
