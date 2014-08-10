1. Equality
    * Trivia! `is` vs `==` for different data types
    * Whyyyy
        * visual memory/namespace
        * bytecode
    * When to use `is`
2. Mutability
    a. Intro
        * Trivia! appending to a not-really-a-copy of a list
        * Whyyyy
            * visual memory/namespace
            * bytecode
        * How to make an actual copy
    b. Mutable default arguments
        * GAME! appending to a mutable default argument
        * Whyyyy
            * `func_defaults`
        * Related fun thing: `func_code`
        * Use `None` instead
        * Storing a cache
3. Scope
    * Trivia! local vs global variables
    * Whyyyy
        * what happens when the function is defined?
        * bytecode
    * Solution using `global` statement, solution using parameters
4. Booleans, comparing unlike objects, oh my!
    * Booleans!
        * they're subclassed from integers!
            * you can add them!
            * `True is 1`!
            * this gets confusing when using them as keys in a dictionary!
        * in 2.x they aren't keywords! which means you can assign new values to them!
    * Comparisons!
        * some examples!
        * and for my last trick, everything you learned in math class was wrong! there are things bigger than `inf`!