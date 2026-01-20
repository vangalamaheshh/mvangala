---
layout: post
title:  "Getting Rusty!"
date:   2022-01-24
categories: Blog Programming Languages Rust  
---

![rust-logo](/assets/images/rust-logo.png)

If you were to ask me 2 weeks ago if I have any interest in learning a new programming language, I'd have outright said 'No'. Not that I am any shy to learn new technologies or languages, but because I was too misguided in my own thinking that ```Python``` for data science and ```Java``` for resilient applications have become pretty much the status quo and that any innovation in the compute space has to be with ```compute clusters``` and ```scalable databases```.

It was not until I decided to learn blockchain and smart contracts that I happened to cross paths with ```Rust```. Actually, my first stop was at ```Solidity```, a language on which ```Ethereum``` blockchain is built. However, ```Solidity``` didn't sit well with me. The syntax felt verbose and documentation seemed scarce. Then along came ```Rust``` and I am smitten with this language ever since. 

To describe ```Rust``` in one sentence: C++ efficiency and Java's robustness, only better!! 

<img src="/assets/images/rust-vs.png" alt="rust-versus" width="40%" />
<img src="/assets/images/rust-chart.png" alt="rust-chart" width="40%" />

I have a Github repo dedicated to all things ```Rust``` learning <a href="https://github.com/vangalamaheshh/rust-esh-cean" target="_blank">here</a>. Feel free to git clone and compose up to get ```Rusty```!

Hello world ....
================

{% highlight rust %}
fn main() {
  println!("Hello world!");
}
{% endhighlight %}

Rust Resources
===============
* <a href="https://doc.rust-lang.org/stable/book/" target="_blank">The Book</a>: Rust fundamentals to advanced concepts - all in one!
* <a href="https://doc.rust-lang.org/rust-by-example/index.html" target="_blank">Rust by Example</a>: Rust concepts with demo code.
* <a href="https://doc.rust-lang.org/reference/introduction.html" target="_blank">Rust Doc</a>: Rust Documentation.
* <a href="https://rust-unofficial.github.io/patterns/" target="_blan">Rust Design Patterns</a>
* <a href="https://tokio.rs/tokio/tutorial" target="_blank">Tokio</a>: Async programming with Rust!
* <a href="https://actix.rs/" target="_blank">Actix Web</a>: Web programming with Rust!
* <a href="https://async-graphql.github.io/async-graphql/en/index.html" target="_blank">Async-Graphql</a>: Building GraphQL APIs with Rust!
* <a href="https://docs.rs/datafusion/latest/datafusion/" target="_blank">Datafusion</a>: Data science in Rust!
* <a href="https://github.com/libp2p/rust-libp2p/tree/master/examples" target="_blank">LibP2P</a>: Peer-to-Peer Applications in Rust!

I try to share some of the topics that I found intriguing in ```Rust``` especially coming from ```Java```, ```Perl``` and ```Python``` background. An FYI - I will continually update this post with more and more snippets as I go.

Variables - Immutable, Mutables & Shadowing
============================================

{% highlight rust %}
fn main() {
  let x = 1;
  println!("{}", x); // prints 1
  x = 2; // errors out since x is immutable
  println!("{}", x); 
}
{% endhighlight %}

```
Compilation error:
x = 2; // errors out since x is immutable
^^^^^ cannot assign twice to immutable variable
```

{% highlight rust %}
fn main() {
    let mut x = 1;
    println!("{}", x); // prints 1
    x = 2; // it works since x is now mutable
    println!("{}", x); // prints 2
}
{% endhighlight %}

{% highlight rust %}
fn main() {
    let x = 1;
    println!("{}", x); // prints 1
    {
        let x = 2; // shadowing 
        println!("{}", x); // prints 2
    }
    println!("{}", x); // prints 1
}
{% endhighlight %}


Constants
==========

{% highlight rust %}
fn main() {
    const MINUTES_IN_A_DAY: usize = 60 * 24; // this works
    println!("{}", MINUTES_IN_A_DAY);
    const SECONDS_IN_DAY: usize = get_seconds_in_day(); // this does not
    println!("{}", SECONDS_IN_DAY);
}

pub fn get_seconds_in_day() -> usize {
    return 60 * 60 * 24;
}
{% endhighlight %}

```
Compilation error:
const SECONDS_IN_DAY: usize = get_seconds_in_day();
calls in constants are limited to constant functions, tuple structs and tuple variants
```

<h1><a href="https://doc.rust-lang.org/std/primitive.char.html" target="_blank">Character</a></h1>

A ```char``` is denoted by single quotes while ```String``` by double quotes. A ```char``` in ```Rust``` is 4 bytes in size and can store not just ASCII but unicode values.

<h1><a href="https://doc.rust-lang.org/std/primitive.tuple.html" target="_blank">Tuples</a></h1>

A Tuple can be heterogeneous. A tuple has to be fixed in size. Tuple values are accessed using either dot notation or by unpacking. A tuple can be empty - (). Tuples get stored on stack.

{% highlight rust %}
fn main() {
    let t: (i32, f32, String, char) = (10, 64.2, String::from("Hello"), '!');
    println!("{}, {}, {}, {}", t.0, t.1, t.2, t.3);
}
{% endhighlight %}

<h1><a href="https://doc.rust-lang.org/std/primitive.array.html" target="_blank">Arrays</a></h1>

Arrays are homogeneous and are fixed in size. Arrays get stored on stack. Array elements are accessed using [], for example, array[0].

{% highlight rust %}
fn main() {
    let array: [&str;2] = ["Jan", "Feb"];
    println!("{:?}", array);
    let array = [1,2,3,4,5]; // remember shadowing; otherwise compiler throws error as the variable declared as immutable
    println!("{:?}", array);
    println!("The length of array is: {}", array.len()); // prints 5
}
{% endhighlight %}

<h1><a href="https://doc.rust-lang.org/std/vec/struct.Vec.html" target="_blank">Vectors</a></h1>

Vectors are homogeneous and they get stored on heap, which means they can grow/shrink in size. The elements in vector can be accessed using [] or with get method, for example, v[0] or v.get(0). 

{% highlight rust %}
fn main() {
    let v = vec![1,2,3,4,5]; // implicit way of initializing a vector
    println!("{:?}", v); // prints [1, 2, 3, 4, 5]
    let mut v: Vec<String> = Vec::new();
    v.push(String::from("Hello"));
    v.push(String::from("world"));
    println!("{:?}", v); // prints ["Hello", "world"]
    // Accessing values from Vector
    let hello: &str = &v[0]; // reference to 1st element of the vector
    println!("{}", hello); // prints Hello
    match v.get(0) {
        Some(elem) => println!("{}", elem), // prints Hello
        None => println!("Index out of bounds error."), // never get to this since 0th index is valid
    }
    // Let's try an index that doesn't exist
    match v.get(100) {
        Some(elem) => println!("{}", elem),
        None => println!("Index out of bounds error."), // this gets invoked
    }
}
{% endhighlight %}

```
Output:
[1, 2, 3, 4, 5]
["Hello", "world"]
Hello
Hello
Index out of bounds error.
```

Looping through ```Vector``` using ```iterator```.

{% highlight rust %}
fn main() {
    let mut v: Vec<i32> = vec![1,2,3,4,5];
    // loop through using iterator
    // read-only
    for i in &v {
        print!("{} ", i);
    }
    println!("");
    println!("{:?}", v);
    // mutate
    for i in &mut v {
        *i *= 2;
        print!("{} ", i);
    }
    println!("");
    println!("{:?}", v);
}
{% endhighlight %}


```
Output:
1 2 3 4 5
[1, 2, 3, 4, 5]
2 4 6 8 10
[2, 4, 6, 8, 10]
```

Though ```Vector``` is homogeneous, we can hack using ```enums``` to store heterogeneous data elements.

{% highlight rust %}
use std::fmt;

fn main() {
    enum Person {
        Age(i32),
        Height(f64),
        Name(String),
    }

    impl fmt::Display for Person {
        fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
            match *&self {
                Person::Age(v) => write!(f, "Age: {}", v),
                Person::Height(v) => write!(f, "Height: {}", v),
                Person::Name(v) => write!(f, "Name: {}", v),
            }
        }
    }

    let dexter = vec![
        Person::Name(String::from("Dexter Morgan")),
        Person::Age(38),
        Person::Height(5.11),
    ];

    for info in &dexter {
        println!("{}", info);
    }
}
{% endhighlight %}

```
Output:
Name: Dexter Morgan
Age: 38
Height: 5.11
```

<h1><a href="https://doc.rust-lang.org/stable/std/string/struct.String.html#" target="_blank">Strings</a></h1>

```Rust's``` core ```str``` and ```String``` standard library.

{% highlight rust %}
fn main() {
  // core str 
  let s: str = "Hello there!"; // this errors out with: expected `str`, found `&str`
  let s: &str = "Hello there!"; // this passes - string literal will always be used with `&str`
  println!("{}", s);
  let c = s; // two variables, s and c - both point to same `str` heap
  println!("{}", s);
  println!("{}", c);
  // String library
  let s: String = String::from("Hello there"); // proper ownership as opposed to borrowing/reference/pointer.
  let c = &s; // borrowing
  println!("{}", s);
  println!("{}", c);
  let c = s; // Move of ownership - NOT borrowing
  println!("{}", c);
  println!("{}", s); // this errors out with: ^ value borrowed here after move
}
{% endhighlight %}

Using `+` operator and `format!` macro.

{% highlight rust %}
fn main() {
  let mut s: String = "Hello there".to_string();
  s.push_str(", mate"); // appends a string literal
  s.push('!'); // appends a character
  let s1 = s; // Move of ownership; `s` is no longer valid.
  println!("{}", s1);
  let s2 = "How are you?";
  let s3 = s1 + s2; // s1 is no longer valid; + operator acutally uses `fn add(self, s: &str) -> String;`
  println!("{}", s3);
  let format_string = format!("{}-{}-{}", "Hello there, mate!", s2, s3);
  println!("{}", format_string);
  println!("{}; {}", s2, s3); // s2 and s3 are still valid
}
{% endhighlight %}

```
Output:
Hello there, mate!
Hello there, mate!How are you?
Hello there, mate!-How are you?-Hello there, mate!How are you?
How are you?; Hello there, mate!How are you?
```

```String``` indexing and slices

{% highlight rust %}
fn main() {
  let s = "Hello there".to_string();
  let h = &s[0]; // errors out with: ^^^^ `String` cannot be indexed by `{integer}` 
  let sub_string = &s[0..5];
  println!("{}", sub_string);
  for c in s.chars() {
    print!("{}", c);
  }
  println!("");
}
{% endhighlight %}

```
Output:
Hello
Hello there
```

```String``` indexing doesn't work in ```Rust``` because under the hood it uses ```Vec<u8>```. Since, they support UTF-8, a character can be more than 1 byte. 

<h1><a href="https://doc.rust-lang.org/std/collections/struct.HashMap.html" target="_blank">Hash Maps</a></h1> ```(a.k.a Associative Arrays or Dictionaries)```


Hashes get stored on heap. Hashes are homogeneous in nature.

{% highlight rust %}
use std::collections::HashMap;
  
fn main() {
  let mut h = HashMap::new();
  h.insert("john", "doe");
  h.insert("jane", "doe");
  println!("{:#?}", h);
  // using zip method
  let fnames: Vec<String> = vec![String::from("John"), "Jane".to_string()];
  let lnames: Vec<String> = vec!["Doe".to_string(); fnames.len()];
  let h: HashMap<_, _> = fnames.into_iter().zip(lnames.into_iter()).collect();
  for (k, v) in &h {
    println!("Key: {}; Value: {}", k, v);
  }
  // using get method
  match h.get(&String::from("John")) {
    Some(v) => println!("Value: {}", v),
    None => println!("No such key."),
  }
  // word count example
  let text: String = "Hello hello, how are you, mate?".to_string();
  let mut word_count: HashMap<String, u32> = HashMap::new();
  for s in text.to_lowercase().replace(",", "").split(" ") {
    let count = word_count.entry(s.to_string()).or_insert(0);
    *count += 1;
  }
  println!("{:#?}", word_count);
}

{% endhighlight %}

```
Output:
{
    "jane": "doe",
    "john": "doe",
}
Key: Jane; Value: Doe
Key: John; Value: Doe
Value: Doe
{
    "mate?": 1,
    "are": 1,
    "hello": 2,
    "how": 1,
    "you": 1,
}
```

Enums
======

{% highlight rust %}
fn main() {
  enum Person {
    Name(String),
  }

  // implement a method on Person enum
  impl Person {
    fn greet(&self) -> () {
      match self {
        Person::Name(s) => println!("Hello, {}!", s),
      }
    }
  }

  let person1 = Person::Name("John Doe".to_string());
  person1.greet(); // prints: Hello, John Doe!
}
{% endhighlight %}

{% highlight rust %}
fn main() {
  enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter,
  }

  fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
  }

  println!("The value of Penny is: {} cents", value_in_cents(Coin::Penny));
  println!("The value of Nickel is: {} cents", value_in_cents(Coin::Nickel));
  println!("The value of Dime is: {} cents", value_in_cents(Coin::Dime));
  println!("The value of Quarter is: {} cents", value_in_cents(Coin::Quarter));
}
{% endhighlight %}

Structs
========

```Structs``` and ```Enums``` make up powerful features of ```Rust```. 

{% highlight rust %}
use std::fmt;

fn main() {
  struct Person {
    name: String,
    age: u32,
    city: String
  }
  
  impl fmt::Display for Person {
    fn fmt(self: &Self, f: &mut fmt::Formatter) -> fmt::Result {
      write!(f, "{} lives in {} and is {} years old.", self.name, self.city, self.age)
    }
  }

  impl Person {
    fn is_older(&self, other: &Person) -> bool {
      return self.age > other.age
    }
  }
  
  let p1 = Person {
    name: String::from("Dexter Morgan"),
    age: 38,
    city: String::from("Miami"),
  };
  
  println!("{}", p1);

  let p2 = Person {
    name: String::from("Deborah Morgan"),
    age: 32,
    city: String::from("Miami"),
  };

  println!("{}", p1.is_older(&p2));
} 
{% endhighlight %}

```
Output:
Dexter Morgan lives in Miami and is 38 years old.
true
```

Happy Coding in ```Rust```!! :+1:

<a href="https://www.buymeacoffee.com/MaheshVangala" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/default-orange.png" alt="Buy Me A Coffee" height="41" width="174"></a>

<div id="share-bar">

    <h4 class="share-heading">Liked it? Please share the post.</h4>

    <div class="share-buttons">
        <a class="horizontal-share-buttons" target="_blank" href="https://www.facebook.com/sharer/sharer.php?u=https://vangalamaheshh.github.io{{page.url}}" 
            onclick="gtag('event', 'Facebook', {'event_category':'Post Shared','event_label':'Facebook'}); window.open(this.href, 'pop-up', 'left=20,top=20,width=500,height=500,toolbar=1,resizable=0'); return false;"
            title="Share on Facebook" >
            <span class="icon-facebook2">Facebook</span>
        </a>
        <a  class="horizontal-share-buttons" target="_blank" href="https://twitter.com/intent/tweet?text=Getting Rusty!&url=https://vangalamaheshh.github.io{{page.url}}"
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


