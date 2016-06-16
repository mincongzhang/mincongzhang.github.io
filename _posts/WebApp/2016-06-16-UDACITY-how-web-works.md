---
layout: post
title: Udacity - WebApp - How the web works?
category: WebApp
description: web
tags: ["web"]
---

--------------------------------------------------------

## HTTP requests

### Query Parameters (GET parameters)
1.Simple query
http://example.com/foo?P=1
query [name = value]

2.More
http://example.com/foo?P=1&Q=neat
2 queries [P = 1] [Q = neat]

### Fragments
1.Hash sign
http://example.com/foo#fragment
It's not sent to the server, it's purely exists in the browser

2.Query+Fragment
http://example.com/foo?P=1#fragment

### Port
http://localhost:8000/
By default the port = 80, include port when use diff port

### Get
1.Request line
http://www.example.com/foo
request line to the server: GET /foo HTTP/1.1
method  : GET
path    : /foo
version : HTTP/1.1
host    : www.example.com
connect to host, path is used for making the request

2.More
http://www.example.com/foo/logo.png?P=1#tricky
Request line: GET /foo.logo.png?P=1 HTTP/1.1

3.POST
Most often used for __sending data__ to the server

--------------------------------------------------------

## HTTP Responses

1.
Request  : GET /foo HTTP/1.1 (request line)
Response : HTTP/1.1 200 OK   (status line)
Status code  : 200
Reason phase : OK

2.Status Codes
200 OK           (2xx success)
302 Found        (3xx need to do sth to find this doc)
404 Not Found    (4xx error on the browser side)
500 Server Error (error on the server side)

3.Headers
Status line is followed by headers:
Date   : Tue Mar 2012 04:33:33 GMT
Server : Apache/2.2.3  (better make this up, dont send this info to user)
Content-Type   : text/html
Content-Length : 1539

4.Play with telnet
(1)try "telnet www.example.com 80"
(2) enter the GET and Host, then enter twice to get data
GET / HTTP/1.0
Host: www.example.com

--------------------------------------------------------

## Servers

1.Purpose of a server
Respond to HTTP requests

2.2 classifications for the type of responses
(1)Static  (Pre-written file to return, e.g. image)
(2)Dynamic (Made on the fly by web applications)
