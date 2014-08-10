* determining equality -- `is` vs `==`
    *
        >>> [] is []
        False
        >>> {} is {}
        False
        >>> list() is list()
        False
        >>> dict() is dict()
        False
        >>> tuple() is tuple()
        True

        >>> 0 is 0
        True
        >>> float(0) is float(0)
        False

        >>> a = 'cat'
        >>> b = 'cat'
        >>> a is b
        True
        >>> c = ''.join(['c', 'a', 't'])
        >>> c
        'cat'
        >>> a is c
        False
    * for that last example, see string interning: http://stackoverflow.com/questions/15541404/python-string-interning
    * integers less than 256
    * integers greater than 256
    * on the same line of code!
    * Allison's puzzle
    * difference between comparing *values* and comparing *the same object in memory*
    * ways to avoid it (always use `==` when comparing *values*)
* mutability
    * GAME!
        >>> favorite_things = ['cats', 'dragons']
        >>> copy_of_favorite_things = favorite_things
        >>> copy_of_favorite_things.append('rainbows')
        >>> copy_of_favorite_things
        ???
        ['cats', 'dragons', 'rainbows']
        >>> favorite_things
        ???
        ['cats', 'dragons', 'rainbows']
    * why does this happen? well, assignment (`=`) creates a link between a name and an object. multiple names can be linked to the *same object*, which is what happens here.
    * make a real copy!
        >>> favorite_things = ['cats', 'dragons']
        >>> copy_of_favorite_things = favorite_things[:]
        >>> copy_of_favorite_things.append('rainbows')
        >>> copy_of_favorite_things
        ['cats', 'dragons', 'rainbows']
>>> favorite_things
['cats', 'dragons']
* mutable default arguments
    * my blog post
    * functions are objects!
        * you can modify other parts of the function, like the code itself!! `func_code`!
    * `func_defaults`
    * can be modified outside of the function!
    * ways to avoid it
        * use `None` as the default
* related: modifying mutable arguments inside tuples
    >>> tup = ([], [])
    >>> tup[0] += [1]
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    TypeError: 'tuple' object does not support item assignment
    >>> tup
    ([1], [])
    * http://emptysqua.re/blog/python-increment-is-weird-part-ii/
* scope!
    * let's play... what will happen??
    * ex1
        >>> a = 1
        >>> def foo():
        ...     print a
        ...
        >>> foo()
        1
    * ex2
        >>> def foo():
        ...     print a
        ...
        >>> a = 1
        >>> foo()
        1
    * ex3 -- what if you tied modifying `a` and then `print`ing?
        >>> def foo():
        ...     a += 1
        ...     print a
        ...
        >>> a = 1
        >>> foo()
        Traceback (most recent call last):
          File "<stdin>", line 1, in <module>
          File "<stdin>", line 2, in foo
        UnboundLocalError: local variable 'a' referenced before assignment
    * reference: http://eli.thegreenplace.net/2011/05/15/understanding-unboundlocalerror-in-python/
    * what happens if you `print a` before assignment? would `a` get printed before the error is thrown? or not?
        >>> def foo():
        ...     print a
        ...     a += 1
        ...
        >>> a = 1
        >>> foo()
        Traceback (most recent call last):
          File "<stdin>", line 1, in <module>
          File "<stdin>", line 2, in foo
        UnboundLocalError: local variable 'a' referenced before assignment
    * so *when `foo` is defined*, Python stores information about the variables inside `foo`. what information does it store?
    * how do you avoid this error? it depends on what you want to do!
        * if you actually want to modify the global variable `a` (which is probably a bad idea:
            >>> def foo():
            ...     global a
            ...     a += 1
            ...     print a
            ...
            >>> a = 1
            >>> foo()
            2
            >>> a
            2
        * if you don't actually want to modify the global variable `a`:
            >>> def foo(a):
            ...     a += 1
            ...     print a
            ...
            >>> a = 1
            >>> foo(a)
            2
            >>> a
            1
    * bytecode
        >>> def foo():
        ...     print a
        >>> dis.dis(foo)
          2           0 LOAD_GLOBAL              0 (a)
                      3 PRINT_ITEM
                      4 PRINT_NEWLINE
                      5 LOAD_CONST               0 (None)
                      8 RETURN_VALUE
        >>> def foo1():
        ...     a += 1
        ...     print a
        ...
        >>> dis.dis(foo1)
          2           0 LOAD_FAST                0 (a)
                      3 LOAD_CONST               1 (1)
                      6 INPLACE_ADD
                      7 STORE_FAST               0 (a)

          3          10 LOAD_FAST                0 (a)
                     13 PRINT_ITEM
                     14 PRINT_NEWLINE
                     15 LOAD_CONST               0 (None)
                     18 RETURN_VALUE
        >>> def foo2():
        ...     a = 1
        ...     print a
        ...
        >>> dis.dis(foo2)
          2           0 LOAD_CONST               1 (1)
                      3 STORE_FAST               0 (a)

          3           6 LOAD_FAST                0 (a)
                      9 PRINT_ITEM
                     10 PRINT_NEWLINE
                     11 LOAD_CONST               0 (None)
                     14 RETURN_VALUE
        >>> def foo3():
        ...     global a
        ...     a+=1
        ...     print a
        ...
        >>> dis.dis(foo3)
          3           0 LOAD_GLOBAL              0 (a)
                      3 LOAD_CONST               1 (1)
                      6 INPLACE_ADD
                      7 STORE_GLOBAL             0 (a)

          4          10 LOAD_GLOBAL              0 (a)
                     13 PRINT_ITEM
                     14 PRINT_NEWLINE
                     15 LOAD_CONST               0 (None)
                     18 RETURN_VALUE
* (scope in closures) http://eev.ee/blog/2011/04/24/gotcha-python-scoping-closures/
* comparing different types of objects
    * `1 < 'cat'`
    * `foo > 1` (where `foo` is a function)
    * `foo > 1000000000000000`
    * there are things greater than infinity!
        >>> infinity = float('inf')
        >>> infinity
        inf
        >>> type(infinity)
        <type 'float'>
        >>> foo > infinity
        True
    * why is this even a thing? shouldn't we expect to get an error?
* booleans are subclassed from `int`!
    *
        >>> True + 1
        2
    *
        >>> 1 == True
        True
    * using a mix of 0, 1, True, and False as keys in a dict: http://utcc.utoronto.ca/~cks/space/blog/python/EqualityDictSurprise (i.e. they has to the same value)
    * in 2.x, `True` and `False` aren't keywords!
        >>> True, False = False, True
        >>> True
        False
        >>> False
        True
* `+=` adding an attribute to an instance:
    >>> class Foo(object):
    ...     bar = 1
    ...
    >>> f = Foo()
    >>> f.bar
    1
    >>> f.__dict__
    {}
    >>> Foo.bar
    1
    >>> f.bar += 1
    >>> f.__dict__
    {'bar': 2}
    >>> Foo.bar
    1
    >>> g = Foo()
    >>> g.bar = g.bar + 1
    >>> g.__dict__
    {'bar': 2}
* `x += y` not doing the same thing as `x = x + y`
    * what does this call underneath?

Thanks to:
    * Gary Bernhardt
    * Allison Kaptur
    * Puneeth Chaganti
    * Tom Ballinger
    * Hacker School