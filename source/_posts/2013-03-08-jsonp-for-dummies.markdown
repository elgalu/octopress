---
layout: post
title: "JSONP for Dummies"
date: 2013-03-08 23:54
comments: true
categories: [jsonp, json, javascript]
published: true
---
If you read [wikipedia][JSONP wiki] definition:

> JSONP or "JSON with padding" is a communication technique used in JavaScript. It provides a method to request data from a server in a different domain, something prohibited by typical web browsers because of the same origin policy.

But let's dig it right away with an example. I will use [J50Npi][] standalone javascript library for that.

## Step by Step Beginner's Sample

Say you want to client-side print the user's Country IP using some external JSONP free service.

You can test that solution from your browser right now:

#### 1. Open your Browser

#### 2. Open a non javascript intensive page; e.g. [Roberto's blog](http://robertodecurnex.github.com/)

#### 3. Open the console by pressing F12 or whatever key your browser use. You may need to select tab 'Console' (tested on Chrome and Internet Explorer 10)

#### 4. Paste the below code (within the console)

```javascript

// This line taken from J50Npi.min.js (within this repo)
var J50Npi={currentScript:null,getJSON:function(b,d,h){var g=b+(b.indexOf("?")+1?"&":"?");var c=document.getElementsByTagName("head")[0];var a=document.createElement("script");var f=[];var e="";this.success=h;d.callback="J50Npi.success";for(e in d){f.push(e+"="+encodeURIComponent(d[e]))}g+=f.join("&");a.type="text/javascript";a.src=g;if(this.currentScript){c.removeChild(currentScript)}c.appendChild(a)},success:null};

// This is a WorldIP free geo-location database.
var url = "http://api.wipmania.com/jsonp";

// No specific data need to be sent there
var data = {};

// We need a function callback to be executed after the response is received
var callback = function(geodata){ alert(geodata.address.country); };

// And here is the magic:
J50Npi.getJSON(url, data, callback);

```

You should see an alert saying your current (ip based location) country name after half a second or so.

![J50Npi IMG](/images/J50Npi_alert_sample.png "Optional title")

[JSONP wiki]: http://en.wikipedia.org/wiki/JSONP
[J50Npi]: https://github.com/robertodecurnex/J50Npi
