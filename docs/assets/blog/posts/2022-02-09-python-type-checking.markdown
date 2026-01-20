---
layout: post
title:  "Python Type Checking and Doc Tests"
date:   2022-02-09
categories: Blog Programming Languages Python  
---

It's not a hyperbole to say that ```Python``` has taken over the pinnacle of Mount Coderest, especially so in data science world if not in others. Let's also be honest, as much as we all love ```Python```, because of it's syntactical sugar and the dynamic nature of the language helping in quickly vrooming off from idea to prototype to production and then to analytics and beyond, there are certain pain points (in my opinion) associated with it's maintenance as the codebase gets larger and larger. Libraries like ```Pandas``` and ```numpy``` use ```dtype``` to circumvent these shortcomings, but the core python itself continues to be sans type-checks.

Because of this, most often than not, a project in ```Python``` which starts out as a quick script to test the waters and before you know it, you are debugging a production system with codebase the size of a football field. Those are the long days (and nights) when the dynamic nature of ```Python```, the very feature that earned ```Python``` it's accolades, becomes a nightmarish scenario until you fix those pesky bugs. By then, if you are anything like me, end up having ```print``` and ```stderr``` statements all over, strewn like landmines of a battlefield.

It's almost with shame I admit, I can't believe why I did not pay attention to ```Python Types and Tests``` and embrace those as part of daily coding life, until now. As I had this light bulb going off moment for myself, I wanted to share a thing or two of my learnings into ```Python Type Checking & Doc Tests```. So, here we go ....

![type-checks](/assets/images/type-checks.png)

# Type-Checking

Let's explore a ```Stack``` (abstract) data type and include type-checks. Later we will add doc-tests to it and run the tests.

{% highlight python %}
#!/usr/bin/env python

#-----------------------------
# @author: Mahesh Vangala
# @license: <MIT + Apache 2.0>
#-----------------------------

"""STACK (Abstract Data Types)

  Goal is to implement 'strict typing' and 'doctests'

  Functionality: (methods) push, pop
"""

from __future__ import annotations
from typing import List, TypeVar, Generic

T = TypeVar("T")

class EmptyStackException(BaseException):
  pass

class Stack(Generic[T]):
  def __init__(self) -> None:
    self.items: list[T] = []
    
  def push(self, item: T) -> T:
    self.items.append(item)
    return item

  def pop(self) -> T:
    if self.is_empty():
      raise EmptyStackException(str(self))
    return self.items.pop()

  def __len__(self) -> int:
    return len(self.items)

  def __bool__(self) -> bool:
    return not self.is_empty()

  def is_empty(self) -> bool:
    return len(self.items) == 0

  def __str__(self) -> str:
    info: str = ""
    if self.is_empty():
      info = "Stack is empty."
    else:
      for idx, val in enumerate(self.items[::-1]):
        info += "#{index}: {val}\n".format(index = idx + 1, val = val)
    return info.strip()

if __name__ == "__main__":
  s: Stack = Stack()
  print(s)
  s.push("Howdy")
  s.push("Hello")
  s.push("Mornin'")
  print(s)
  print(s.pop())
  print(s)
  try:
    while True:
      print(s.pop())
  except BaseException as e:
    print(e)

{% endhighlight %}

Use <a href="https://mypy.readthedocs.io/en/stable/" target="_blank">```mypy```</a> to check for any type mismatches before running the script.

{% highlight bash %}
pip install mypy
mypy stack.py
# Try mixing types (ex. int and str) and run again to see mypy in action.
{% endhighlight %}

Now, running the script ```python stack.py``` will output,

```
Stack is empty.
#1: Mornin'
#2: Hello
#3: Howdy
Mornin'
#1: Hello
#2: Howdy
Hello
Howdy
Stack is empty.
```

![doc-tests](/assets/images/doc-tests.png)

# Doc Tests

Let's add doc tests ...

{% highlight python %}
#!/usr/bin/env python

#-----------------------------
# @author: Mahesh Vangala
# @license: <MIT + Apache 2.0>
#-----------------------------

"""STACK (Abstract Data Types)

  Goal is to implement 'strict typing' and 'doctests'

  Functionality: (methods) push, pop
"""

from __future__ import annotations
from typing import List, TypeVar, Generic

T = TypeVar("T")

class EmptyStackException(BaseException):
  pass

class Stack(Generic[T]):
  def __init__(self) -> None:
    """
    >>> s = Stack()
    >>> print(s)
    Stack is empty.
    """
    self.items: list[T] = []
    
  def push(self, item: T) -> T:
    """
    >>> s = Stack()
    >>> s.push(10)
    10
    >>> s.push(20)
    20
    >>> print(s)
    #1: 20
    #2: 10
    """
    self.items.append(item)
    return item

  def pop(self) -> T:
    """
    >>> s = Stack()
    >>> s.push("Dessert")
    'Dessert'
    >>> s.pop()
    'Dessert'
    >>> s.pop()
    Traceback (most recent call last):
      ...
    stack.EmptyStackException: Stack is empty.
    """
    if self.is_empty():
      raise EmptyStackException(str(self))
    return self.items.pop()

  def __len__(self) -> int:
    """
    >>> s = Stack()
    >>> s.push(1)
    1
    >>> s.push(2)
    2
    >>> len(s) == 2
    True
    >>> s.pop()
    2
    >>> len(s) == 2
    False
    """
    return len(self.items)

  def __bool__(self) -> bool:
    """
    >>> s = Stack()
    >>> bool(s)
    False
    >>> s.push("stuff")
    'stuff'
    >>> bool(s)
    True
    """
    return not self.is_empty()

  def is_empty(self) -> bool:
    """
    >>> s = Stack()
    >>> s.is_empty()
    True
    >>> s.push("stuff")
    'stuff'
    >>> s.is_empty()
    False
    """
    return len(self.items) == 0

  def __str__(self) -> str:
    """
    >>> s = Stack()
    >>> print(s)
    Stack is empty.
    >>> s.push("stuff")
    'stuff'
    >>> print(s)
    #1: stuff
    """
    info: str = ""
    if self.is_empty():
      info = "Stack is empty."
    else:
      for idx, val in enumerate(self.items[::-1]):
        info += "#{index}: {val}\n".format(index = idx + 1, val = val)
    return info.strip()

if __name__ == "__main__":
  s: Stack = Stack()
  print(s)
  s.push("Howdy")
  s.push("Hello")
  s.push("Mornin'")
  print(s)
  print(s.pop())
  print(s)
  try:
    while True:
      print(s.pop())
  except BaseException as e:
    print(e)

{% endhighlight %}

Running doc-tests using ```python -m doctest -v stack.py``` will run the tests.

```
#...
7 items passed all tests:
   4 tests in stack.Stack.__bool__
   2 tests in stack.Stack.__init__
   6 tests in stack.Stack.__len__
   4 tests in stack.Stack.__str__
   4 tests in stack.Stack.is_empty
   4 tests in stack.Stack.pop
   4 tests in stack.Stack.push
28 tests in 10 items.
28 passed and 0 failed.
Test passed.
```

<br/>
Happy ```Python``` Coding! :+1:

<a href="https://www.buymeacoffee.com/MaheshVangala" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/default-orange.png" alt="Buy Me A Coffee" height="41" width="174"></a>

<div id="share-bar">

    <h4 class="share-heading">Liked it? Please share the post.</h4>

    <div class="share-buttons">
        <a class="horizontal-share-buttons" target="_blank" href="https://www.facebook.com/sharer/sharer.php?u=https://vangalamaheshh.github.io{{page.url}}" 
            onclick="gtag('event', 'Facebook', {'event_category':'Post Shared','event_label':'Facebook'}); window.open(this.href, 'pop-up', 'left=20,top=20,width=500,height=500,toolbar=1,resizable=0'); return false;"
            title="Share on Facebook" >
            <span class="icon-facebook2">Facebook</span>
        </a>
        <a  class="horizontal-share-buttons" target="_blank" href="https://twitter.com/intent/tweet?text=Python Type Checking and Doc Tests&url=https://vangalamaheshh.github.io{{page.url}}"
            onclick="gtag('event', 'Twitter', {'event_category':'Post Shared','event_label':'Twitter'}); window.open(this.href, 'pop-up', 'left=20,top=20,width=500,height=500,toolbar=1,resizable=0'); return false;"
            title="Share on Twitter" >
            <span class="icon-twitter">Twitter</span>
        </a>
        <a  class="horizontal-share-buttons" target="_blank" href="http://www.reddit.com/submit?url=https://vangalamaheshh.github.io{{page.url}}"
          onclick="gtag('event', 'Reddit', {'event_category':'Post Shared','event_label':'Reddit'}); window.open(this.href, 'pop-up', 'left=20,top=20,width=900,height=500,toolbar=1,resizable=0'); return false;"
          title="Share on Reddit" >
          <span class="icon-reddit">Reddit</span>
        </a>
        <a  class="horizontal-share-buttons" target="_blank" href="https://www.linkedin.com/shareArticle?mini=true&url=https://vangalamaheshh.github.io{{page.url}}"
          onclick="gtag('event', 'LinkedIn', {'event_category':'Post Shared','event_label':'LinkedIn'}); window.open(this.href, 'pop-up', 'left=20,top=20,width=500,height=500,toolbar=1,resizable=0'); return false;"
          title="Share on LinkedIn" >
          <span class="icon-linkedin">LinkedIn</span>
        </a>
    </div>

</div>
<style type="text/css">
/* Share Bar */
#share-bar {
    font-size: 20px;
    border: 3px solid #7de77b;
    border-radius: 0.3em;
    padding: 0.3em;
    background: rgba(125,231,123,.21)
}

.share-heading {
    margin-top: 0px;
}

/* Title */
#share-bar h4 {
    margin-bottom: 10px;
    font-weight: 500;
}

/* All buttons */
.share-buttons {
}

.horizontal-share-buttons {
    border: 1px solid #928b8b;
    border-radius: 0.2em;
    padding: 0.2em;
    margin-right: 0.2em;
    line-height: 2em;
}

/* Each button */
.share-button {
    margin: 0px;
    margin-bottom: 10px;
    margin-right: 3px;
    border: 1px solid #D3D6D2;
    padding: 5px 10px 5px 10px;
}
.share-button:hover {
    opacity: 1;
    color: #ffffff;
}

/* Facebook button */
.icon-facebook2 {
    color: #3b5998;
}

.icon-facebook2:hover {
    background-color: #3b5998;
    color: white;
}

/* Twitter button */
.icon-twitter {
    color: #55acee;
}
.icon-twitter:hover {
    background-color: #55acee;
    color: white;
}

/* Reddit button */
.icon-reddit {
    color: #ff4500;
}
.icon-reddit:hover {
    background-color: #ff4500;
    color: white;
}

/* Hackernews button */
.icon-hackernews {
    color: #ff4500;
}

.icon-hackernews:hover {
    background-color: #ff4500;
    color: white;
}

/* LinkedIn button */
.icon-linkedin {
    color: #007bb5;
}
.icon-linkedin:hover {
    background-color: #007bb5;
    color: white;
}

</style>


