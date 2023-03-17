---
title: "/tmp"
weight: 20
# bookFlatSection: false
bookToc: false
---
# Temporary code access
Random stuff that doesn't fit anywhere else.

```py
import os
import http.server
import socketserver
import sys
from urllib.parse import urlparse

HOST = "0.0.0.0"
PORT = 3000
if len(sys.argv) > 2:
    PORT = int(sys.argv[1])
    HOST = sys.argv[2]

INDEXFILE = 'index.html'

class MyHandler(http.server.SimpleHTTPRequestHandler):
   def do_GET(self):

    # Parse query data to find out what was requested
    parsedParams = urlparse(self.path)

     # See if the file requested exists
    if os.access('.' + os.sep + parsedParams.path, os.R_OK):
        # File exists, serve it up
        http.server.SimpleHTTPRequestHandler.do_GET(self);
    else:
        # send index.html, but don't redirect
        self.send_response(200)
        self.send_header('Content-Type', 'text/html')  
        self.end_headers()
        with open(INDEXFILE, 'rb') as fin:
          self.copyfile(fin, self.wfile)

Handler = MyHandler
HOST_PORT = (HOST, PORT)
with socketserver.TCPServer(HOST_PORT, Handler) as httpd:
    print("serving at port", HOST_PORT)
    httpd.serve_forever()
```