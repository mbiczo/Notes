
Statistics (Random Notes)
==========================
Personal random notes based off the book, *Statistics, 4th Edition*, by David Freedman, Robert Pisani & Roger Purves.

What is Statistics?
-------------------

*Statistics is the art of making numerical conjectures about puzzling questions.*

Study Design
------------
Study design, or the designing of experiments, is one of the most important topics in Statistics.  Without careful consideration, all following data collected could be biased, and therefore unreliable, or even false.  There are various methods and best practices to test a hypothesis by gathering data.

Controlled Experiments
'''''''''''''''''''''''

**Comparison**

This basic method is to divide a population into *control* and *treatment* groups.  The *treatment* group receives the test, and the control group remains unchanged.  The measurements or responses of both groups are then *compared*.

**Things to Note:**

* The division of the population should be randomly assigned.  
* The experiment should be ran as *double-blind*, or in other words, neither the subjects know which group they are a part of, nor the testers know what subjects they're testing.  This is one of the best methods to incorporate.  
* When an impartial chance procedure is used to randomize the assignment of subjects (such as a flipping of a coin), the experiment is referred to as *randomized controlled*.
* The control groups and treatment groups should resemble each other.
* If the treatment and control differ, the results are likely to be *confounded* with the effect of the treatment.


Observational Studies
'''''''''''''''''''''
In observational studies, the *subjects* assign themselves to which group they will be a part of while investigators just watch, rather than in *controlled* experiments where the *investigators* assign the subjects to each group.  For example, studies on smoking - subjects would not begin smoking and continue for 10 years just to collect data.  However, in the same way as controlled experiments, the investigators still use the treatment-control idea.

Statisticians talk about *controlling* confounding hidden factors.  In the above example controlling age and sex factors by comparing only smoking males to non-smoking males and ages 50-55, respectively, and so on...


**Things to Note:**

* Observational comparisons can be quite misleading.
* There is a strong *association* between smoking and disease.
* *association* is **not** the same as *causation* 
* There may be *counding* factors that are contributing to the disease.
* Good observational studies *control* for confounding variables.

Study Types
'''''''''''

Two types of studies are *cross-sectional* and *longitudinal*.

In **cross-sectional** studies different subjects are compared to eachother during a snapshot in time.

In **longitudinal** studies a subject is followed over time and compared with herself over time.

**NOTE:**  If a study draws conclusions about the effects of age, find out whether the data are cross-sectional or longitudinal.


Descriptive Statistics
-----------------------


Histogram
---------

Histograms are used to help summarize and describe various questions about population distributions.

In a histogram, the *areas* of the blocks represent *percentages* which should add up to 100%.  The **endpoint convention** includes the rule on what to do with a data point that falls on the boundary between two class intervals.  For example a footnote may say, 'the class intervals *include* the left endpoint'

Drawing a histogram
'''''''''''''''''''

1. Start with a *distribution table* (shown below).  

================   ===========
Income Level       Percentage
================   ===========
$0-$1,000          1%
$1,000-$2,000      3% 
...                ...
$50,000 and over   10%
================   ===========

2. Make sure the x-axis has an even distribution or is *linear*
3. To find the *height* of a block over a *class interval*, divide the percentage by the length of the interval

**Example:**

.. image:: /img/stats/Histogram1.png


Density Scale
'''''''''''''

To help illustrate your data a vertical scale can help.  In some cases a *density scale* may be a good choice.  A density scale includes the percentage *per unit on the x-axis*.  For example, if the x-axis is shown in years, then a vertical (y-axis) of Percent by year may be appropriate.  Necessary scaling and area assignment is necessary.

**NOTE** In a histogram, the heights of the block represents *crowding*, or percentage per horizontal unit.


Variables
'''''''''

In Statistics, a *variable* is a characteristic which changes from observation to observation.  For example, a person's age, sex, family size, occupation, income, etc...

Variable types are categorized as follows:

* Qualitative, or descriptive such as sex, occupation, etc...

* Quantitative, or a number such as age or family size. These can be *continuous* or *discrete*.

  - Discrete variables only differ from fixed (whole) amounts such as in family size 0, -1, 2 more people.
  - Continuous variables such as age can be arbitrarily small.  3 years, 2 months, 33 seconds, etc..
    
When drawing a Histogram with continuous variables, be sure to consider the *endpoint convention*.  When working with discrete variables, the common solution is to center the values at each class interval.
	

Controlling for a Variable
''''''''''''''''''''''''''

A quick note on controlling variables.  In an example, let's say, to *control* for age would mean to break out a general population into smaller sub-sets of age ranges so that other *coundounding* factors aren't at play - or skewing the numbers.


Cross-Tabulation
''''''''''''''''

Another way to display histogram data could be a *cross-tab* or tabulation table.  A trimmed example from the book is shown below:


.. image:: /img/stats/crosstab.png


The Average and Standard Deviation
-----------------------------------

In order to achieve a more drastic summary of your data - apart from only the Histogram - measuring the *center* and the *spread* around the center can help.

Center
''''''
Measurements of center can be found by calculating the *average* and/or the *median*.

**Average**
The *average* of a list of numbers equals their sum divided by how many there are.

.. image:: /img/stats/AvgExample.png


**Median**

The *median* is the middle number when all numbers are lined up from smallest to largest.  If the list of numbers is even (meaning there are two center numbers), then add the two numbers together and divide by 2 to find the median.

**Root Mean Squared (RMS)**

The RMS, also knows as the *quadratic mean*  is another tool that statisticians use to calculate the *size* of a series of numbers.  The calculation is its name backwards.  First, square the entries.  Next, take the mean of the newly calculated numbers, and finally, calculate the square root in order to find the RMS.

TODO: add formula


Spread
''''''
The *standard deviation* measures spread around the average.  The *interquartile range* is another measure of spread.

**Standard Deviation**

The standard deviationn tells how far away numbers on a list are from their average.  Most entries (~68%) will be around one (1) SD away from the average.

The standard deviation is the RMS of their deviations from the average.  In other words, to calculate the SD from a list of numbers, first, find the average and calculate each number's deviation or distance from that average, then continue with the RMS calculation.

.. image:: /img/stats/SD_Equation_Alt.png



**SD vs SD+**


Research the difference between standard deviation (SD) and SD+.  A test would be to calculate the standard deviation of [-1, 1] and if the output equals 1, then the machine is using regular standard deviation.  If the output equals 1.41... it's using SD+


**Interquartile Range (IQR)**

The IQR is useful in summarizing non-normal disturbutions, or data distributions that do not follow the Normal Curve - this will be described a bit more in-depth, but more on this later.  By definition, the Interquartile range is the data that lies between the 25th and 75th *percentile*.


Normal Approximation for Data
------------------------------

The **The Normal Curve** was discovered during the development of the mathematics of chance.  Later, it was used as an ideal histogram to compare other data and histograms to.


The Normal Curve
'''''''''''''''''

The equation has three of the most famous numbers in the history of mathematics as well as a familiar shape:

.. image:: /img/stats/Normal_Curve_Equation.png

.. image:: /img/stats/Normal_Curve.png

**Normal Curve Features**

The areas under the normal curve between -1 and +1 is about 68%
The areas under the normal curve between -2 and +2 is about 95%
The areas under the normal curve between -3 and +3 is about 99.7%

**NOTE**  A value is converted to *standard units* by seeing how many SDs it is above (+) or below (-) the average.


Percentiles
'''''''''''

Not all distributions are *normal*.  Other examples may be *right-skewed* and *left-skewed*.  For these, statisticians often use **percentiles** to summarize the data.  Otherwise, if we applied the same math as above, we'd end up with the estimation that some families make negative income (-1.5 SDs)

Also, as mentioned above, the *Interquartile Range (IQR)** is also helpful in summarizing these types of data shapes (having longer tails).  See an example of a *right-skewed* distribution below.


.. image:: /img/stats/Right_Skewed_Dist.png

.. image:: /img/stats/Percentile_Ex.png









