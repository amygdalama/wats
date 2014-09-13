# Title
Investigating Python WATs: Identity, Mutability, and Scope

# Category
Python Core

# Duration
30 minutes

# Description
> If your talk is accepted this will be made public and printed in the program. Should be one paragraph, maximum 400 characters.

Most of us have experienced unexpected behavior of Identity, Mutability, or Scope in the CPython interpreter, but we might not have a rigorous understanding of why these unexpected behaviors occur. For each of these three topics, we'll look at some common surprising behaviors, investigate the cause of the behaviors, and cover some practical tips on how to avoid related bugs.

# Audience
> Who is the intended audience for your talk? (Be specific; "Python programmers" is not a good answer to this question.)
Programmers curious about Identity, Mutability, or Scope

# Python Level
Novice

# Objectives
> What will attendees get out of your talk? When they leave the room, what will they know that they didn't know before?
Participants will leave the talk with a rigorous understanding of what causes common Python WATs, tools for avoiding bugs caused by these surprising behaviors, and some fun Python trivia.

# Detailed Abstract
> Detailed description. Will be made public if your talk is accepted.

## Identity
First, we'll look at some common CPython gotchas involving object identity. We'll try to guess the value of various `is` statements, and then see what the actual value is. Some examples:

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

Then, we'll discuss why some of these statements evaluate to `True` and others `False`, and how identity differs from equality. We'll discuss implementation details of CPython, like storing an array of integers between -5 and 256, and how when you create instances of the integer type with values in that range, you're just creating a reference to a value in that array.

Finally, we'll cover some quick practical tips about how to avoid bugs resulting from using the `is` statement, based on what we've learned.

## Mutability
Again, we'll look at some common Python gotchas, this time caused by mutability. Some examples:

    >>>



# Outline

# Additional Notes

# Additional Requirements