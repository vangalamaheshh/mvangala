---
layout: post
title:  "Big-Data-Processing (FullStack & beyond) Part-3"
date:   2022-03-03
categories: Blog Data-Engineering Big-Data Full-Stack  
---

## Web Sockets & Kafka Streams

In <a href="https://vangalamaheshh.github.io/blog/data-engineering/big-data/full-stack/2022/02/04/big-data-proc-part-2.html" target="_blank">Part-2</a>, we have seen our web app fetching data from graph database via an API layer. Now, users can get the movies info of their favorite actors or the cast of their favorite movies using the web form with out much latency.

In this post, we will explore streaming these results back to the server and in the process get our hands dirty with ```Web Sockets``` and ```Kafka Streams```. Following video demonstrates the learning outcomes of this post with respect to ```Sockets & Streams```.

<div style="padding: 10px;">
    <iframe width="560" height="315" src="https://www.youtube.com/embed/T0kHHqInwxs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

As far as my experience goes working with ```Web Sockets``` in multiple languages - ```Python```, ```Rust``` and ```NodeJs```, ```NodeJs``` websocket library - <a href="https://www.npmjs.com/package/ws" target="_blank">```ws```</a> - is more amicable to work with especially in seamless integration with web client frameworks such as ```Angular```. And, thanks to ```Microservices Based Architecture``` with ```Docker```, it is ever so easy to glue together systems of different languages cohesively so that we are using best of the benefits programming language ecosystem has to offer.

With that being said, let's add ```stream``` functionality using ```web sockets``` to our UI codebase. 

```services/ws.service.ts```
{% highlight javascript %}
import { Injectable, Inject } from '@angular/core';
import { Observable, of } from  'rxjs';
import {map} from 'rxjs/operators';

import { webSocket, WebSocketSubject } from 'rxjs/webSocket';
import { catchError, tap, switchAll } from 'rxjs/operators';
import { EMPTY, Subject } from 'rxjs';
export const WS_ENDPOINT = 'ws://localhost:8443/';

@Injectable({
    providedIn: 'root'
})
export class WsService {
    private socket$: WebSocketSubject<any>;
    private socketClosed: boolean = false;
    private messagesSubject$: any = new Subject();
    public messages$ = this.messagesSubject$.pipe(
        switchAll(), catchError(e => { throw e })
    );

    constructor() { }

    public connect(): void {
  
        if (!this.socket$ || this.socket$.closed || this.socketClosed) {
          this.socket$ = this.getNewWebSocket();
          this.socket$.subscribe(
            msg => {
              this.messagesSubject$.next(of(msg))
            }, // when there is a message from the server.
            err => console.log(err), // when there's an error from WebSocket API.
            () => {
              console.log('complete');
              this.socketClosed = true;
            } // when connection is closed.
          );
        }
      }
      
      private getNewWebSocket() {
        return webSocket(WS_ENDPOINT);
      }
    
      sendMessage(msg: any): Observable<any> {
        return of(this.socket$.next(msg));
      }
    
      close() {
        this.socket$.complete(); 
      }
}
{% endhighlight %}

```app.component.html```
{% highlight javascript %}
...
<div class="stream-container" *ngIf="loading || data.length > 0">
  <button mat-raised-button class="stream-btn" (click)="stream();">Stream</button> 
</div>
<div *ngIf="notifications.length > 0" class="stream-container">
  <ng-container *ngFor="let notification of notifications;">
    <p>{{notification}}</p>
  </ng-container>
</div>
{% endhighlight %}

```app.component.scss```
{% highlight css %}
.stream-container {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    padding: 10px;
}

.stream-btn {
    padding: 5px;
    size: 2rem;
}
{% endhighlight %}

```app.component.ts```
{% highlight javascript %}
// ...
import { WsService } from './services/ws.service';
// ...
public loading: boolean = false;
public notifications: string[] = [];

constructor(
    private wsService: WsService
    , private movieService: MovieService  
) { }

// ...
public stream() {
    this.wsService.connect();
    this.data.forEach((m, i) => {
      this.wsService.sendMessage(m);
      if (i === this.data.length - 1) {
        this.notifications.push("Messages were streamed successfully.");
      }
    });
}
{% endhighlight %}

It's time to fire up ```NodeJs``` container and serve our server side application that captures the stream events from our client and publishes the events in turn onto ```Kafka``` topic.

{% highlight bash %}
docker run --name web-sock-nodejs --rm -it -p 8443:8443 node:latest bash
{% endhighlight %}

Once on the container, 

{% highlight bash %}
mkdir /mnt/web-sock-app && cd $_
npm init
npm install --save express kafkajs ws
{% endhighlight %}

Add the following content to ```/mnt/web-sock-app/index.js```.

{% highlight javascript %}
'use strict';
const EXPRESS = require('express');
const HTTP = require('http');
const WebSocket = require('ws');
const { Kafka } = require('kafkajs');

const PORT = 8443;
const HOST = '0.0.0.0';

const kafka = new Kafka({
  clientId: 'web-sock-app',
  brokers: ['kafka:9092']
});

const producer = kafka.producer();
const topic = 'test-topic';

const sendMessage = (msg) => {
  return producer
    .send({
      topic,
      messages: Array({
        value: msg
      }),
    })
    .then(console.log)
    .catch(e => console.error(`[example/producer] ${e.message}`, e))
}

const stream = async (msg) => {
  await producer.connect();
  sendMessage(msg);
}

var app = EXPRESS();
const server = HTTP.createServer(app);
const wss = new WebSocket.Server({noServer: true});

server.on('upgrade', function (request, socket, head) {
  wss.handleUpgrade(request, socket, head, function (ws) {
      wss.emit('connection', ws, request);
  });
});

wss.on('connection', function (ws, request) {
  ws.on('message', function (message) {
    console.log(`Received message ${message}`);
    stream(message).catch(e => console.error(`[example/producer] ${e.message}`, e));
  });
  ws.on('close', function () {
    console.log("closing websocket");
  });
});

server.listen(PORT, HOST, () => console.log('app listening on port 8443!'));
{% endhighlight %}

Run the application using,

```node index.js```

<b><u>Disclaimer</u></b>: <br/>
In a production app, it's recommended to use key-value based messages onto kafka topic. In doing so, with proper hash function, identical message will always be sent to the same partition of the kafka topic. In addition, it's best advised to use <a href="https://avro.apache.org/docs/current/gettingstartedpython.html" target="_blank">```Apache Avro```</a> format when serializing the messages onto Kafka Topic.

If you don't have ```Kafka Cluster``` set up, please refer to <a href="https://vangalamaheshh.github.io/blog/data-engineering/big-data/apache-kafka/2022/01/06/apache-kafka-101.html" target="_blank">this post</a>. Go ahead and create ```test-topic``` and you should see the messages getting streamed onto the consumer as you play with the IMDB form on our web-ui.

![big-data-part3](/assets/images/big-data-part3.png)

Happy Coding, or better to say, Happy Streaming! :+1:

<a href="https://www.buymeacoffee.com/MaheshVangala" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/default-orange.png" alt="Buy Me A Coffee" height="41" width="174"></a>

<div id="share-bar">

    <h4 class="share-heading">Liked it? Please share the post.</h4>

    <div class="share-buttons">
        <a class="horizontal-share-buttons" target="_blank" href="https://www.facebook.com/sharer/sharer.php?u=https://vangalamaheshh.github.io{{page.url}}" 
            onclick="gtag('event', 'Facebook', {'event_category':'Post Shared','event_label':'Facebook'}); window.open(this.href, 'pop-up', 'left=20,top=20,width=500,height=500,toolbar=1,resizable=0'); return false;"
            title="Share on Facebook" >
            <span class="icon-facebook2">Facebook</span>
        </a>
        <a  class="horizontal-share-buttons" target="_blank" href="https://twitter.com/intent/tweet?text=Big Data Processing (FullStack and beyond) Part-3&url=https://vangalamaheshh.github.io{{page.url}}"
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


