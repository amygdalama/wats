Python WATs: Uncovering Odd Behavior
Amy Hanlon

============

WHO AM I?

Hacker School alum
Software Engineer at Venmo

@amygdalama
mathamy.com

============

THANKS!

* Allison Kaptur (speaking this evening!)
* Tom Ballinger
* Gary Bernhardt (watch his WAT talk!)
* Maybe more?

============

OUTLINE

1. Equality vs Identity
2. Mutability
3. Scope
4. WAT finale

============

`for section in outline:`

a. Trivia/WAT
b. Why?
c. Tips

speaker notes: define WAT

============

1. EQUALITY VS IDENTITY

i.e. `==` vs `is`

============

TRIVIA

>>> "" is ""
True
>>> 0 is 0
True
>>> [] is []
False
>>> {} is {}
False
>>> () is ()
True

============

TRIVIA

>>> a = 256
>>> b = 256
>>> a is b
True

>>> a = 257
>>> b = 257
>>> a is b
False

>>> a = 257; b = 257
>>> a is b
True

============

WHYYY?

{figure of integers from -5 to 256, already loaded when you fire up python
then, step through code, creating an `a` that points to 256, and a `b` that points to the same 256.}

speaker notes: `is` compares whether two objects are stored in the *same location in memory*, rather than if the objects' values are the same. python stores the integers from -5 to 256 in an array, kind of like a cache, and when you assign a variable name to an integer, if the integer is in this special range, the name is pointing to that specific integer in memory.

============

WHYYY?

{figure of integers from -5 to 256, already loaded when you fire up python
then, step through code, creating a object with the value `257` and assigning `a` to it, and create another object with the value `257`, and assign `b` to it, etc.}

speaker notes: when we compare integers outside of the special range of -5 to 256, we create new instances of the integer object and store them in memory separately.

============

WHYYY?

>>> a = 257; b = 257
>>> id(a) == id(b)
True

>>> a = 257
>>> b = 257
>>> id(a) == id(b)
False

speaker notes: `id` is a builtin python function that takes in an object and returns the "identity" of the object. in CPython, this "identity" is the object's location in memory

============

TIPS

* Use `==` instead of `is`
* Exceptions:
    * x is None
    * x is not None
    * when you really, really mean "is the same object in memory"

speaker notes: I don't think I've ever had to see if two objects were the same objects in memory

============

2. MUTABILITY

============

TRIVIA

>>> def wat():
...     faves = ['cats', 'dragons']
...     copy_of_faves = faves
...     copy_of_faves.append('rainbows')
...     print 'copy: ', copy_of_faves
...     print 'original: ', faves
...
>>> wat()
copy: ???
original: ???

============

WHYYY?

{figure of list in memory, two names pointing to list, show change to list when code is executed} (code on left, figure on right)

speaker notes: assignment creates a link between a name and an object. multiple names can be linked to the same object, which is what happens here.

============

WHYYY?

>>> def wat():
...     faves = ['cats', 'dragons']
...     copy_of_faves = faves
...     copy_of_faves.append('rainbows')
...
>>> dis.dis(wat)

  2           0 LOAD_CONST               1 ('cats')
              3 LOAD_CONST               2 ('dragons')
              6 BUILD_LIST               2
              9 STORE_FAST               0 (faves)

  3          12 LOAD_FAST                0 (faves)
             15 STORE_FAST               1 (copy_of_faves)

  4          18 LOAD_FAST                1 (copy_of_faves)
             21 LOAD_ATTR                0 (append)
             24 LOAD_CONST               3 ('rainbows')
             27 CALL_FUNCTION            1
             30 POP_TOP
             31 LOAD_CONST               0 (None)
             34 RETURN_VALUE


(have this next to code, highlight relevant code/relevant bytecode step-by-step and walk through)
(TODO: talk through with Allison)

speaker notes:

============

TIPS

* Use slicing
    >>> copy_of_faves = faves[:]
* Use the `list` function
    >>> copy_of_faves = list(faves)

speaker notes: but what if you have arbitrarily-deep nested lists?

============

TIPS

Shallow Copy

>>> faves = [['cat', ['dragon']]]
>>> copy_of_faves = list(faves)
>>> copy_of_faves[0].append('rainbows')
>>> faves
[['cat', ['dragon'], 'rainbows']]

speaker notes: the `list` function will add each object in the iterable given to it as an argument to a new list. but if those objects are also mutable, that doesn't solve our problem. in other words, `list` creates a *shallow* copy

============

TIPS

Deep Copy

>>> import copy
>>> faves = [['cat', ['dragon']]]
>>> copy_of_faves = copy.deepcopy(faves)
>>> copy_of_faves[0].append('rainbows')
>>> copy_of_faves
[['cat', ['dragon'], 'rainbows']]
>>> faves
[['cat', ['dragon']]]

speaker notes: `deepcopy` recursively copies objects into a new object

============

MORE MUTABILITY:
MUTABLE DEFAULT ARGUMENTS

============

TRIVIA

>>> def append_cat(l=[]):
...     l.append('cat')
...     return l
...
>>> append_cat()
['cat']
>>> append_cat()
['cat', 'cat']

============

WHYYY?

>>> append_cat.func_defaults
(['cat', 'cat'],)
>>> append_cat.func_defaults[0].append('dragon')
>>> append_cat()
['cat', 'cat', 'dragon', 'cat']

speaker notes: functions are objects! they have attributes! one of those attributes is `func_defaults`!
`func_defaults` is a *tuple*, which is immutable, but it can contain elements that are mutable, which is happening here!
we can even mutate `func_defaults` outside of the function!

============

TIPS

Use `None` as default parameter

>>> def append_cat(l=None):
...     if l is None:
...             l = []
...     l.append('cat')
...     return l
...
>>> append_cat()
['cat']
>>> append_cat()
['cat']

speaker notes: if we use `None` instead as a parameter, and we can check to see if the value of `l is None`, and if it is, we can set `l` to a *new* empty list.

============

TIPS

Use mutable default arguments to your advantage!

>>> def sorting_hat(student, cache={}):
...     if student in cache:
...             print "Used cache!"
...             return cache[student]
...     else:
...             house = expensive_sorting_function(student)
...             cache[student] = house
...             return house
...
>>> sorting_hat('Amy Hanlon')
'Ravenclaw'
>>> sorting_hat('Amy Hanlon')
Used cache!
'Ravenclaw'

speaker notes: we can use mutable default arguments to our advantage!
this might not be such a good idea, since other humans reading your code might not immediately understand what's going on, but let's run with it.
python dictionaries are also mutable (i.e. you can add and remove things from them but they still point to the same object in memory)
we can use a dictionary as a cache.
so let's say we want to write a function to replace the sorting hat in Harry Potter. and let's say that the function that sorts new students is very expensive. so we want the sorting hat to remember how it sorted previous students in a cache. then if we give the sorting hat the same student twice, the second time it sorts the student will be really fast!

============

3. SCOPE

============

TRIVIA

============

WHYYY?

============

TIPS

============