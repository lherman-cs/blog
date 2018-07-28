+++
categories = []
date = "2018-07-02T04:22:57-04:00"
image = "https://images.unsplash.com/photo-1513042966058-0ac1891fdc2d?ixlib=rb-0.3.5&q=80&fm=jpg&crop=entropy&cs=tinysrgb&w=1080&fit=max&ixid=eyJhcHBfaWQiOjExNzczfQ&s=811dd33449166725062598c12718894c"
tags = []
title = "Python Decorator"
weight = 5
writer = "Lukas Herman"

+++
Decorator is a very useful and powerful syntax that Python developers came up with in Python 2.4 through [PEP 318 proposal](https://www.python.org/dev/peps/pep-0318/). Since then, many powerful libraries started using this extensively. These libraries including Flask, Django, AioHTTP, Sanic, and so on... You got the point. Python decorator is powerful!

*Flask (quoted directly from the official page)*

```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World!"
```

*Note: ```app.route("/")``` is a Python decorator*


You might not know what I'm talking about. That's okay! At some point, everybody was a beginner too. In this article, I'll walk you through slowly from the very basic to some advanced topics.


## Intro
Decorator is not a term that Python developers came up with. It is actually one out of design patterns that software engineers use. By definition, 

> Decorator pattern is a design pattern that allows behavior to be added to an individual object, either statically or dynamically, without affecting the behavior of other objects from the same class.

In simple term, **decorator dynamically alters a function, method, or class without modifying the original code.**

Though, decorator pattern is not necessarily the same with Python decorator. Python's official page says this clearly [here](https://wiki.python.org/moin/PythonDecorators). But, the idea is still similar. 

# Coding
## Original Code

```python
def add(x, y):
    '''This is a very complicated function :)'''
    return x + y

print(add(3, 5))
```

To make this tutorial fun, I have some tasks that you can do to help you understand the topic:

* Add printing "hello world" functionality to ```add``` function **without the decorator syntax**
* Add printing "hello world" functionality to ```add``` function **with the decorator syntax**
* Add a timer functionality to ```add``` function **with the decorator syntax**

Following are the rules for every task above:

* You have to create a new function that accepts the same parameters and return the same thing
* You can't modify the original code, ```add```.

## Task #1: Add printing "hello world" functionality to ```add``` function without the decorator syntax

```python
def hello(fn):
    def wrapper(x, y):
        print("hello world")
        return fn(x, y)
    return wrapper

def add(x, y):
    '''This is a very complicated function :)'''
    return x + y

decorated = hello(add)
print(decorated(3, 5))
```

Here, we see that ```hello``` accepts a function and store it in ```fn```. Then, we create a new function in ```hello``` called ```wrapper```. ```wrapper``` is a function that we're **decorating**. We added ```print("hello world")``` right before we call ```fn``` with ```x``` and ```y``` parameters (```fn``` in this case is the ```add``` function). After we run this, we would see that it'd print "hello world" first and then 8 (from ```add(3, 5)```). And that's how we accomplished **task #1**!!

## Task #2: Add printing "hello world" functionality to ```add``` function with the decorator syntax

```python
def hello(fn):
    def wrapper(x, y):
        print("hello world")
        return fn(x, y)
    return wrapper

@hello
def add(x, y):
    '''This is a very complicated function :)'''
    return x + y

print(add(3, 5))
```

*Note: there's ```@hello``` above the ```add``` function*

As you can see, we didn't change the code much. But, yet, it's much cleaner now. We can even call the original function normally. That's pretty cool, right!? So, what's going on here?

After [PEP381](https://www.python.org/dev/peps/pep-0318/) proposal was implemented, this ```@``` became a syntax sugar for the code that you saw in **task #1**. Instead of doing ```decorated = hello(add)```, this syntax **decorates** your original function and replace the original function with the decorated function. That way, we can simply call the original function normally but with **extra functionalities**.

## Task #3: Add a timer functionality to ```add``` function with the decorator syntax

```python
from time import time

def timeit(fn):
    def wrapper(x, y):
        start = time()
        result = fn(x, y)
        elapsed = time() - start
        print(f"Elapsed: {elapsed:.4e}s")

        return result
    return wrapper

@timeit
def add(x, y):
    '''This is a very complicated function :)'''
    return x + y

print(add(3, 5))
```

Here, we use Python's ```time``` function from time module. The function is very simple. It basically returns the current timestamp. This is very useful because later on, you can simply subtract the current timestamp with the previous timestamp that you stored before you call a function to get the elapsed time.

In this code, we see a similar pattern, it's just slightly different from the previous version. Here, instead of returning the result right after it's being called, we store the result from the original function in a variable fist and return it later. This has to be done because we want to **calculate the elapsed time after the add ```function``` gets called**. 

If we run this code, we would see something like below:
```sh
Elapsed: 7.1526e-07s
8
```

# Appendix
Congratulations! We have just learned a basic understanding of Python decorator. From now on, I'll be talking about some advanced topics. So, if you think that you're still shaky, please review the material multiple times. Learning about this topic is not easy! I've been in your position. It was tough for me at first.

## Advance #1: Generalize our decorator function
If you paid attention to our previous codes, our decorator functions were not flexible enough. They only work if we have the same parameters and return value with our ```add``` function. What if we have another function that doesn't have any parameter? Our code won't be able to run! 

Luckily, though, Python is very flexible and dynamic. Python has these 2 powerful syntaxes in defining parameters: *args and **kwargs. ```args``` and ```kwargs``` are just being used because of the naming convention. One asterisk means it accepts an arbitrary amount of parameters. Two asterisks mean it accepts an arbitrary amount of keyword parameters.

Let's see how these 2 syntaxes generalize our decorator function.

Previous:

```python
def timeit(fn):
    def wrapper(x, y):
        start = time()
        result = fn(x, y)
        elapsed = time() - start
        print(f"Elapsed: {elapsed:.4e}s")

        return result
    return wrapper
```

After:

```python
def timeit(fn):
    def wrapper(*args, **kwargs):
        start = time()
        result = fn(*args, **kwargs)
        elapsed = time() - start
        print(f"Elapsed: {elapsed:.4e}s")

        return result
    return wrapper
```

Yes! we only changed ```x``` and ```y``` to ```*args``` and ```**kwargs```. Yet, now we can use our ```timeit``` decorator function for **any function**.

## Advance #2: Using decorator to speed up your function
Following we have a recursive implementation of the fibonacci function:

```python
def fib(n):
    if n < 2:
        return n
    return fib(n - 2) + fib(n - 1)

print(fib(40))
```

On my laptop, this code took **37.62s** to finish. This makes sense because, in the recursion, we keep calling the same function with the same parameter **a lot!** To be a little bit scientific, this implementation is upper bounded by **O(2^n)**.

One of many ways to improve this implementation is to use a global array to store the return value for each call. That way, instead of recursing in, it can just look up the array and see if the value is already in the global array, else recursing in. This technique is called **memoization**. So, instead of having O(2^n) running time complexity, we would have a **O(n)** algorithm. This is great and definitely helpful to increase the performance. But, it's dirty!

Luckily, Python has this awesome decorator function called ```lru_cache```. This function is under the functools module. You can read more details [here](https://docs.python.org/3/library/functools.html#functools.lru_cache).

```lru_cache``` basically does the memoization technique, but with a decorator function. Very neat! Here's how to use it:

```python
from functools import lru_cache

@lru_cache(maxsize=None)
def fib(n):
    if n < 2:
        return n
    return fib(n - 2) + fib(n - 1)

print(fib(40))
```

That's it! Just 2 extra lines of codes! (1 line is even for importing the module, which shouldn't be counted). How's the performance? From **37.62s**, this code only took **0.02s**. That's **1881x** performance!!

# Conclusion
Python decorator is a very powerful feature; it has a lot of use cases, ranging from performance to HTTP routing. It can add a lot of functionalities and performance to your functions without sacrificing the code readability!

As always, you can check out the source code [here](https://github.com/lherman-cs/blog-python-decorator).