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

### POST
Most often used for sending data to the server
