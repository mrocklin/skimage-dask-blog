Releasing the GIL
-----------------

*Section by Johannes Schönberger*

Fortunately the Cython in scikit-learn compiles down to C, not Python, so we
can release the GIL in performance critical locations.

Here is how we appraoched releasing the GIL ...
These are the problems we faced ...
And how we went about resolving them ...

It took a couple days.
