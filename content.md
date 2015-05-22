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

Scikit-Image needed a way to parallelize its filtering functions. Dask can do this. Given a NumPy array we'd want to:

1. to create a dask array from the numpy array.
2. "ghost" the array with a depth specified by the user
3. apply a function to each chunk
4. trim the ghost cells
5. re-combine our chunks into a NumPy array and return the result

Scikit-Image could make its own function to do this, but it seems like a common enough routine that we would want wrap it up into a function. So we added the `dask.array.ghost.map_overlap` method. This handles steps 2 - 4.

We decided to leave the dask array creation up to scikit-image so they could choose the appropriate chunk layout.So in Scikit-Image we added `skimage.util.apply_parallel` which handles steps 1 and 5. Skimage's most common usecase is optimizing for parallelization (instead of handling out-of-core data), so we break up the numpy array into `count_cpu()` chunks. The ghosting depth must also be chosen by the skimage user in `apply_parallel` because there is no simple way to infer this by inspecting the input function. And the user of skimage should be knowledgeable about the size of the filter's [kernel](https://en.wikipedia.org/wiki/Kernel_%28image_processing%29).  So in skimage we basically ended up with

```python
import dask.array as da

def apply_parallel(nparr, function, chunks=None, depth=0):
   if chunks is None:
      chunks = _choose_chunks(nparr.shape, count_cpu())
   darr = da.from_array(nparr, chunks)
   return darr.map_overlap(func, depth=depth).compute()
```

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
