My reStructuredText Cheat Sheet
===============================
Adapted from `PEP 0012`_ - Simple reStructuredText Template

.. _PEP 0012: https://www.python.org/dev/peps/pep-0012/#habits-to-avoid/


First-Level Title
=================

Second-Level Title
------------------

Third-Level Title
'''''''''''''''''

*emphasized*

**strongly emphasized**

``$Inline literals (code)``


* list item 1
* list item 2
* list item 3
  
  - sublist item a
  - sublist item b
  
  
=======  =======  ===========
   A        B       A + B
=======  =======  ===========
row1      True     row1 True
row2      False    row2 False
row3      True     row3 True
=======  =======  ===========


.. code:: python

  #create a code block of python
  for i in range(10):
    print(i)
  

Let's create a hyperlink and go to the `Hackerrank`_

.. _Hackerrank: https://www.hackerrank.com/


This sentance has a footnote - please refer to [GitHub]_ for more info

.. [GitHub] This is the GitHub footnote
