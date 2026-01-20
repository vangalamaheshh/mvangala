---
layout: post
title:  "Apache Spark Cluster - Installation!"
date:   2022-01-03
categories: Blog Data-Engineering Big-Data Apache-Spark  
---

<script src="/assets/scripts/copy-header.js"></script>
<link rel="stylesheet" href="/assets/css/copy-header.scss" />

Following are covered in this post.
- Install `Apache Spark (v3.2.0)` in pseudo-distributed fashion on local workstation.
- Run `Jupyter Notebook` integrated with `PySpark`.
- Run `pi.py` - out of box example - via,
    - `Spark Submit` on command line
    - `Notebook` using `PySpark`

It is assumed that you have `Docker` and `Compose` installed.

Installation:
=============

docker-compose.yaml

{% include code-header.html %}
{% highlight docker %}
version: "3.3"
services:
  spark-master:
    container_name: spark-master
    image: mvangala/apache-spark-cluster:3.2.0
    ports:
      - "9090:8080"
      - "7077:7077"
      - "8889:8889"
    environment:
      - SPARK_LOCAL_IP=spark-master
      - SPARK_WORKLOAD=master

  spark-worker-a:
    container_name: spark-worker-a
    image: mvangala/apache-spark-cluster:3.2.0
    ports:
      - "9091:8080"
      - "7000:7000"
    depends_on:
      - spark-master
    environment:
      - SPARK_MASTER=spark://spark-master:7077
      - SPARK_WORKER_CORES=1
      - SPARK_WORKER_MEMORY=1G
      - SPARK_DRIVER_MEMORY=1G
      - SPARK_EXECUTOR_MEMORY=1G
      - SPARK_WORKLOAD=worker
      - SPARK_LOCAL_IP=spark-worker-a
  
  spark-worker-b:
    container_name: spark-worker-b
    image: mvangala/apache-spark-cluster:3.2.0
    ports:
      - "9092:8080"
      - "7001:7000"
    depends_on:
      - spark-master
    environment:
      - SPARK_MASTER=spark://spark-master:7077
      - SPARK_WORKER_CORES=1
      - SPARK_WORKER_MEMORY=1G
      - SPARK_DRIVER_MEMORY=1G
      - SPARK_EXECUTOR_MEMORY=1G
      - SPARK_WORKLOAD=worker
      - SPARK_LOCAL_IP=spark-worker-b
{% endhighlight %}

Once you have above content saved into `docker-compose.yaml`, run

{% highlight docker %}
docker-compose -f docker-compose.yaml up -d
{% endhighlight %}

This will fire up a master node and 2 worker nodes. You can view the Spark UI at `http://localhost:9090` in your browser. It will look similar to the picture below.

![Spark UI](/assets/images/spark_ui.png)

Run a job using Spark Submit:
=============================

Now time to run `pi.py` using `spark-submit`. Run the following commands to see `map-reduce` in action.

{% highlight docker %}
docker exec -it spark-master bash
# this command will launch you into spark-master container
spark-submit examples/src/main/python/pi.py 1000
# run pi.py script with 1000 partitions as an argument
{% endhighlight %}

Run a job via **`Jupyter Notebook`** using **`PySpark`**:
=========================================================

Run the following command from within `spark-master` docker container,

{% highlight docker %}
pyspark
# this launches jupyter-notebook on port 8889 
{% endhighlight %}

Follow the markdown below to run the commands in `jupyter-notebook`.

{% include /mark-down/spark-submit.md %}

![PySpark Notebook](/assets/images/spark-submit.png)

To recap, we successfully,
- installed Apache-Spark cluster (v3.2.0)
- spark-submit a job
- launched jupyter-notebook using pyspark
- executed spark friendly commands in jupyter-notebook.

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
        <a  class="horizontal-share-buttons" target="_blank" href="https://twitter.com/intent/tweet?text=Apache-Spark Installation&url=https://vangalamaheshh.github.io{{page.url}}"
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


