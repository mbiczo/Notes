MongoDB Quick Ref Guide
=======================
Purpose:
  Research the benefit and differences between a NoSQL data model such as MongoDB versus a traditional RDBMS.

Document DB
===========
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



References
----------
BSON reference: http://bsonspec.org/

    
