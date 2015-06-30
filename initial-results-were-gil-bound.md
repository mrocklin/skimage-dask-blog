Initial results were GIL bound
------------------------------

*Section by Arve Seljebu:*

If we do a simple experiment ... and find that we don't get much speedup ...

This is because of the GIL, which stops multiple Python threads from operating
at once.
