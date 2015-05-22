SciKit Image and Dask Array
===========================

[Scikit-image](http://scikit-image.org/) is a library for image analysis.  It
contains tuned implementations for common image analyses like filtering, blob
finding, and edge detection.
[Dask](http://dask.pydata.org) is library for parallel computation.
Dask.array is a NumPy clone that supports multi-core execution of arrays in
memory or on disk.
Recent work integrates the two, enabling many of the image processing functions
in scikit-image to leverage the multiple cores of a modern CPU.


Scikit-image
------------

*Section by Stefan van der Walt:*

*  Scikit image builds off of NumPy with custom Cython code
*  Algorithms are sophisticated, used daily, but generally restricted to
   a single-core.
*  Parallelism is available because operations are local, but hard because they
   often overlap just a little bit, precluding simple embarrassingly parallel
   solutions


Dask.array
----------

*Section by Matthew Rocklin:*

*   Dask.array cuts blocks out of a large array and orchestrates many numpy
    calls in parallel.
*   Dask.array.ghost manages slightly overlapping sub-arrays, solving
    scikit-image's situation well


Efforts to combine the two
--------------------------

*Section by Blake Griffith*

We came up with the following API to cross projects.  The following issues came
up ...


Initial results were GIL bound
------------------------------

*Section by Arve Seljebu:*

If we do a simple experiment ... and find that we don't get much speedup ...

This is because of the GIL, which stops multiple Python threads from operating
at once.


Releasing the GIL
-----------------

*Section by Johannes Schönberger*

Fortunately the Cython in scikit-learn compiles down to C, not Python, so we
can release the GIL in performance critical locations.

Here is how we appraoched releasing the GIL ...
These are the problems we faced ...
And how we went about resolving them ...

It took a couple days.


Now things work well
--------------------

*Section by Arve Seljebu:*

Final demonstration with speed difference.


Final thoughts
--------------

*  Importance of speed in interactive data science
*  The GIL is surmountable
*  Inter-project collaboration yields low-hanging fruit
