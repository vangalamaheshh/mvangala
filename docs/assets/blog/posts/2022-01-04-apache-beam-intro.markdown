---
layout: post
title:  "Apache Beam - Intro!"
date:   2022-01-04
categories: Blog Data-Engineering Big-Data Apache-Beam  
---

<p>What's better than <b>Apache Spark</b>?</p>
<p>Writing <b>Beam pipelines</b> on <b>Spark cluster</b>.</p>
Let's dive into the `Beam world` to experience first hand how fluid the design patterns are and how beautiful a job `Beam community` has done to abstract away the complexities to give the data-engineers a Unicorn, ahem, Unified Programming language.

A `Unified Programming language`! That's right - code once, run anywhere, everywhere!! If you have ventured down the path of writing parallel pipelines geared towards specific environment, only to be told to port it to a different environment when it came to production, those painful memories (I have had my share of those working in Genomics field not that long ago) - they are every data engineer's nemesis.

Finally, `Beam` addresses this problem by separating the execution logic (runners) from code (pipelines). As of writing, `Beam` supports,

- Direct Runner (for local development)
- <a href="https://spark.apache.org/" target="_blank">Apache Spark</a>
- <a href="https://flink.apache.org/" target="_blank">Apache Flink</a>
- <a href="https://cloud.google.com/blog/topics/developers-practitioners/dataflow-backbone-data-analytics" target="_blank">Google Dataflow</a>
- <a href="https://samza.apache.org/" target="_blank">Apache Samza</a>
- <a href="https://nemo.apache.org/" target="_blank">Apache Nemo</a>
- <a href="https://jet-start.sh/" target="_blank">Apache Jet</a>

In addition, `Beam` supports `Java`, `Python` and `Golang` at the moment and more on the way. 

In this post, I share my learnings into a simple pipeline designed with `Beam`. We will also explore `Interactive Beam Visual Display` using `Jupyter-Notebook`.

It is assumed that you have `Spark Cluster` installed and `Jupyter-Notebook` running. For more information, <a href="https://vangalamaheshh.github.io/blog/data-engineering/big-data/apache-spark/2022/01/03/apache-spark-installation.html" target="_blank">see my blog post here</a>.

A Simple Pipeline - Sum of Numbers:
===================================

{% highlight python %}
import apache_beam as beam

with beam.Pipeline() as pipeline:
    result = (
        pipeline
        | 'Create numbers' >> beam.Create([1, 2, 3, 4])
        | 'Multiply by two' >> beam.Map(lambda x: x * 2)
        | 'Sum everything' >> beam.CombineGlobally(sum)
        | 'Print results' >> beam.Map(print)
    )

{% endhighlight %}

![notebook1](/assets/images/beam-intro-1.png)

Interactive Visual Display of Beam pipeline:
============================================

{% include /mark-down/visual-display-beam.md %}

Happy Coding!! :+1:

<a href="https://www.buymeacoffee.com/MaheshVangala" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/default-orange.png" alt="Buy Me A Coffee" height="41" width="174"></a>

<div id="share-bar">

    <h4 class="share-heading">Liked it? Please share the post.</h4>

    <div class="share-buttons">
        <a class="horizontal-share-buttons" target="_blank" href="https://www.facebook.com/sharer/sharer.php?u=https://vangalamaheshh.github.io{{page.url}}" 
            onclick="gtag('event', 'Facebook', {'event_category':'Post Shared','event_label':'Facebook'}); window.open(this.href, 'pop-up', 'left=20,top=20,width=500,height=500,toolbar=1,resizable=0'); return false;"
            title="Share on Facebook" >
            <span class="icon-facebook2">Facebook</span>
        </a>
        <a  class="horizontal-share-buttons" target="_blank" href="https://twitter.com/intent/tweet?text=Apache Beam - Intro!&url=https://vangalamaheshh.github.io{{page.url}}"
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


