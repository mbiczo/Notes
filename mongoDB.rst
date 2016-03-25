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
**Finding Items**

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

Inserting, Updating & Deleting
------------------------------

**Inserting**

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


Insert multiple documents using ``insert_many()`` and a python ``list``

.. code:: python

  # Pass a list to be inserted
  people_to_insert = [james, susan]
  
  try:
    #script will insert until/when an error is encounted, then exception out
    people.insert_many(people_to_insert, ordered=True)
  


**Updating**

**Deleting**



      

References
----------
BSON reference: http://bsonspec.org/

    
