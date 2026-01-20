---
layout: post
title:  "Rust Crates!"
date:   2022-03-11
categories: Blog Programming Languages Rust  
---

Similar to <a href="https://www.cpan.org/" target="_blank">```Perl-CPAN```</a>, <a href="https://cran.r-project.org/" target="_blank">```R-CRAN```</a>, <a href="https://pypi.org/" target="_blank">```Python-Pypi```</a>, <a href="https://mvnrepository.com/" target="_blank">```Java-Maven```</a>, ```Rust``` has <a href="https://crates.io/" target="_blank">```crates.io```</a>, where one can publish the code for others to utilize the functionality. In this post, let's explore,

1. Using one such publicly available crate, <a href="https://docs.rs/rand/0.8.5/rand/" target="_blank">```rand```</a>.
2. Rust Modules.
3. Rust Crates.
4. Publishing our own crate, ```movies```.

Let's get started!

![rust-logo](/assets/images/cratesio.jpg)

* Using <b><u>rand</u></b> crate.
===================================
If you want to follow along and bring up ```Rust``` environment locally, please feel free to clone <a href="https://github.com/vangalamaheshh/rust-esh-cean" target="_blank">```my github repo```</a> and ```compose up``` using,

{% highlight bash %}
git clone https://github.com/vangalamaheshh/rust-esh-cean
cd rust-esh-cean
docker network create rust-net
docker-compose up -d
docker exec -it rust-esh-cean bash
{% endhighlight %}

Now you are on ```Rust``` docker container and ready to code along ...

{% highlight rust %}
cargo new movie-crate-demo
cd movie-crate-demo
{% endhighlight %}

Time to add ```rand``` crate to project dependency list.

Add the following to ```Cargo.toml``` file.

{% highlight rust %}
//...
[dependencies]
rand = "0.8.5"
{% endhighlight %}

Running ```cargo run``` will fetch ```rand crate``` as showed in the output below.

```
Updating crates.io index
  Downloaded cfg-if v1.0.0
  Downloaded rand v0.8.5
...
Running `target/debug/movie-crate-demo`
Hello, world!
```

Add the following to ```src/main.rs``` file.

{% highlight rust %}
use rand;

fn main() {
    let mut counter: i8 = 0;
    while counter < 10 {
        counter += 1;
        println!("{}) {:+}", counter, rand::random::<i8>());
    }
}
{% endhighlight %}

```
Output:
1) +18
2) -33
3) -28
...
9) -74
10) -109
```

* Rust <b><u>Modules</u></b>.
===============================

Let's create a module ```movies``` and add a function ```play``` that prints a movie name that we pass to this function.

Create a file ```src/movie.rs``` with following content.

{% highlight rust %}
pub mod movies {
    pub fn play(name: String) -> String {
        format!("Playing movie: {}", name)
    }
}
{% endhighlight %}

It's time to include this module in our ```Cargo.toml```.

```
[lib]
name = "movies"
path = "src/movies.rs"
```

Now, let's test our ```movies module``` by adding following code to our ```src/main.rs``` file.

{% highlight rust %}
use movies as movie_module;

fn main() {
    let name = String::from("Good Will Hunting");
    println!("{}", movie_module::movies::play(name));
}
{% endhighlight %}

```
Output:
Playing movie: Good Will Hunting
```

* Rust <b><u>Crates</u></b>.
===========================

From within the folder, ```movie-crate-demo```, run the following to create ```movies crate``` and move exising ```movies module``` into ```movies crate```.

```
cargo new --lib movies
mv src/movies.rs movies/src/
```

Remove the following content from ```movie-crate-demo/Cargo.toml``` and in turn add this content to ```movie-crate-demo/movies/Cargo.toml```.

```
[lib]
name = "movies"
path = "src/movies.rs"
```

Add the following to ```movie-crate-demo/Cargo.toml``` file.

```
[dependencies]
movies = { path = "movies" }
```

Edit ```movie-crate-demo/src/main.rs``` file with the following,

{% highlight rust %}
extern crate movies as movies_crate;
use movies_crate::movies;

fn main() {
    let name = String::from("Good Will Hunting");
    println!("{}", movies::play(name));
}
{% endhighlight %}

```
Output:
Playing movie: Good Will Hunting
```

* Publishing <b><u>Movies Crate</u></b> to crates.io
=====================================================

In order to publish crates, you need to create an account on <a href="https://crates.io" target="_blank">crates.io</a>. Run the following to publish our ```movies crate``` to crates.io.

```
cd movies
cargo login <api-token>
cargo package --allow-dirty
cargo publish --allow-dirty
```

That's it. You now have your ```movies crate``` published onto crates.io. Here's how it looks after I published my ```movies crate```.

![movie-crate](/assets/images/movie-crate.png)

* Using our published <b><u>Movies Crate</u></b> in our project.
================================================================
Edit the ```movie-crate-demo/Cargo.toml``` as follows,

{% highlight rust %}
[dependencies]
movies = "0.1.0"
{% endhighlight %}

Running ```cargo run``` will download ```movies crate``` from ```crates.io``` and will output,

```
Playing movie: Good Will Hunting
```

To recap, we explored,

1. Using ```rand``` crate.
2. Created ```movies``` module.
3. Created ```movies``` crate.
4. Published ```movies crate``` to ```crates.io```.
5. Used ```movies crate``` in our project.

Happy Publishing ```Rust Crates```!! :+1:

<a href="https://www.buymeacoffee.com/MaheshVangala" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/default-orange.png" alt="Buy Me A Coffee" height="41" width="174"></a>

<div id="share-bar">

    <h4 class="share-heading">Liked it? Please share the post.</h4>

    <div class="share-buttons">
        <a class="horizontal-share-buttons" target="_blank" href="https://www.facebook.com/sharer/sharer.php?u=https://vangalamaheshh.github.io{{page.url}}" 
            onclick="gtag('event', 'Facebook', {'event_category':'Post Shared','event_label':'Facebook'}); window.open(this.href, 'pop-up', 'left=20,top=20,width=500,height=500,toolbar=1,resizable=0'); return false;"
            title="Share on Facebook" >
            <span class="icon-facebook2">Facebook</span>
        </a>
        <a  class="horizontal-share-buttons" target="_blank" href="https://twitter.com/intent/tweet?text=Rust Crates!&url=https://vangalamaheshh.github.io{{page.url}}"
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


