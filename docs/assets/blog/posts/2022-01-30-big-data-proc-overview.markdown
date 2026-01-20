---
layout: post
title:  "Big-Data-Processing (FullStack & beyond) Overview"
date:   2022-01-30
categories: Blog Data-Engineering Big-Data Full-Stack  
---

Most of the big-data related posts if not all of them in the free flowing web only demonstrate a part of the overall process. For me, big-data processing begins where the source data is being originated from. And that place starts with a web-browser. In order to be a fully-informed data-engineer, I firmly believe, requires not just command line + programming + SQL skills but also web development, APIs, data viz. skills and beyond.

In that spirit, I wanted to contribute my share in bringing forth all stages of development that are required in making an idea take shape, almost life-like.

This is an overview of multi-part posts in this effort. We cover the following in the upcoming blog posts,

1. <b>Develop a web-front-end (a.k.a. client) application to capture data.</b>
<br/>
- <u>Learning outcomes</u>: 
  - <a href="https://angular.io/" target="_blank">```Angular```</a>
  - <a href="https://material.angular.io/" target="_blank">```Material```</a>
  - <a href="https://rxjs.dev/" target="_blank">```RxJs```</a>
  - <a href="https://www.apollographql.com/" target="_blank">```Apollo-GraphQL```</a>
  - <a href="https://rxjs.dev/api/webSocket/webSocket" target="_blank">```Web-Sockets```</a>
  - <a href="https://developers.google.com/protocol-buffers" target="_blank">```Protocol-Buffers```</a>
2. <b>Develop a web-back-end (a.k.a. server) application to capture business logic. </b>
<br/>
- <u>Learning outcomes</u>: 
  - <a href="https://flask.palletsprojects.com/en/2.0.x/" target="_blank">```Python Flask```</a>
  - <a href="https://nodejs.org/en/" target="_blank">```NodeJs```</a>
  - <a href="https://microservices.io/patterns/microservices.html" target="_blank">```Microservice Architecture```</a>
3. <b>(Near) Real-Time Processing Queues.</b>
<br/>
- <u>Learning outcomes<u>: 
  - <a href="https://kafka.apache.org/" target="_blank">```Apache Kafka```</a>
4. <b>(Embarrassingly) Parallel Data Processing.</b>
<br/>
- <u>Learning outcomes</u>: 
  - <a href="https://beam.apache.org/" target="_blank">```Apache Beam```</a>
5. <b>Scalable, Columnar, NoSQL - Databases.</b>
<br/>
- <u>Learning outcomes</u>:
  - <a href="https://cassandra.apache.org/_/index.html" target="_blank">```Apache Cassandra```</a>
6. <b>Graph Databases.</b>
<br/>
- <u>Learning outcomes</u>:
  - <a href="https://neo4j.com/" target="_blank">```Neo4J```</a>
7. <b>Data analysis & Visualization.</b>
<br/>
- <u>Learning outcomes</u>:
  - <a href="https://jupyter.org/" target="_blank">```Jupyter Notebooks```</a>
  - <a href="https://networkx.org/" target="_blank">```Graph Based Analysis```</a>
  - <a href="https://matplotlib.org/" target="_blank">```Matplotlib```</a>
  - <a href="https://seaborn.pydata.org/" target="_blank">```Seaborn```</a>
  - <a href="https://ggplot2.tidyverse.org/" target="_blank">```R ggplot2```</a>
8. <b>Machine/Deep Learning (a.k.a. ML/DL).</b>
<br/>
- <u>Learning outcomes</u>:
  - <a href="https://numpy.org/" target="_blank">```Numpy```</a>
  - <a href="https://pandas.pydata.org/" target="_blank">```Pandas```</a>
  - <a href="https://scikit-learn.org/stable/" target="_blank">```Scikit-Learn```</a>
  - <a href="https://www.tensorflow.org/" target="_blank">```Tensorflow```</a>
  - <a href="https://keras.io/" target="_blank">```Keras```</a>

Phew! That's plateful :smiley: 

![data-roles-venn](/assets/images/data-roles-venn.png)

People like to put labels but in my opinion, you learn the necessary to get the task done. In our case, learn, build, fix and repeat!!

Anyhow, to cover all this ground without losing sight, we better pick an interesting usecase that piques our interest. What's better than talking about <b>Movies</b> ....

Let's download <a href="https://datasets.imdbws.com/" target="_blank">IMDB Dataset</a> and explore a tad bit using the code below.

- Download dataset

  {% highlight bash %}
  wget https://datasets.imdbws.com/name.basics.tsv.gz -O actors.tsv.gz
  wget https://datasets.imdbws.com/title.basics.tsv.gz -O movies.tsv.gz
  {% endhighlight %}

- Unzip dataset

  {% highlight bash %}
  gunzip actors.tsv.gz
  gunzip movies.tsv.gz
  {% endhighlight %}

- Explore dataset

{% highlight python %}
#!/usr/bin/env python

"""
Fetch actors and their movie information from IMDB source files

Arguments

--movies [comma separated list of movies] #optional
--actors [comma spearated list of actors] #optional
--outfile #required

If both --movies and --actors are provided, returns full cast of the movies provided and full movies of actors privided.
"""

#-----------------------------
# @author: Mahesh Vangala
# @date: Jan, 29, 2022
# @license: <MIT + Apache 2.0>
#-----------------------------

import sys
import pandas as pd
import argparse

def parse_args():
  parser = argparse.ArgumentParser(description='Process IMDB info.')
  parser.add_argument(
    '-m'
    , '--movies'
    , required = False
    , help = "A comma separated list of movies to choose from."
  )
  parser.add_argument(
    '-o'
    , '--outfile'
    , required = True
    , help = "Path to output file."
  )
  parser.add_argument(
    '-a'
    , '--actors'
    , required = False
    , help = "A comma separated list of actors to choose from."
  )
  parser.add_argument(
    '--actor_file'
    , required = True
    , help = "Actors file <name.basics.tsv>"
  )
  parser.add_argument(
    '--movie_file'
    , required = True
    , help = "Movies file <title.basics.tsv>"
  )
  (args, rest_of_args) = parser.parse_known_args()
  return (args, rest_of_args)

def fetch_info(actor_info, movie_info):
  staging = actor_info.copy(deep = True)
  staging["title"] = actor_info["knownForTitles"].apply(lambda x: x.split(","))
  del staging["knownForTitles"]  
  staging = staging.explode("title")
  staging = staging.merge(
    movie_info[movie_info.tconst.isin(staging.title)]
    , how = "inner"
    , left_on = "title"
    , right_on = "tconst"
  )
  return staging

def get_cols_original():
  cols = ['nconst','primaryName','birthYear','deathYear','primaryProfession',
          'title','primaryTitle','isAdult','startYear','endYear','runtimeMinutes','genres']
  return cols

def get_cols_modified():
  cols = ['actorId','actorName','actorBirthYear','actorDeathYear','actorProfession',
          'movieId','movieName','isAdultMovie','movieStartYear','movieEndYear','movieRuntimeMinutes','movieGenres']
  return cols

def get_datasets():
  ACTOR_FILE = "assets/imdb_data/actors.tsv"
  MOVIE_FILE = "assets/imdb_data/movies.tsv"
  actor_info = pd.read_csv(ACTOR_FILE, sep = "\t", header = 0)
  movie_info = pd.read_csv(MOVIE_FILE, sep = "\t", header = 0)
  movie_info = movie_info[movie_info.titleType == 'movie']
  return {"actor": actor_info, "movie": movie_info} 

if __name__ == "__main__":
  (args, rest_of_args) = parse_args()
  actor_info = pd.read_csv(args.actor_file, sep = "\t", header = 0)
  movie_info = pd.read_csv(args.movie_file, sep = "\t", header = 0)
  movie_info = movie_info[movie_info.titleType == 'movie']
  results = pd.DataFrame()
  if args.actors:
    actors = args.actors.split(",")
    results = actor_info[actor_info.primaryName.isin(actors)]
    results = fetch_info(actor_info[actor_info.primaryName.isin(actors)], movie_info)
  if args.movies:
    movies = args.movies.split(",")
    movie_info = movie_info[movie_info.primaryTitle.isin(movies)]
    results = results.append(fetch_info(actor_info, movie_info))
  results = results[get_cols_original()]
  results.columns = get_cols_modified()
  results.drop_duplicates().to_csv(args.outfile, sep = ",", header = True, index = False)

{% endhighlight %}

Save this code to ```imdb_process.py``` and run as,

{% highlight python %}
python imdb_process.py                                        \
    --movies "Pulp Fiction,Reservoir Dogs"                    \
    --actors "Quentin Tarantino,Brad Pitt"                    \
    --movie_file movies.tsv                                   \
    --actor_file actors.tsv                                   \
    --outfile actors_movies_info.csv
{% endhighlight %}

The output looks like as showed below. (only few rows are showed ...)

![actors_info](/assets/images/actors.png)

Okay, we've got our baseline setup. We'll explore building a web-application in the upcoming <b><a href="https://vangalamaheshh.github.io/blog/data-engineering/big-data/full-stack/2022/02/02/big-data-proc-part-1.html" target="_blank">Part-1</a></b> of this series. Stay tuned!!

Happy Coding! :+1:

<a href="https://www.buymeacoffee.com/MaheshVangala" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/default-orange.png" alt="Buy Me A Coffee" height="41" width="174"></a>

<div id="share-bar">

    <h4 class="share-heading">Liked it? Please share the post.</h4>

    <div class="share-buttons">
        <a class="horizontal-share-buttons" target="_blank" href="https://www.facebook.com/sharer/sharer.php?u=https://vangalamaheshh.github.io{{page.url}}" 
            onclick="gtag('event', 'Facebook', {'event_category':'Post Shared','event_label':'Facebook'}); window.open(this.href, 'pop-up', 'left=20,top=20,width=500,height=500,toolbar=1,resizable=0'); return false;"
            title="Share on Facebook" >
            <span class="icon-facebook2">Facebook</span>
        </a>
        <a  class="horizontal-share-buttons" target="_blank" href="https://twitter.com/intent/tweet?text=Big Data Processing (FullStack & beyond) - Overview&url=https://vangalamaheshh.github.io{{page.url}}"
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


