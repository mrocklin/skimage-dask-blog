Scikit-image
------------

*Section by Stefan van der Walt:*

*  Scikit image builds off of NumPy with custom Cython code
*  Algorithms are sophisticated, used daily, but generally restricted to
   a single-core.
*  Parallelism is available because operations are local, but hard because they
   often overlap just a little bit, precluding simple embarrassingly parallel
   solutions
