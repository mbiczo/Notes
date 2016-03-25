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


      

References
----------
BSON reference: http://bsonspec.org/

    
