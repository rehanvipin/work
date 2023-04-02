---
title: "/tmp"
weight: 20
# bookFlatSection: false
bookToc: false
---
# Temporary code access
Random stuff that doesn't fit anywhere else.

```py
import argparse
import os
from http.server import SimpleHTTPRequestHandler
from socketserver import TCPServer
from urllib.parse import urlparse

parser = argparse.ArgumentParser(description='Start a server.')
parser.add_argument('-p', '--port', type=int, default=3000)
parser.add_argument('-i', '--interface', type=str, default='0.0.0.0', help='Interface to bind to')
parser.add_argument('-b', '--base-dir', type=str, default='.')
args = parser.parse_args()

HOST = args.interface
PORT = args.port
BASE_DIR = args.base_dir

INDEXFILE = 'index.html'

if os.access(BASE_DIR, os.R_OK):
    os.chdir(BASE_DIR)
else:
    print('cannot find', BASE_DIR)
    exit(1)

class MyHandler(SimpleHTTPRequestHandler):
    def do_GET(self):

        # Parse query data to find out what was requested
        parsedParams = urlparse(self.path)

        # See if the file requested exists
        if os.access('.' + os.sep + parsedParams.path, os.R_OK):
        # File exists, serve it up
            SimpleHTTPRequestHandler.do_GET(self);
        else:
        # send index.html, but don't redirect
            self.send_response(200)
            self.send_header('Content-Type', 'text/html')  
            self.end_headers()
            with open(INDEXFILE, 'rb') as fin:
                self.copyfile(fin, self.wfile)

HOST_PORT = (HOST, PORT)
with TCPServer(HOST_PORT, MyHandler) as httpd:
    print("serving dir -", BASE_DIR, "- at port", HOST_PORT)
    httpd.serve_forever()
```